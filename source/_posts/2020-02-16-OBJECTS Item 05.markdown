---

layout: post
title: "책임 할당하기"
date: 2020-02-16:13:02:43 +0900
tags: [OBJECTS]
categories: objects
subtitle : 
feature-img: "assets/img/banner.jpg"
---

### "OBJECTS 5장"
---

- 데이터가 아닌 책임에 초점을 맞추자.
- GRASP 패턴 

<bR>

### 책임 주도 설계를 향해

- 데이터보다 행동을 먼저 결정하라
- 협력이라는 문맥안에서 책임을 결정하라 

<br>

<!-- more -->

#### 데이터보다 행동을 먼저 결정하라

- 객체에게 중요한 것은 데이터가 아니라 외부에 제공하는 행동임
- "이 객체가 수행해야 하는 책임은 무엇인가" 를 결정하느 후에 "이 책임을 수행하는데 필요한 데이터는 무엇인가"를 결정
- 책임중심의 설계에서는 객체의 행동, 즉 책임을 결정한 후에 객체의 상태를 결정

<br>

#### 협력이라는 문맥 안에서 책임을 결정하라

- 책임은 객채의 입장이 아니라 객체가 참여하는 협력에 적합해야 함
- 협력에 적합한 책임 : 메시지 전송자에게 적합한 책임
- 메시지를 전송하는 클라이언트의 의도에 적합한 책임을 할당해야 함
- 메시지를 결정 한 후에 객체를 선택
- 객체가 메시지를 선택하는 것이 아니라 메시지가 객체를 선택하게 해야 함

> 메시지를 전송해야 하는데 누구에게 전송해야 하지?
>
> 책임 중심의 설계에서는 협력이라는 문맥 안에 객체가 수행할 책임에 초점을 맞춘다.

<br>

#### 책임 주도 설계

- 책임을 결정한 후에 책임을 수행할 객체를 결정하는 것 

<br>

### 책임 할당을 위한 GRASP 패턴

- 크레이그 라만
- General Responsibility Assignment Software Pattern
- 일반적인 책임 할당을 위한 소프트 웨어 패턴
- 객체에게 책임을 할당할 때 지침으로 삼을 수 있는 원칙들의 집합을 패턴 형식으로 정리

<br>

#### 도메인 개념에서 출발하기 

- 어떤 책임을 할당해야 할 때 가장 먼저 고민해야 하는 유력한 후보

![](/assets/images/post/200216/(1).png)

- 올바른 구현을 이끌어 낼 수 있는 도메인은 모델은 모두 정답

<br>

#### 정보 전문가에게 책임을 할당하라 

1. 메시지를 전송할 객체는 무엇을 원하는가?
2. 메시지를 수신할 적합한 객체는 누구인가?

- INFORMATION EXPERT(정보 전문가) 패턴 : 책임을 수행할 정보를 알고 있는 객체에게 책임을 할당하는 것 

  - 필요한 정보를 가진 객체들로 책임이 분산
  - 응집력있고 이해가 쉬워짐
  - 객체가 자신이 소유하고 있는 정보와 관련된 작업을 수행한다는 일반적인 직관 표현
  - 정보는 데이터와 다름
  - 정보 전문가는 데이터를 반드시 저장하고 있을 필요는 없음 
  - 객체에게 책임을 할당할 때 가장 기본이 되는 책임 원칙
  - 객체란 상태와 행동을 함께 가지는 단위라는 객체지향의 가장 기본적 원리를 책임 할당의 관점에서 표현

  <br>
  
  ![](/assets/images/post/200216/(2).png)

<br>

#### 높은 응집도와 낮은 결합도 

- 설계는 트레이드오프 활동 
- 동일한 기능을 구현할 수 있는 무수히 많은 설계 존재

<br>

![](/assets/images/post/200216/(3).png)

<br>

- DiscountCondition과 누가 협력해야 할까?
- Movie ? Screening?
- 기능적 측면에서 두가지 중 어떤 방법을 선택하더라도 차이는 없음
- 높은 응집도와 낮은 결합도는 객체에 책임을 할당할 때 항상 고려해야 하는 기본 원리
- LOW COUPLING(낮은 결합도)
  - 설계의 전체적인 결합도가 낮게 유지되도록 책임을 할당
  - Movie 와 DiscountCondition은 이미 결합 
  - 따라서 설계 전체적으로 결합도를 추가하지 않고 협력을 완성할수 있음
  - Screeing이 DiscountCondition과 새로운 결합도가 추가
  - Movie가 더 나은 설계 대안
