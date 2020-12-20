---
layout: post
title: "메시지와 인터페이스"
date: 2020-02-23:10:52:32 +0900
tags: [OBJECTS]
categories: objects
subtitle : 
feature-img: "assets/img/banner.jpg"
---

### "OBJECTS 6장"
---

- 훌륭한 객체지향 코드를 얻기 위해서는 클래스가 아니라 객체를 지향
- 애플리케이션의 가장 중요한 재료 = 객체들이 주고받는 메시지 
- 객체가 수신하는 메시지들이 객체의 퍼블릭 인터페이스를 구성

> 애플리케이션은 클래스로 구성되지만 메시지를 통해 정의된다.

<!-- more -->

<br>

### 협력과 메시지 

#### 클라이언트 - 서버 모델

- 협력 안에서 메시지를 전송하는 객체 : 클라이언트
- 메시지를 수신하는 객체 : 서버
- 객체는 협력에 참여하는 동안 클라이언트와 서버의 역할을 동시에 수행
- 협력에 적합한 객체를 설계하기 위해서는 외부에 전송하는 메시지의 집합도 함께 고려하는 것이 바람직 

![](/assets/images/post/200223/(1).png)

> 객체가 독립적으로 수행할 수 있는 것보다 더 큰 책임을 수행하기 위해서는 다른 객체와 협력해야 한다는 것이다. 
>
> 두 객체 사이의 협력을 가능하게 해주는 매개체가 바로 메시지라는 것이다.

<br>

#### 메시지와 메시지 전송 

- 메시지 : 객체들이 협력하기 위해 사용할 수 있는 유일한 의사소통 
  - 메시지 전송(메시지 패싱) : 한 객체가 다른 객체에게 도움을 요청하는 것 
  - 메시지 전송자 : 메시지를 전송하는 객체 
  - 메시지 수진자 : 메시지를 수신하는 객체 
  - 오퍼레이션명 + 인자
- 메시지 전송 
  - 메시지 수신자 + 오퍼레이션명 + 인자

![](/assets/images/post/200223/(2).png)

<br>

#### 메시지와 메서드 

- 메서드 : 메시지를 수신했을 때 실제로 실행되는 함수 또는 프로시저
- 객체의 타입에 따라 실행되는 메서드가 달라질 수 있음 
  - 두 객체 사이의 결합도를 낮춤
  - 유연하고 확장 가능한 코드 작성 가능하게 함

>  객체는 메시지와 메서드라는 두가지 서로 다른 개념을 실행 시점에 연결해야 하기 떄문에 컴파일 시점과 실행 시점의 의미가 달질 수 있다. 

<br>

- 메시지 수신자는 메시지를 처리하기 위해 필요한 메서드를 스스로 결정할 수 있음

<br>

#### 퍼블릭 인터페이스와 오퍼레이션 

- 퍼블릭 인터페이스 : 객체가 의사소통을 위해 외부에 공개하는 메시지의 집합

- 오퍼레이션 : 퍼블릭 인터페이스에 포함된 메시지 

  - 수행 가능한 어떤 행동에 대한 `추상화` 
  - 내부의 구현 코드는 제외하고 단순히 메시지와 관련된 시그니처만을 가르킴
  - 실행하기 위해 객체가 호출될 수 있는 변환이나 정의에 관한 명세 
  - 인터페이스의 각 요소는 오퍼레이션 
  - 구현이 아닌 `추상화`

- 메서드 : 메시지를 수신했을 때 실제로 실행되는 코드

  - 오퍼레이션을 구현한 것 
  - 오퍼레이션과 관련된 알고리즘 또는 절차를 명시 

  ![](/assets/images/post/200223/(3).png)

1. 객체가 다른 객체에게 메시지 전송
2. 런타임 시스템은 메시지 전송을 오퍼레이션 호출로 해석
3. 메시지를 수신한 객체의 실제 타입 기반으로 적절한 메서드 실행

<br>

#### 시그니처 

- 시그니처 : 오퍼레이션(또는 메서드) 의 이름 + 파라미터 목록
> 오퍼레이션의 관점에서 다형성이란 동일한 오퍼레이션 호출에 대해 서로 다른 메서드들이 실행되는 것 

- 메시지가 객체의 품질을 결정 

<br>

### 인터페이스와 설계 품질 

