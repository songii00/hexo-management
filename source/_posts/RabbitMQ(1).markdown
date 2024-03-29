---
layout: post
title:  "RabbitMQ (1)"
date:   2019-08-29 17:48:45 +0900
tags: [MessageQueue]
categories: MessageQueue
subtitle : RabbitMQ 란
---

### 1. RabbitMQ 란

![factory method pattern](1.png)

#### MON(메시지 지향 미들웨어)
메시지 지향 미들웨어(Meesage Oriented Middleware: MOM)은 비동기 메시지를 사용하는 다른 응용 프로그램 사이에서 데이터 송수신을 의미한다.

#### MQ
MOM을 구현한 시스템이 메시지 큐(MessageQueue: MQ)이다. 
프로그래밍에서 MQ는 프로세스 또는 프로그램 인스턴스가 데이터를 서로 교환할때 사용하는 방법을 말하는데,
이때 데이터를 교환할 때 시스템이 관리하는 메시지 큐를 이용한다.
                                         
서로 다른 프로세스나 프로그램 사이에 메시지를 교환할 때 AMQP(Advanced Message Queuing Protocol)을 이용하게 된다.
                                      
#### AMQP                  
AMQP(Advanced Message Queuing Protocol)란 MQ를 오픈 소스에 기반한 표준 프로토콜이다. 
AMQP 자체가 프로토콜을 의미하기 때문에 이 프로토콜을 구현한 MQ 기술은 여러가지가 있으며 그 중 하나가 RabbitMQ 이다.
RabbitMQ 이외에도 ActiveMQ, ZeroMQ, Kafka 등이 있다.

#### RabbitMQ 란 

한마디로 RabbitMQ란 
Rabbit Message Queue 의 약자로 AMQP (Advanced Message Queueing Protocol)를 구현한 메세지 브로커 소프트웨어(message broker software) 오픈소스이다. 
메시지를 전달 받아 Consumer에게 라우트하는 것이 주된 역할이다.

### 2. 장점 

#### 메세지 큐의 장점

Message Queueing은 대용량 데이터를 처리하기 위한 배치 작업이나, 채팅 서비스, 비동기 데이터를 처리할 때 사용한다.
프로세스 단위로 처리하는 웹 요청이나 일반적인 프로그램을 만들어서 사용하는데 
사용자가 많아지거나 데이터가 많아지면 요청에 대한 응답을 기다리는 수가 증가하다가 나중에는 대기 시간이 지연되어서 병목현상이 생기거나, 
서비스가 정상적으로 되지 못하는 상황이 발생한다.

이때 기존에 분산되어 있던 데이터 처리를 한 곳에 집중하면서 하나의 미들웨어로써 메세지 브로커를 두어서 필요한 프로그램에 작업을 분산시키는 방법을 사용하게 된다.

![factory method pattern](2.png) 

이해를 돕기 위해 잠시 RabbitMQ의 간단한 work flow를 살펴보자. 

간단하게 용어를 살펴보자면 
Producer란 메시지를 보내는 주체, Broker는 메시지를 Consumer에게 전달하는 미들웨어, Consumer는 메시지를 받아 소비하는 주체이다.
Producer가 Message를 Queue에 넣어두면, Consumer가 Message를 가져와 처리하게 된다.

이처럼 메세지 큐를 사용하게 되면 다음과 같은 장점이 있다.

- 비동기(Asynchronous) : Queue에 넣기 때문에 나중에 처리가능. 다른 API에게 위임함으로써 Request에 대해 빠르게 응답.
- 비동조(Decoupling) : 애츨리케이션과 분리 가능. 결합도 낮춤.
- 탄력성(Resilience) : 일부가 실패 시 전체에 영향을 받지 않음
- 과잉(Redundancy) : 실패할 경우 재실행 가능
- 보증(Guarantees) : 작업이 처리된걸 확인 가능
- 확장성(Scalable) : 다수의 프로세스들이 큐에 메시지 전송 가능

#### RabbitMQ 장점 

- 신뢰성, 안정성과 성능을 충족할 수 있도록 다양한 기능을 제공
- 유연한 라우팅 : Message Queue가 도착하기 전에 라우팅 되며 플러그인을 통해 더 복잡한 라우팅도 가능
- 클러스터링 : 로컬네트워크에 있는 여러 RabbitMQ 서버를 논리적으로 클러스터링할 수 있고 논리적인 브로커도 가능
- 관리 UI가 있어 편하게 관리 가능
- 거의 모든 언어와 운영체제를 지원 
- 오픈소스로 상업적 지원이 가능

### 3. RabbitMQ WorkFlow

![factory method pattern](3.png) 

(1) 사용자가 PDF를 생성하기를 요청한다.
(2) Producer 에게 요청이 전송되고 Producer Exchange 에게 요청 보낸다.
(3) Producer 가 보낸 메세지는 Queue에 직접 전달되지 않고 Exchange 로 전달된다. 

Exchange는 Producer가 전달한 메시지를 Routing Key를 사용하여 적절한 Queue에 전달하는 역할(Routing)을 수행하며, 
Routing은 Exchange Type에 따라 전략이 바뀌게 된다.

Exchange type으로 어떻게 메세지를 전달할 것인지 동작방식을 선택할 수 있는데,
Exchange를 생성할때 Exchange의 Type을 정해야 한다.

Message는 Consumer가 소비할때까지 Queue에 대기한다.

![factory method pattern](5.png) 

(4) Consumer가 메세지를 소비한다.

###### 출처
> https://12bme.tistory.com/176
> https://ram2ram2.tistory.com/3
> https://m.blog.naver.com/tmondev/221051503100
> https://skibis.tistory.com/310
> https://nesoy.github.io/articles/2019-02/RabbitMQ