-  HIGH COHESION(높은 응집도) 패턴
  - 높은 응집도를 유지할 수 있게 책임을 할당
  - Screeing은 영화 요금 계산과 관련된 책임을 추가 맡아야 함
  - 요금 계산 방식의 변경 경우 Screeing도 함께 변경되어야 함
  - 서로 다른 이유로 변경되는 책임을 짊어지게 됨 -> 응집도 낮아짐
- LOW COUPLING, HIGH COHESION 패턴은 책임과 협력의 품질을 검토하는데 사용할 수 있는 중요한 평가 기준

<br>

#### 창조자에게 객체 생성 책임을 할당하라 

- CREATOR (창조자) 패턴 : 객체를 생성할 책임을 어떤 객체에게 할당할지에 대한 지침 제공
- 생성되는 객체와 연결되거나 관련될 필요가 있는 객체에 해당 객체를 생성할 책임을 맡기는 것 

![](/assets/images/post/200216/(4).png)

- Resercation의 CREATOR로 선택된 Screening
- 협력과 책임이 제대로 동작하는지 확인할 수 있는 유일한 방법은 코드를 작성하고 실행해 보는 것 뿐

> 올바르게 설계하고 있는지 궁금한가? 코드를 작성하라.

<br>

### 구현을 통한 검증 

- 변경에 취약한 클래스란 코드를 수정해야 하는 이유를 하나 이상 가지는 클래스 

> 변경의 이유에 따라 클래스를 분리해야 한다.

<br>

- 일반적으로 설계를 개선하는 작업은 변경의 이유가 하나 이상인 클래스를 찾는 것으로 부터 시작
- 코드를 통해 변경의 이유를 파악할 수 있는 첫 번째 방법은 인스턴스 변수가 초기화되는 시점
  - 응집도가 높은 클래스를 인스턴스를 생성할 때 모든 속성을 함께 토기화
  - 응집도가 낮은 클래스는 객체의 속성 중 일부만 초기화하고 일부는 초기화되지 않음

> 함께 초기화 되는 속성을 기준으로 코드를 분리해야 한다. 

<br>

- 두번째는 메서드들이 인스턴스 변수를 사용하는 방식을 살펴봐야 함
  - 모든 메서드가 객체의 모든 속성을 사용한다면 응집도 높음
  - 메서드들이 사용하는 속성에 따라 그룹이 나뉜다면 클래스의 응집도 낮음

> 속성 그룹과 해당 그룹에 접근하는 메서드 그룹을 기준으로 코드를 분리해야 한다. 

<br>

#### 다형성을 통해 분리하기 

- 역할 : 객체의 구체적인 타입을 추상화
  - 역할을 대체할 클래스들 사이에서 구현을 공유해야하는 필요가 있다면 추상클래스 사용
  - 구현을 공유할 필요 없이 역할을 대체하는 객체들의 책임만 정의하고 싶다면 인터페이스 사용 

![](/assets/images/post/200216/(5).png)

> 객체의 타입에 따라 변하는 행동이 있다면 타입을 분리하고 변화하는 행동을 각 타입의 책임으로 할당하라는 것이다. GRASP 에서는 이를 POLYMORPHISM(다형성) 패턴이라고 부른다.

![](/assets/images/post/200216/(6).png)

<br>

#### 변경으로부터 보호하기

- PROTECTED VARIATIONS(변경 보호) 패턴
  - 변경을 캡슐화하도록 책임을 할당하는 것 
  - "설계에서 변하는 것이 무엇인지 고려하고 변하는 개념을 캡슐화하라."
- 클래스를 변경에 따라 분리하고 인터페이스를 이용해 변경을 캡슐화하는 것은 설계의 결합도와 응집도를 향상시키는 매우 강력한 방법 
- 하나의 클래스가 여러 타입의 행동을 구현하고 있다면 ? 클래스 분할, POLYMORPHISM 패턴에 따라 책임 분산
- 예측 가능한 변경으로 여러 클래스들이 불안정해진다면? PROTECTED VARIATIONS 패턴에 따라 안정적인 인터페이그 뒤로 변경을 캡슐화

