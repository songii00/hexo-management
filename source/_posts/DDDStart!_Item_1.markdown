---
layout: post
title: "도메인 모델 시작"
date: 2020-06-14:17:28:38 +0900
tags: [DDDStart!]
categories: ddd start
---

### "DDD Start! 1장"

---

### 도메인

- 소프트웨어로 해결하고자 하는 문제 영역
- 도메인은 여러 하위 도메인으로 구성 

<!-- more -->
![](1.png) 

- 하위 도메인은 다른 하위 도메인들과 연동하여 완전한 기능을 제공 
- 소프트웨어가 도메인의 모든 기능을 제공하지 않음 

![](2.png) 

- 하위 도메인을 어떻게 구성할지 여부는 상황에 따라 달라짐 

### 도메인 모델 

- 특정 도메인을 개념적으로 표현한 것 
- 도메인을 이해하는데 도움된다면 표현방식은 중요하지 않음 
- 도메인 자체를 이해하기 위한 개념 모델 
- 구현 모델이 필요함 

![](3.jpg) 

> 도메인 모델을 사용하면 여러 관계자들이 동일한 모습으로 도메인을 이해하고 도메인 지식을 공유하는데 도움이 된다. 


### 도메인 모델 패턴 

- 일반적인 애플리케이션의 아키텍처는 네개의 계층으로 구성 

![](4.png)

- 도메인 모델 
  - 아키텍쳐상의 도메인 계층을 객체 지향 기법으로 구현하는 패턴 
  - 도메인 계층을 구현할 때 사용하는 객체 모델 
- 도메인 모델 패턴 
  - 도메인 규칙을 객체지향 기법으로 구현하는 패턴 
- 도메인 규칙 
  - 도메인의 핵심 규칙 
  - ex ) 주문 취소는 배송 전에만 할수있다. / 출고 전 배송지 변경할 수있다. 
  - 핵심 규칙을 구현한 코드는 도메인 모델에만 위치

### 도메인 모델 도출 

- 도메인에 대한 초기 모델 
- 요구사항에서 출발 

> 단순히 코드를 보기 좋게 작성하는 것 뿐만 아니라 도메인 관점에서 코드가 도메인을 잘 표현해야 비로소 코드의 가독성이 높아지며 문서로서 코드가 의미를 갖는다. 

### 엔티티와 밸류 

- 요구사항에서 도출한 도메인은 크게 엔티티와 밸류로 구분 

#### 엔티티

![](5.png)

- 고유하고 변하지 않는 식별자를 가짐

#### 밸류 타입 

- 개념적으로 완전한 하나 

![](6.png) 

```java
public class ShippingInfo {
  private Receiver receiver;
  private Address addredss;
  .. 생성자, getter ... 
}
```

```java
// 데이터 변경기능을 제공하지 않는 불변 타입 
// 안전한 코드 작성 가능 
public class Money {
  private int value;
  public Money(int value){
    this.value = value;
  }
  public int getValue(){
    return this.value;
  }
}
```

- 두 밸류가 같으려면 모든 속성이 같아야 함

#### 엔티티 식별자와 밸류 타입 

```java
public class Order {
  // OrderNo 타입 자체로 id가 주문번호임을 알 수 있다. 
  private OrderNo id;
  ... 
  public OrderNo getId(){
    return id;
  }
}
```

#### 도메인 모델에 set 메서드 넣지 않기

- set 메서드는 단순히 상태값만 변경할지, 상태값에 따라 다른 처리를 위한 코드를 함께 구현할지 애매함
- 도메인 객체를 생성할 때 완전한 상태가 아닐수 있음 

```java
Order order = new Order();
order.setOrderLine(lines);
order.setShippingInfo(shippingInfo);
// 주문자를 설정하지 않은 상태에서 주문 완료 처리 
order.setState(OrderState.PREPARING);
```

- DTO 
  - Data Transfer Object 
  - 프레젠테이션 계층과 도메인 계층이 데이터를 서로 주고받을 때 사용하는 일종의 구조체 
  
### 도메인 용어

```java
public enum OrderState {
  PAYMENT_WAITING, PREPARING, SHIPPED, DELIVERING, DELIVERY_COMPLETED;
}
```

- 장점
  - 코드를 도메인 용어로 해석하거나 도메인 용어로를 코드로해석하는 과정이 줄어듬
  - 코드 가독성을 높임
  - 버그 감소

> 도메인 용어에 알맞은 단어를 찾는 시간을 아까워 하지 말자.