- 좋은 인터페이스는 최소한의 인터페이스와 추상적인 인터페이스라는 조건을 만족해야 함
- 최소한의 인터페이스 
  - 꼭 필요한 오퍼레이션만을 인터페이스에 포함
- 추상적인 인터페이스
  - 어떻게 수행하는지가 아니라 무엇을 하는지
- 퍼블릭 인터페이스의 품질에 영향을 미치는 원칙과 기법
  - 디미터 법칙
  - 묻지 말고 시켜라
  - 의도를 드러내는 인터페이스
  - 명령 - 쿼리 분리

<br>

##### 디미터 법칙

- 협력하는 객체의 내부 구조에 대한 결합으로 인해 발생하는 설계 문제를 해결하기 위해 제안된 원칙 
- 객체 내부 구조에 강하게 결합되지 않도록 협력 경로를 제한 
- 낯선 자에게 말하지 말라
- 인접한 이웃하고만 말하라 
- 클래스가 특정한 조건을 만족하는 대상에게만 메시지를 전송하도록 프로그래밍 해야함
  - this 객체
  - 메서드의 매개변수
  - this 의 속성
  - this 의 속성인 컬렉션의 요소 
  - 메서드 내에서 생성된 지역 객체 

<br>

```java
screening.getMovie().getDiscountConditions();
```

```java
screening.calculateFee(audienceCount);
```
<br>

- 부끄럼타는 코드 (shy code)
  
  - 불필요한 어떤 것도 다른 객체에게 보여주지 않으며, 다른 객체의 구현에 의존하지 않는 코드 
  
  
> 메시지 수진자의 내부 구조가 전송자에게 노출되지 않으며, 메시지 전송자는 수신자의 내부 구현에 결합되지 않음 

<br>

- 기차충돌 : 메시지 전송자가 수신자의 내부 구조에 대해 물어보고 반환받은 요소에 대해 연쇄적으로 메시지를 전송 
  - 일련의 getter 들이 기차의 객체처럼 상호 연결되어 보이는 코드

<br>

#### 묻지 말고 시켜라 

- 객체의 상태에 관해 묻지 말고 원하는 것을 시켜라 
> 내부의 상태를 묻는 오퍼레이션을 인터페이스에 포함시키고 있다면 더 나은 방법은 없는지 고민해 보라.
>
> 상태를 묻는 오퍼레이션을 행동을 요청하는 오퍼레이션으로 대체함으로써 인터페이스를 향상시켜라.

<br>

- 인터페이스는 객체가 어떻게 하는지가 아니라 무엇을 하는지를 서술해야 함

<Br>

#### 의도를 드러내는 인터페이스 

메서드를 명명하는 두가지 방법

1. 메서드가 작업을 어떻게 수행하는지를 나타내도록 이름짓는 것 

```java
public class PeriodCondition implements DiscountCondition {
    public boolean isSatisfiedByPeriodCondition(Screening screening) {...}
}

public class SequenceCondition implements DiscountCondition {
    public boolean isSatisfiedBySequenceCondition(Screening screening) {...}
}
```

<br>

**문제점**

- 두개의 메서드의 이름이 달라 두 메서드가 동일한 작업을 수행한다는 것을 알기 어려움
- 메서드 수준에서 캡슐화 위반 (precondition 사용하는 코드 변경 시 호출하는 메서드를 변경해야 함)

<br>

2. '어떻게'가 아니라 '무엇'을 하는지를 드러내는 것 

- 어떻게 수행하는지를 드러내는 이름 :  메서드의 내부 구현을 설명하는 이름 
- 객체가 협력 안에서 수행하는 책임에 관해 고민해야 함

```java
public interface DiscountCondition {
    boolean isSatisfiedBy(Screening screening);
}

public class PeriodCondition implements DiscountCondition {
    public boolean isSatisfiedBy(Screening screening) {...}
}

public class SequenceCondition implements DiscountCondition {
    public boolean isSatisfiedBy(Screening screening) {...}
}
```

<br>

##### 장점

- 동일한 목적을 가진다는 것을 메서드의 이름을 통해 명확히 표현
- 두 메서드는 동일한 메시지를 서로 다른 방법으로 처리하기 때문에 대체 가능