<br>

#### Movie 클래스 개선하기

- 책임을 중심으로 협력을 설계할 때 얻을 수 있는 혜택

  - 모든 클래스의 내부 구현은 캡슐화 
  - 모든 클래스는 변경의 이유를 오직 하나만 가짐
  - 각 클래스는 응집도가 높고 다른 클래스와 최대한 느슨하게 결합
  - 클래스는 작고 오직 한 가지 일만 수행
  - 책임은 적절하게 분배

  <br>

  ![](/assets/images/post/200216/(7).png)
  
  <br>
  
  - 데이터가 아닌 책임을 중심으로 설계하라
  - 객체에게 중요한 것은 상태가 아니라 행동
  - 객체지향 설계의 기본은 책임과 협력에 초점을 맞추는 것

<BR>

#### 변경과 유연성 

- 설계를 주도하는 것은 변경
- 변경에 대비하는 두가지 방법
  - 코드를 이해하고 수정하기 쉽도록 최대한 단순하게 설계
  - 코드를 수정하지 않고도 변경을 수용할 수 있도록 코드를 유연하게 만들기 
- 합성

<br>

![](/assets/images/post/200216/(8).png)

<br>

```java
Movie movie = new Movie("타이타닉",
        Duration.ofMinutes(210),
        Money.wons(2000),
        new AmountDiscountPolicy(...));

movie.changeDiscountPolicy(new PercentDiscountPolicy(...));
```

- 유연성에 대한 압박이 설계에 어떤 영향을 미치는지 보여줌
- 유연성은 의존성 관리의 문제 
- 의존성의 정도가 유연성의 정도를 결정 

<br>

### 책임 주도 설계의 대안 

이해하기 쉽고 수정하기 쉬운 소프트 웨어로 개선하기 위해 겉으로 보이는 동작은 바꾸지 않은 채 내부 구조를 변경하는 것

: 리팩터링

<br>

#### 메서드 응집도 

```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer, int audienceCount) {
         Movie movie = screening.getMovie();

        // 할인 가능 여부 확인
        boolean discountable = false;
        for(DiscountCondition condition : movie.getDiscountConditions()){
            if(condition.getType() == DiscountConditionType.PERIOD){
                discountable = screening.getWhenScreened().getDayOfWeek().equals(condition.getDayOfWeek())
                        && condition.getStartTime().compareTo(screening.getWhenScreened().toLocalTime()) <= 0
                        && condition.getEndTime().compareTo(screening.getWhenScreened().toLocalTime()) >= 0;
            }
            else {
                discountable = condition.getSequence() == screening.getSequence();
            }

            if(discountable){
                break;
            }
        }
        
        Money fee;
        if(discountable){
            Money discountAmount = Money.Zero;
            switch (movie.getMovieType()){
                case NONE_DISCOUNT:
                    discountAmount = Money.Zero;
                    break;
                case AMOUNT_DISCOUNT:
                    discountAmount = movie.getDiscountAmount();
                    break;
                case PERCENT_DISCOUNT:
                    discountAmount = movie.getFee().times(movie.getDiscountPercent());
                    break;
            }
            fee = movie.getFee().minus(discountAmount).times(audienceCount);
        }
        else {
            fee = movie.getFee();
        }
        return new Reservation(customer, screening, fee, audienceCount);
    }
}
```
<br>

- 몬스터 메서드 
- 긴 메서드는 응집도가 낮기 때문에 이해하기 어렵고 재사용하기 어려우며, 변경하기도 어려움
  - 메서드가 명령문들의 그룹으로 구성되있고 각 그룹에 주석을 달아야 한다면? 메서드 응집도 낮음
  - 주석을 추가하는 대신 메서드를 작게 분리해서 각메서드의 응집도를 높여라 
- 메서드가 잘게 나눠져있을 떼 

  - 다른 메서드에서 사용될 확률이 높아짐

  - 고수준의 메서드를 볼때 일련의 주석을 읽는 것 같은 느낌이 들게 함

  - 오버라이딩 하는 것이 쉬움
  - 이름을 지을 때 주의를 요함
