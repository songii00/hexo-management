---
layout: post
title:  "함수형 프로그래밍"
date:   2019-08-29 17:48:45 +0900
tags: [CleanArchitecture]
categories: clean-architecture
subtitle : Clean Architecture - Item 6
feature-img: "assets/img/banner.jpg"
---
### 함수형 프로그래밍
함수형 프로그래밍은 자료 처리를 수학적 함수의 계산으로 취급하고 상태와 가변 데이터를 멀리하는 프로그래밍 패러다임의 하나이다.
> 함수형 언어에서는 변수는 변경되지 않는다. 

이것은 함수형 프로그래밍을 관통하는 중요한 법이다.

<br>

<!-- more -->

#### 불변성과 아키텍처

아키텍트는 항상 가변성을 염려해야 하는데 그 이유는, 
경합(race)조건, 교착상태(deadlock), 동시 업데이트(concurrent update)문제가 모두 가변 변수에 의해 발생하기 때문이다.

- 경합(race)조건 : 공유 자원에 대해 여러 개의 프로세스가 동시에 접근하기 위해 경쟁하는 상태로 둘 이상의 입력이나 조작이 동시에 일어나 의도하지 않은 결과를 가져오는 경우.

![factory method pattern](/assets/images/post/191012/(8).png) 

###### <center> [경합조건] 올바른 동작 플로우 </center>

![factory method pattern](/assets/images/post/191012/(9).png) 

###### <center> [경합조건] 비 정상적 동작 플로우 </center>

<br>

- 교착상태(deadlock) : 프로세스가 자원을 얻지 못해 다음 처리를 하지 못하는 상태로, 시스템의 한정적 자원을 여러 곳에서 사용하려고 할 때 발생.

![factory method pattern](/assets/images/post/191012/(7).png) 

다수의 스레드와 프로세스를 사용하는 애플리케이션에서 마주치는 모든 문제는 가변 변수가 없다면 절대로 생기지않는다.
따라서 아키텍트라면 동시성(concurrency)문제에 지대한 관심을 가져야 한다.

다만 자원이 무한대가 아니기때문에 불변성을 실현하는데 있어 일종의 타협이 필요하다.

<br>

#### 가변성의 분리

![factory method pattern](/assets/images/post/191012/(27).png){: width="80%" height="80%"}

애플리케이션을 제대로 구조화 하려면 변수를 변경하는 컴포넌트와 변경하지 않는 컴포넌트를 분리해야 한다. 동시에 가변 변수들을 보호하는 적절한 수단을 동원해야 한다.

불변 컴포넌트에서는 순수하게 함수형 방식으로만 작업이 처리되며, 어떤 가변 변수도 사용되지 않아야 한다. 

상태변경은 컴포넌트를 갖가지 동시성 문제에 노출하는 꼴이므로, 흔히 트랜잭션 메모리와 같은 실천법을 사용하여 동시업데이트와 경합 조건 문제로부터 가변 변수를 보호한다.  

따라서 가능한 많은 처리를 불변 컴포넌트로 옮겨야 하고, 가변 컴포넌트에는 가능한 한 많은 코드를 빼내야 한다.




##### 트랜잭션 메모리 더 알아보기 > [https://ko.wikipedia.org/wiki/....][link02] <br/>
[link02]: https://ko.wikipedia.org/wiki/%EC%86%8C%ED%94%84%ED%8A%B8%EC%9B%A8%EC%96%B4_%ED%8A%B8%EB%9E%9C%EC%9E%AD%EC%85%94%EB%84%90_%EB%A9%94%EB%AA%A8%EB%A6%AC

<br>


#### 이벤트 소싱

이벤트 소싱이란 상태가 아닌 트랜잭션을 저장하자는 전략이다. 이벤트 소싱의 동작방법은 상태가 필요해지면 단순히 상태의 시작점부터 모든 트랜잭션을 처리하는 것이다. 

이해를 돕기 위해 트랜잭션을 이벤트라고 생각하며 다음의 예를 살펴보자.

![factory method pattern](/assets/images/post/191012/(28).png)

사용자가 책을 주문할 때 기존 데이터 저장 방식이라면 최종적으로 2번 책만 저장하게 된다.

![factory method pattern](/assets/images/post/191012/(29).png)

같은 상황에서 이벤트 소싱은 이벤트 자체를 데이터로 저장하게 된다. “사용자가 1번 책을 추가하여 주문을 생성한다.”, “2번 책을 주문에 추가한다”, “1번 책을 주문에서 삭제한다.” 라는 이벤트 자체가 저장되는 방식이다.

따라서 데이터 저장소에서 삭제되거나 변경되는 것이 하나도 없다. CRUD가 아니라 그저 CR만 수행하기 때문이다. 데이터 저장소의 변경과 삭제가 없어 동시 업데이트 문제로부터 자유로워 진다.

이 전략에서는 가변변수는 하나도 필요가 없다.

저장공간과 처리능력이 충분하다면 애플리케이션이 완전한 불변성을 갖도록 만들수 있고, 완전한 함수형으로 만들 수 있다.

- 대표적인 예로 소스 코드 버전 관리 시스템

##### 이벤트 소싱 더 알아보기 > [https://medium.com/@mjspring....][link03] <br/>
[link03]: https://medium.com/@mjspring/%EC%9D%B4%EB%B2%A4%ED%8A%B8-%EC%86%8C%EC%8B%B1-event-sourcing-%EA%B0%9C%EB%85%90-50029f50f78c


<br>

#### 결론

> 함수형 프로그래밍은 변수 할당에 부과되는 규율이다.

> "소프트웨어, 즉 컴퓨터 프로그렘은 순차, 분기, 반복, 참조로 구성된다. 그이상도 그 이하도 아니다."

> 프로그래밍 패러다임은 우리가 해서는 안되는 것에 대한 것이다.

<br>

###### 출처 <br/>

###### [https://hacks.mozilla.org/2017/06/avoiding-race-conditions-in-sharedarraybuffers-with-atomics/][link01] <br/>
[link01]: https://hacks.mozilla.org/2017/06/avoiding-race-conditions-in-sharedarraybuffers-with-atomics/


