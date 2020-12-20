---

layout: post
title: "SpringBatch Testcase 예제 살펴보기"
date: 2020-01-19 15:51:02 +0900
tags: [spring batch]
categories: spring batch 
subtitle : 
feature-img: "assets/img/banner.jpg"
---

### "SpringBatch Testcase"
---

스프링 배치로 프로젝트를 구성할 때 테스트 케이스는 필수라고 생각된다. 일괄 처리라는 배치의 특성 상 잘못된 배치가 수행 되었을 경우 그에따라 미칠 파장은 클 것이다. 실제로 수십만건의 메일을 발송해야 하는 배치 프로젝트를 구성하다가 db update 시 값이 잘못되어 중간에 배치를 중지해야 하는 일이 있었다. 오류를 발견한 후에는 이미 이만건정도의 메일이  발송된 후였다. Chunk size 가 10000이였기 때문에 이미 두번정도의 chunk 가 수행된 후였다. 

<!-- more -->

이처럼 엄청난 파급력을 가지는 Batch를 수행시 테스트 케이스의 작성은 필수적인데,  평소에 단순히 기존의 테스트 케이스를 가져와 변경해서 사용하다가 수행 원리와 작동 원리를 좀더 알아보는 기회가 생겨 포스팅을 작성하게 되었다.

아래는 코드는 두 Double value 를 단순히 더하는 기능을 수행하는 잡과 해당 잡을 테스트 하는 아주 간단한 테스트 케이스 로 이루어져 있다. 각 어노테이션의 기능과 코드의 의미를 주석으로 설명하였으며, 밑의 코드는 출처에 추가한 링크 포스팅을 기반으로 정리하여 실행가능한 아주 간단한 테스트 케이스를 만든 것에 불과하다. 자세한 작동원리와 이론은 링크된 블로그에 구체적으로 설명되어 있다. 

코드를 살펴보자.

<br>

### 코드 살펴보기

#### 계산기 Job

```java
@Slf4j // log 사용을 위한 lombok 어노테이션
@RequiredArgsConstructor // 생성자 DI를 위한 lombok 어노테이션
@Configuration
public class CalculatorJobConfiguration {

    private final JobBuilderFactory jobBuilderFactory;
    private final StepBuilderFactory stepBuilderFactory;

    @Bean
    public Job calculatorJob(){
        return jobBuilderFactory.get("calculatorJob")
                .start(calculatorStep(null, null))
                .build();
    }

    @Bean
    @JobScope
    public Step calculatorStep(@Value("#{jobParameters[value1]}") Double value1 , @Value("#{jobParameters[value2]}") Double value2){
        return stepBuilderFactory.get("calculatorStep")
                .tasklet((contribution, chunkContext) -> {
                    log.info(">>>>> calculatorStep");
                    log.info("value1 : {} value2 : {}", value1, value2);
                    log.info("value3 : {}", value1 + value2);  // 단순히 두개의 파라미터를 더하여 출력

                    return RepeatStatus.FINISHED;
                }).build();
    }
}
```

<br>

#### 테스트 케이스

```java
@RunWith(SpringRunner.class)
// JUnit 프레임워크에서 내장된 Runner 실행 시 SpringRunner.class라는 확장된 클래스를 실행
@SpringBatchTest
// JobLauncherTestUtils를 사용하기 위해 Spring Batch 4.1 버전에 새롭게 추가된 어노테이션
@SpringBootTest(classes={CalculatorJobConfiguration.class, TestBatchConfig.class})
// 통합 테스트 실행시 사용할 Java 설정
public class CalculatorJobTest {

    // Batch Job을 테스트 환경에서 실행할 Utils 클래스
    @Autowired
    private JobLauncherTestUtils jobLauncherTestUtils;

    @Test
    public void 계산기테스트() throws Exception {

        JobParameters jobParameters = new JobParametersBuilder()
                .addString("version", LocalDateTime.now().toString())
                .addDouble("value1", 2.0)
                .addDouble("value2", 3.4)
                .toJobParameters();

        JobExecution jobExecution = jobLauncherTestUtils.launchJob(jobParameters);
        // JobParameter와 함께 Job을 실행
        // 해당 Job의 결과는 JobExecution에 담겨 반환
        assertThat(jobExecution.getStatus()).isEqualTo(BatchStatus.COMPLETED);
        // 성공적으로 배치가 수행되었는지 검증
    }
}
```

```java
@Configuration
// @Compoent 어노테이션이 붙어있는 class를 빈으로 등록
// ex ) @Compoent, @Configuration, @Repository, @Service, @Controller, @RestController
@EnableAutoConfiguration
// 스프링부트의 meta 파일을 읽어 미리 정의되어 있는 자바 설정 파일(@Configuration)들을 빈으로 등록하는 역할
@EnableBatchProcessing// 배치 환경 설정
class TestBatchConfig {
}
```



##### 출처 <br/>
[https://jojoldu.tistory.com/455](https://jojoldu.tistory.com/455)

[https://cornswrold.tistory.com/314](https://cornswrold.tistory.com/314)