- 객체로 책임을 분배할 떄 가장 먼저 할일은 메서드를 응집도 있는 수준으로 분해하는 것 

<br>

```java
public class ReservationAgency {
    public Reservation reserve(Screening screening, Customer customer,
                               int audienceCount) {
        boolean discountable = checkDiscountable(screening);
        Money fee = calculateFee(screening, discountable, audienceCount);
        return createReservation(screening, customer, audienceCount, fee);
    }

    private boolean checkDiscountable(Screening screening) {
        return screening.getMovie().getDiscountConditions().stream()
                .anyMatch(condition -> condition.isDiscountable(screening));
    }
    
    private Money calculateFee(Screening screening, boolean discountable,
                               int audienceCount) {
        if (discountable) {
            return screening.getMovie().getFee()
                    .minus(calculateDiscountedFee(screening.getMovie()))
                    .times(audienceCount);
        }
    
        return  screening.getMovie().getFee();
    }
    
    private Money calculateDiscountedFee(Movie movie) {
        switch(movie.getMovieType()) {
            case AMOUNT_DISCOUNT:
                return calculateAmountDiscountedFee(movie);
            case PERCENT_DISCOUNT:
                return calculatePercentDiscountedFee(movie);
            case NONE_DISCOUNT:
                return calculateNoneDiscountedFee(movie);
        }
    
        throw new IllegalArgumentException();
    }
    
    private Money calculateAmountDiscountedFee(Movie movie) {
        return movie.getDiscountAmount();
    }
    
    private Money calculatePercentDiscountedFee(Movie movie) {
        return movie.getFee().times(movie.getDiscountPercent());
    }
    
    private Money calculateNoneDiscountedFee(Movie movie) {
        return movie.getFee();
    }
    
    private Reservation createReservation(Screening screening,
                                          Customer customer, int audienceCount, Money fee) {
        return new Reservation(customer, screening, fee, audienceCount);
    }
}
```

<br>

- 코드를 작은 메서드로 분해하면 전체적인 흐름을 이해하기 쉬워짐
- 수정 후 코드는 변경이 쉬워짐 

<br>

#### 객체를 자율적으로 만들자 

```java
package org.eternity.movie.step05;

import java.time.DayOfWeek;
import java.time.LocalTime;

public class DiscountCondition {
    private DiscountConditionType type;

    private int sequence;
    
    private DayOfWeek dayOfWeek;
    private LocalTime startTime;
    private LocalTime endTime;
    
    public DiscountCondition(int sequence){
        this.type = DiscountConditionType.SEQUENCE;
        this.sequence = sequence;
    }
    
    public DiscountCondition(DayOfWeek dayOfWeek, LocalTime startTime, LocalTime endTime){
        this.type = DiscountConditionType.PERIOD;
        this.dayOfWeek= dayOfWeek;
        this.startTime = startTime;
        this.endTime = endTime;
    }
    
    public boolean isDiscountable(Screening screening) {
        if (type == DiscountConditionType.PERIOD) {
            return isSatisfiedByPeriod(screening);
        }
    
        return isSatisfiedBySequence(screening);
    }
    
    private boolean isSatisfiedByPeriod(Screening screening) {
        return screening.getWhenScreened().getDayOfWeek().equals(dayOfWeek) &&
                startTime.compareTo(screening.getWhenScreened().toLocalTime()) <= 0 &&
                endTime.compareTo(screening.getWhenScreened().toLocalTime()) >= 0;
    }
    
    private boolean isSatisfiedBySequence(Screening screening) {
        return sequence == screening.getSequence();
    }
}
```

<br>

- 자신이 소유하고 있는 데이터를 자기 스스로가 처리하도록 만드는 것이 자울적인 객체를 만드는 지름길

- 메서드를 다른 클래스로 이동시킬 때 인자에 정의된 클래스 중 하나로 이동하는 경우가 일반적임

- 데이터를 사용하는 메서드를 데이터를 가진 클래스로 이동시키고 나면 캡슐화와 높은 응집도, 낮은 결합도를 가지는 설계를 얻게됨

  >  책임 주도 설게 방법에 익숙하지 않다면? 일단 데이터 중심으로 구현 한 후 이를 리팩터링 하더라도 유사한 결과를 얻을 수 있다. 