> 어떻게 하느냐가 아니라 무엇을 하느냐에 따라 메서드의 이름을 짓는 패턴을 의도를 드러내는 선택자(Intention Revealing Selector) 라고 부른다.

> 하나의 구현을 가진 메시지의 이름을 일반화 하도록 도와주는 간단한 훈련방법을 소개하겠다. 매우 다른 두번째 구현을 상상하라. 그러고는 해당 메서드에 동일한 이름을 붙인다고 상상해보라. 그렇게 하면 아마도 그간에 여러분이 할수 있는 한 가장 추상적인 이름을 메서드에 붙일 것이다.

<br>

- 의도를 드러내는 인터페이스 : 의도를 드러내는 선택자를 인터페이스 레벨로 확장
  - 구현과 관련된 모든 정보를 캡슐화
  - 객체의 퍼블릭 인터페이스에는 협력과 관련된 의도만을 표현
  - 수행방법에 관해서는 언급하지 말고 결과와 목적만을 포함하도록 클래스와 오퍼레이션의 이름을 부여 
- 객체에게 묻지 말고 시키되 구현 방법이 아닌 클라이언트의 의도를 드러내야 함

<br>

#### 함께 모으기 

```java
// 디미터 법칙 위반 
public class Theater {
    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

    public void enter(Audience audience) {
        if (audience.getBag().hasInvitation()) {
            // 기차 충돌
          	// Theater 는 ticketSeller가 가진 TicketOffice, Ticket에 대해 알아야함
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().setTicket(ticket);
        } else {
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            audience.getBag().minusAmount(ticket.getFee());
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            audience.getBag().setTicket(ticket);
        }
    }
}
```

<br>

#### 묻지 말고 시켜라

```java
public class Theater {
    private TicketSeller ticketSeller;

    public Theater(TicketSeller ticketSeller) {
        this.ticketSeller = ticketSeller;
    }

    public void enter(Audience audience) {
        ticketSeller.setTicket(audience);
    }
}
```

```java
public class TicketSeller {
    private TicketOffice ticketOffice;

    public TicketSeller(TicketOffice ticketOffice) {
        this.ticketOffice = ticketOffice;
    }

    public void setTicket(Audience audience) {
        ticketOffice.plusAmount(audience.setTicket(ticketOffice.getTicket()));
    }
}
```

```java
public class Audience {
    private Bag bag;

    public Audience(Bag bag) {
        this.bag = bag;
    }

    public Long setTicket(Ticket ticket) {
        return bag.setTicket(ticket);
    }
}

public class Bag {
    private Long amount;
    private Invitation invitation;
    private Ticket ticket;

    public Bag(long amount) {
        this(null, amount);
    }

    public Bag(Invitation invitation, long amount) {
        this.invitation = invitation;
        this.amount = amount;
    }

    public Long setTicket(Ticket ticket) {
        if (hasInvitation()) {
            this.ticket = ticket;
            return 0L;
        } else {
            this.ticket = ticket;
            minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }

    private boolean hasInvitation() {
        return invitation != null;
    }

    private void minusAmount(Long amount) {
        this.amount -= amount;
    }
}
```

<br>

#### 인터페이스에 의도를 드러내자 

- 클라이언트의 의도가 분명하게 드러나도록 객체의 퍼블릭 인터페이스를 개선 

```java
public class TicketSeller {
    public void sellTo(Audience audience) {
        ...
    }
}
 public class Audience {
 		public Long buy(Ticket ticket) {
          	...
    }
}
 public class Bag {
 		public Long hold(Ticket ticket) {
           ...
  	}
}
```

- 오퍼레이션의 이름은 협력이라는 문맥을 반영해야함 
- 클라이언트가 객체에게 무엇을 원하는지를 표현해야 함
- 객체 자신이 아닌 클라이언트의 의도를 표현하는 이름을 가져야 함

<br>

### 원칙의 함정

- 설계는 트레이드 오프의 산물

>  원칙을 아는 것보다 더 중요한 것은 언제 원칙이 유용하고 언제 유용하지 않은지를 판단할 수 있는 능력을 기르는 것이다. 

<br>

#### 디미터 법칙은 하나의 도트(.) 를 강제하는 규칙이 아니다

```java
InputStream.of(1,15,20,3,9).filter(x -> x >10).distinct().count();
```

