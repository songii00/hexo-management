---
layout: post
title:  "spring batch - 버그해결기(chunk)"
date:   2019-06-10 20:35:10 +0900
tags: [spring batch,bug]
subtitle : chunk 무한지옥 해결법
categories: spring batch
feature-img: "assets/img/banner.jpg"
---

### 문제상황
Spring Batch를 이용해 대량의 데이터 전송 로직을 구성중 대량의 데이터를 페이징 처리 후 chunk를 반복하며 전송해야 하는 상황이 생겼다.   

이때 구성 흐름은 reader에서 데이터를 읽고 writer에서 FeignClient를 이용해 전송한후, StepExecutionListener를 통해 로깅 처리를 한다. 그 후 다시 reader를 통해 다시 다음 인덱스의 데이터를 읽고, writer로 전송한다. 읽어올 데이터가 없을 때까지 해당 chunk를 반복한 후 데이터가 없을때 다음 스텝을 진행한다. 

문제는 reader->writer를 반복 후 더 이상 읽어올 데이터가 없으면 listener가 실행될 거라고 생각했지만, 현실은 chunk의 무한반복 문제가 발생했다는 것이다. 코드를 보면서 살펴보자.

#### 코드예시

Reader
```java
@Override
public KopisRequest read() {
log.info(">>>>>>>>>>> Item Read");

// 응답 객체 생체
ResponsePram responseParam = new ResponsePram();

// 페이징 처리 데이터
// startSearchIndex 부터 1000개의 데이터를 읽어
List<ResponseDetail> responseDetailList = DetailService.getDetailList(
        new ResponseDetail.Search(startSearchIndex, 1000));
        
increaseSearchIndex(responseDetailList.size()); // startSearchIndex를 읽어온 데이터 수만큼 증가
responseParam.setItems(responseDetailList); // 반환 객체에 저장

return responseParam; // 객체 반환
}
```


Reader는 ItemReader와 ItemStream을 구현했다. 

read 메서드가 실행되면 페이징 처리된 데이터인 ResponseDetail 객체 정보를 startSearchIndex(시작 인덱스)부터 1000개 읽어온다. 
예를 들어 startSearchIndex가 1이라면 1부터 1000개의 데이터를 읽어오는 쿼리를 실행한다. 그후, startSearchIndex 를 1000개 증가시켜 1001로 설정한다. 마지막으로 responseParam 에 읽어온 ResponseDetailList를 저장 후 반환한다. 

writer는 예제 코드는 없지만 간단히 reader에서 읽어온 데이터를 전송하는 역할만 수행한다고 하자. 

위 step이 실행된다면 어떻게 될까. 결과적으로만 말하면 read->write->read->write의 무한 반복이다. 위의 코드가 실행되는 순간 이미 chunk 무한지옥의 늪에 빠졌다고 할수있다. 

도대체 왜 chunk는 끝나질 않는걸까?

### 이유
답은 ChunkOrientedTasklet에 있다. ChunkOrientedTasklet는 chunk단위로 작업하기 위한 전체 코드가 위치한 곳이다. 

```java
public class ChunkOrientedTasklet<I> implements Tasklet {
    private static final String INPUTS_KEY = "INPUTS";
    private final ChunkProcessor<I> chunkProcessor;
    private final ChunkProvider<I> chunkProvider;
    private boolean buffering = true;
    private static Log logger = LogFactory.getLog(ChunkOrientedTasklet.class);

//...

public RepeatStatus execute(StepContribution contribution, ChunkContext chunkContext) throws Exception {
        Chunk<I> inputs = (Chunk)chunkContext.getAttribute("INPUTS");
        if (inputs == null) {
            inputs = this.chunkProvider.provide(contribution);
            if (this.buffering) {
                chunkContext.setAttribute("INPUTS", inputs);
            }
        }

        this.chunkProcessor.process(contribution, inputs);
        this.chunkProvider.postProcess(contribution, inputs);
        if (inputs.isBusy()) {
            logger.debug("Inputs still busy");
            return RepeatStatus.CONTINUABLE;
        } else {
            chunkContext.removeAttribute("INPUTS");
            chunkContext.setComplete();
            if (logger.isDebugEnabled()) {
                logger.debug("Inputs not busy, ended: " + inputs.isEnd());
            }
    
            return RepeatStatus.continueIf(!inputs.isEnd());
        }
    }
```

여기서 chunkProvider.provide()로 Reader에서 Chunk size만큼 데이터를 가져온다. 그 후, chunkProcessor.process() 에서 Reader에서 받은 데이터 Processor, Writer를 거치게 된다. 

아래의 코드는 read를 담당하는 provide 메서드이다.

```java
public Chunk<I> provide(final StepContribution contribution) throws Exception {
        final Chunk<I> inputs = new Chunk();
        this.repeatOperations.iterate(new RepeatCallback() {
            public RepeatStatus doInIteration(RepeatContext context) throws Exception {
                Object item = null;

                try {
                    item = SimpleChunkProvider.this.read(contribution, inputs);
                } catch (SkipOverflowException var4) {
                    return RepeatStatus.FINISHED;
                }
    
                if (item == null) {
                    inputs.setEnd();
                    return RepeatStatus.FINISHED;
                } else {
                    inputs.add(item);
                    contribution.incrementReadCount();
                    return RepeatStatus.CONTINUABLE;
                }
            }
        });
        return inputs;
    }
```

위 코드는 inputs이 ChunkSize만큼 쌓일때까지 read()를 호출한다. 이 read() 는 내부를 보면 실제로는 ItemReader.read를 호출하게 된다. 

여기서 중요한 부분은 read를 통해 읽어온 데이터가 null이 되지 않으면 RepeatStatus.CONTINUABLE 을 리턴한다는 것이다. RepeatStatus.CONTINUABLE 를 반환하면 해당 chunk는 다시 반복되게 되는데 이때문에 위 예제의 Reader 에서는 chunk가 끝나지 않는 것이다.

#### 개선코드 예시

```java

@Override
public KopisRequest read() {
    log.info(">>>>>>>>>>> Item Read");
    
    // 응답객체 생성 
    ResponsePram responseParam =new ResponsePram();
    
    // 페이징 처리 데이터
    // startSearchIndex 부터 1000개의 데이터를 읽어온다.
    List<ResponseDetail> responseDetailList = DetailService.getDetailList(
            new ResponseDetail.Search(startSearchIndex, 1000));
     
    // 만약 읽어온 리스트가 비어있다면 null을 반
    if (CollectionUtils.isEmpty(responseDetailList)) {
        return null;
    }
    
    increaseSearchIndex(responseDetailList.size()); // startSearchIndex를 1000만큼 증가시킨다.
    responseParam.setItems(responseDetailList); // 반환 객체에 저장
     
    return responseParam; // 객체 반환
}

```

만약 조회한 리스트가 없다면 null을 반환하는 로직을 추가하는 것만으로 무한반복 지옥에서 탈출할 수 있다.

Tasklet이 아니라 chunk를 사용해야 할때, ItemReader를 구현해서 사용해야 할때, 위 상황을 명심하자. 또한 혹시 더좋은  방법이 있다면 블로그의 댓글기능이 아직 구현되있지 않으므로 Contact의 메세지를 부탁드리며 이상으로 버그 해결기는 마치도록 하겠다.