> 기차 충돌처럼 보이는 코드라도 객체의 내부 구현에 대한 어떤 정보도 외부로 노출하지 않는다면 그것은 디미터 법칙을 준수한 것이다.

- 과연 여러개의 도트를 사용한 코드가 객체의 내부 구조를 노출하고 있는가?

<br>

#### 결합도와 응집도의 충돌 

> 디미터 법칙과 묻지 말고 시켜라 원칙을 무작정 따르면 애플리케이션은 응집도가 낮은 객체로 넘쳐날 것이다.

**Before**

```java
public class PeriodCondition implements DiscountCondition {
    public boolean isSatisfiedByPeriodCondition(Screening screening) {
        return screening.getStartTime().getDayOfWeek().equals(dayOfWeek) &&
        startTime.compareTo(screening.getStartTime().toLocalTime()) <=0 &&
        endTime.compareTo(screening.getEndTime().toLocalTime()) >= 0;        
    }
}
```

<br>

**After**

```java
public class Screening {
    public boolean isDiscountable(DayOfWeek dayOfWeek, LocalTime startTime, LocalTime endTime){
        return whenScreened.getDayOfWeek().equals(dayOfWeek) && 
                startTime.compareTo(whenScreened.toLocalTime()) <=0 &&
                endTime.compareTo(whenScreened.toLocalTime()) >=0;
    }
}

public class PeriodCondition implements DiscountCondition {
    public boolean isSatisfiedByPeriodCondition(Screening screening) {
        return screening.isDiscountable(dayOfWeek, startTime, endTime);
    }
}
```
<br>
- Screening이 기간에 따르는 할인조건을 판단하는 책임을 떠안게 됨 
- Screening의 캡슐화를 향상시키는 것보다 Screening의 응집도를 높이고 PreiodCondition 사이의 결합도를 낮추는 것이 전체적인 관점에서 더 좋은 방법
- 디미터 법칙의 위반 여부는 묻는 대상이 객체인지, 자료구조 인지에 달려 있음
  - 객체는 내부 구조를 숨겨야 하므로 디미터 법칙을 따라야 함
  - 자료구조라면 디미터 법칙을 적용할 필요가 없음

> 원칙을 맹신하지 마라. 원칙이 적절한 상황과 부적절한 상황을 판단할 수 있는 안목을 길러라.

<br>

### 명령-쿼리 분리 원칙

- 루틴 : 어떤 절차르 묶어 호출 가능하도록 이름을 부여한 기능 모듈
  - 프로시저와 함수로 구분
- 프로시저 : 부수 효과를 발생 시킬 수 있지만 값을 반환할 수 없음
- 함수 : 값을 반환할 수 있지만 부수효과를 발생시킬 수 없음
- 명령과 쿼리 
  - 객체의 인터페이스 측면에서 프로시저와 함수를 부르는 또다른 이름
  - 명령 = 프로시저
  - 쿼리 = 함수
- 오퍼레이션은 부수효과를 발생시키는 명령이거나 부수효과를 발생시키지 않는 쿼리 중 하나여야 함
- 어떤 오퍼레이션도 명령인 동시에 쿼리여서는 안됨
- 명령-쿼리 인터페이스 
  - 명령-쿼리 분리 원칙에 따라 작성된 객체의 인터페이스

<br>

#### 반복 일정의 명령과 쿼리 분리하기

- 명령과 쿼리를 뒤섞으면 실행 결과를 예측하기 어려움
- 가장 깔끔한 해결책은 명령과 쿼리를 명확하게 분리하는 것

<br>

#### 명령-쿼리 분리와 참조 투명성

명령과 쿼리를 분리함으로써 명령형 언어의 틀 안에서 `참조 투명성` 의 장점을 누릴수 있다.

- 부수효과(side effect)
- 참조 투명성
  - 어떤 표현식 e가 있을 때 모든 e를 e의 값으로 바꾸더라도 결과가 달라지지 않는 특성
- 불변성
  - 어떤 값이 변하지 않는 성질 

<br>

#### 책임에 초점을 맞춰라 

- 메시지를 먼저 선택하고 그 후에 메시지를 처리할 객체를 선택하라
- 계약에 의한 설계 
  - 클라이언트와 서버가 준수해야 하는 제약을 코드 상에 명시적으로 표현하고 강제할 수 있는 방법

<br>