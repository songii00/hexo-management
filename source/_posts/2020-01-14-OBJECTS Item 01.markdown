---

layout: post
title: "객체, 설계"
date: 2020-01-14 21:04:23 +0900
tags: [OBJECTS]
categories: objects
subtitle : OBJECTS - Item 01
feature-img: "assets/img/banner.jpg"
---

### "OBJECTS 1장"
---

### 이론? 실무?

> 이론이 먼저일까, 실무가 먼저일까? - 로버트 L.글래스

결론은 이론보다 실무가 먼저다. 

- 실무의 발전 -> 이론이 모습을 갖춰감 -> 해당 분야가 성숙해지는 시점에서야 이론이 실무를 추월
- 소프트웨어 분야는 아직 걸음마 단계에 머물러 있으므로 이론보다 실무가 더 앞서있으며, 중요

<br>

<!-- more -->

### 01 티켓 판매 애플리케이션 구현하기

![](/assets/images/post/200115/(1).png)

​	<br>

```java
public class Invitation { // 초대장
    private LocalDateTime when; // 초대일자
}

@Getter
public class Ticket {
    private Long fee; // 요금
}

@AllArgsConstructor
public class Bag {
    // 관람객 - 초대장, 현금, 티켓 만 소지할 수 있음
    // 소지품을 보관할 가방
    private Long amount; // 현금
    private Invitation invitation; // 초대장
    private Ticket ticket; // 티켓

    public boolean hasInvitation(){ // 초대장 여부 판단
        return invitation != null;
    }
    public boolean hasTicket(){ // 티켓 여부 판단
        return ticket != null;
    }
    public void minusAmount(Long amount){ // 현금 증가 혹은 감소
        this.amount -= amount;
    }
    public void plusAmount(Long amount){
        this.amount += amount;
    }
    public void setTicket(Ticket ticket){ // 초대장을 티켓으로 교환
        this.ticket = ticket;
    }
}

@AllArgsConstructor
@Getter
public class Audience { // 관람객
    private Bag bag; // 가방을 소지
}

@Getter
public class TicketOffice { // 매표소
    private Long amount; // 티켓의 판매 금액이 보관
    private List<Ticket> tickets = new ArrayList<>(); // 교환해 줄 티켓 목록

    public void minusAmount(Long amount){
        this.amount -= amount;
    }

    public void plusAmount(Long amount){
        this.amount += amount;
    }

    public Ticket getTicket(){
        return tickets.remove(0);
    }
}

@AllArgsConstructor
@Getter
public class TicketSeller { // 판매원
    // 매표소에서 초대장을 티켓으로 교환해주거나 티켓을 판매
    private TicketOffice ticketOffice;
}

@AllArgsConstructor
public class Theater {
    private TicketSeller ticketSeller;

    public void enter(Audience audience){ // 입장 
        if(audience.getBag().hasInvitation()){ // 관람객의 초대장 확인 
            // 이벤트 당첨 관람객
            // 판매원에게 티켓을 받음 
            Ticket ticket = ticketSeller.getTicketOffice().getTicket(); 
            // 관람객의 가방에 티켓을 넣어줌 
            audience.getBag().setTicket(ticket);
        }
        else {
            // 이벤트 미당첨 관람객
            // 판매원에게 티켓을 받음 
            Ticket ticket = ticketSeller.getTicketOffice().getTicket();
            // 관람객 가방에서 티켓 금액만큼 차감 
            audience.getBag().minusAmount(ticket.getFee());
            // 매표소에 금액 증가 
            ticketSeller.getTicketOffice().plusAmount(ticket.getFee());
            // 관람객의 가방에 티켓을 넣어줌 
            audience.getBag().setTicket(ticket);
        }
    }   
    
    // 입장 끝 
}
```

<br>

### 02 무엇이 문제인가

> 모든 소프트웨어 모듈에는 세가지 목적이 있다. 
>
> 첫번째 목적은 실행 중에 제대로 동작하는 것이다. ..
>
> 두번째 목적은 변경을 위해 존재하는 것이다. 대부분의 모듈은 생명주기 동안 변경되기 때문에 간단한 작업만으로도 변경이 가능해야 한다. ...
>
> 모듈의 세번째 목적은 코드를 읽는 사람과 의사소통 하는 것이다.  - 로버트 마틴

결국 마틴에 따르면 모든 모듈은 제대로 실행되어야 하고, 변경에 용이해야 하며, 이해하기 쉬어야 한다.

<br>

#### 예상을 빗나가는 코드

앞의 프로그램은 불행하게도 두번째와 세번째 목적을 만족시키지 못한다.

- 이해 가능한 코드란 그 동작이 우리의 예상을 크게 벗어나지 않는 코드이다.
- 앞의 예제는 관람객과 판매원이 소극장의 통제를 받는 수동적인 존재이다.
- 여러가지 세부적인 내용들을 한꺼번에 기억하고 있어야 한다.
  - Theater 의 enter 메서드 살펴보기
  - audience 가 bag을 가지고 있고, bag 안에는 현금, 티켓이 들어있으며, TicketSeller 가 TicketOffice 에서 티켓을 판매하고 TicketOffice 안에 돈과 티켓이 보관되어 있다는 사실을 모두 기억해야 함
- audience와 ticketseller를 변경할 경우 theater도 함께 변경해야함 

<br>

#### 변경에 취약한 코드

만약 관람객이 가방을 들고 다니지 않는다면 ?

현금이 아니라 신용카드로 결제한다면?

**"이것은 객체 사이의 의존성(dependency) 과 관련된 문제이다."**

<br>

- 의존성은 변경에 대한 영향을 암시
- 어떤 객체가 변경될 때 그 객체에 의존하는 다른 객체도 함께 변경될 수 잇음.
- 애플리케이션의 기능을 구현하는 최소한의 의존성만 유지하고 불필요한 의존성을 제거해야 함.

![](/assets/images/post/200115/(2).png)

***"객체 사이의 의존성이 과한 경우를 가리켜 결합도(coupling)이 높다고 말한다. 반대로 객체들이 합리적인 수준으로 의존할 경우에 결합도가 낮다고 한다."***

- 두 객체 사이의 결합도가 Up -> 함께 변경될 가능성도 Up -> 변경이 어려워짐
- 설계의 목표는 결합도를 낮춰 변경이 용이한 설계를 만드는것

<br>

### 03 설계 개선하기

관람객과 판매원을 자율적인 존재로 만들자.



#### 자율성을 높이자

- 문제점 : Theater 가 audience와 ticketSeller , Audience 소유의 Bag, TicketSeller 가 근무하는 TicketOffice 모두 접근 가능

- 해결방법 : Audience 와 TicketSeller 가 직접 Bag과 TicketOffice 를 처리하는 자율적인 존재가 되도록 설계 변경

첫번째는 Threater 내부의 enter 메서드에서 TicketOffice 에 접근하는 모든 코드를 TicketSeller로 숨기기

```java
@AllArgsConstructor
public class TicketSeller { // 판매원
    // 매표소에서 초대장을 티켓으로 교환해주거나 티켓을 판매
    private TicketOffice ticketOffice;

    public void sellTo(Audience audience){
        if(audience.getBag().hasInvitation()){ // 관람객의 초대장 확인
            // 이벤트 당첨 관람객
            // 판매원에게 티켓을 받음
            Ticket ticket = ticketOffice.getTicket();
            // 관람객의 가방에 티켓을 넣어줌
            audience.getBag().setTicket(ticket);
        }
        else {
            // 이벤트 미당첨 관람객
            // 판매원에게 티켓을 받음
            Ticket ticket = ticketOffice.getTicket();
            // 관람객 가방에서 티켓 금액만큼 차감
            audience.getBag().minusAmount(ticket.getFee());
            // 매표소에 금액 증가
            ticketOffice.plusAmount(ticket.getFee());
            // 관람객의 가방에 티켓을 넣어줌
            audience.getBag().setTicket(ticket);
        }
    }
}

@AllArgsConstructor
public class Theater {
    private TicketSeller ticketSeller;

    public void enter(Audience audience){ // 입장
        ticketSeller.sellTo(audience);
    }
}
```

<br>

- 외부에서 ticketOffice 에 직접 접근할수 없음
- TicketSeller는 ticketOffice 에서 티켓을 꺼내거나 판매요금을 적립하는 일을 스스로 수행
- Theater 는 ticketOffice 내부의 TicketSeller의 존재를 모름

**"개념적이나 물리적으로 객체 내부의 세부사항을 감추는 것을 캡슐화(encapsulation)라고 부른다."**

- Theater는 오직 TicketSeller의 인터페이스에만 의존
- TicketSeller 내부의 TIcketOffice 인스턴스를 포함하고 있는 것은 구현 영역에 속함
- 객체를 인터페이스와 구현으로 나누고 인터페이스만 공개하는 것은 객체 사이의 결합도를 낮추고 변경하기 쉬운 코드를 작성하기 위한 가장 기본적인 설계원칙

![](/assets/images/post/200115/(3).png)

두번째는 Audience 의 캡슐화 개선하기이다. 

- TicketSeller 의 Audience 의 getBag 메서드를 호출 -> Audience 내부의 Bag 인스턴스에 직접 접근

```java
@AllArgsConstructor
public class Audience { // 관람객
    private Bag bag; // 가방을 소지

    public Long buy(Ticket ticket){
        if(bag.hasInvitation()){ // 관람객의 초대장 확인
            // 이벤트 당첨 관람객
            // 관람객의 가방에 티켓을 넣어줌
           bag.setTicket(ticket);
           return 0L;
        }
        else {
            // 이벤트 미당첨 관람객
            // 관람객의 가방에 티켓을 넣어줌
            bag.setTicket(ticket);
            // 관람객 가방에서 티켓 금액만큼 차감
            bag.minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }

}

@AllArgsConstructor
public class TicketSeller { // 판매원
    // 매표소에서 초대장을 티켓으로 교환해주거나 티켓을 판매
    private TicketOffice ticketOffice;

    public void sellTo(Audience audience){
       ticketOffice.plusAmount(audience.buy(ticketOffice.getTicket()));
    }
}
```

- TicketSeller 와 Audience 사이의 결합도 낮아짐
- 내부 구현의 캡슐화로 Audience 의 구현을 수정하더라도 TicketSeller 에 영향을 미치지 않음

![](/assets/images/post/200115/(4).png)

<br>

#### 어떻게 한것인가

자기 자신의 문제를 스스로 해결하도록 코드를 변경하였다. 객체의 자율성을 높이는 방향으로 설계를 개선하였으며, 그결과 이해하기 쉽고 유연한 설계를 얻을 수 있다.

<br>

#### 캡슐화와 응집도

**"핵심은 객체 내부의 상태를 캡슐화하고 객체 간에 오직 메세지를 통해서만 상호작용 하도록 만드는 것이다."**

밀접하게 연관된 작업만을 수행하고 연관성 없는 작업은 다른 객체에게 위임하는 객체를 가르켜 응집도가 높다고 말한다. 따라서 객체의 응집도를 높이기 위해서 객체 스스로 자신의 데이터를 책임져야 한다.

<br>

#### 절차지향과 객체지향

- 수행하기 전 코드에서 Theater 는 enter 메서드 안에서 관람객을 입장시키는 절차를 구현
- Audience, TicketSeller, Bag, TicketOffice 는 관람객을 입장시키는데 필요한 정보를 제공
- 모든 처리는 Theater 의 enter 메서드에서 존재
- Theater의 enter 메서드는 -> 프로세스
- Audience, TicketSeller, Bag, TicketOffice -> 데이터

**"프로세스와 데이터를 별도의 모듈에 위치시키는 방식을 절차적 프로그래밍(Procedural Programming) 이라고 부른다."**

- 수정 후 코드는 데이터를 사용하는 프로세스가 데이터를 소유하고 있는 Audience 와 TicketSeller 내부로 옮겨짐

**"데이터와 프로세스가 동일한 모듈 내부에 위치하도록 프로그래밍 하는 방식을 객체지향 프로그래밍(Object-Oriented Programming)이라고 부른다."**

- 캡슐화를 이용해 의존성을 적절히 관리
- 객체 사이의 결합도를 낮춤
- 객체지향이 절차지향에 비해 좀더 유연함
- 객체 내부의 변경이 외부에 파급되지 않도록 제어할 수 있어 변경이 수월

<br>

#### 책임의 이동 (shift of responsibility)

![](/assets/images/post/200115/(5).png)

![](/assets/images/post/200115/(6).png)

- Theater에 몰려있던 책임이 개별 객체로 이동함

> "이것이 바로 책임의 이동을 의미하는 것이다."

- 설계를 어렵게 만드는 것은 의존성임.
- 불필요한 의존성을 제거하고 객체 사이의 결합도를 낮춰야 함 -> 캡슐화
- 자율성 Up, 응집도 Up 

<br>

#### 더 개선할 수 있다

```java
@AllArgsConstructor
public class Bag {
    private Long amount;
    private Invitation invitation; 
    private Ticket ticket; 

  	// Bag 내부에서만 사용되므로 public -> private 으로 변경
    private boolean hasInvitation(){ // 초대장 여부 판단
        return invitation != null;
    }
    private boolean hasTicket(){ // 티켓 여부 판단
        return ticket != null;
    }
    private void minusAmount(Long amount){ // 현금 증가 혹은 감소
        this.amount -= amount;
    }
    private void plusAmount(Long amount){
        this.amount += amount;
    }
    private void setTicket(Ticket ticket){ // 초대장을 티켓으로 교환
        this.ticket = ticket;
    }
    
    public Long hold(Ticket ticket){
        if(hasInvitation()){ // 관람객의 초대장 확인
            // 이벤트 당첨 관람객
            // 가방에 티켓 넣음
            setTicket(ticket);
            return 0L;
        }
        else {
            // 이벤트 미당첨 관람객
            // 가방에 티켓 넣음
            setTicket(ticket);
            // 가방에서 티켓 금액만큼 차감
            minusAmount(ticket.getFee());
            return ticket.getFee();
        }
    }
}

@AllArgsConstructor
public class Audience { // 관람객
    private Bag bag; // 가방을 소지

    public Long buy(Ticket ticket){
       return bag.hold(ticket);
    }
}
```

- Bag 을 자율적인 존재로 변경 -> Bag 내부 상태에 접근하는 모든 로직을 Bag 안으로 캡슐화 -> 결합도 Down
- Audience 를 Bag의 구현이 아닌 인터페이스에 의존하도록 수정

<br>

```java
@Getter
public class TicketOffice { // 매표소
    private Long amount; // 티켓의 판매 금액이 보관
    private List<Ticket> tickets = new ArrayList<>(); // 교환해 줄 티켓 목록

    // private 접근 제한자로 변경
    private void minusAmount(Long amount){
        this.amount -= amount;
    }

    private void plusAmount(Long amount){
        this.amount += amount;
    }

    private Ticket getTicket(){
        return tickets.remove(0);
    }
    
    public void sellTicketTo(Audience audience){
        plusAmount(audience.buy(getTicket()));
    }
}

@AllArgsConstructor
public class TicketSeller { // 판매원
    // 매표소에서 초대장을 티켓으로 교환해주거나 티켓을 판매
    private TicketOffice ticketOffice;
		// ticketOffice가 자율적임
    public void sellTo(Audience audience){
       ticketOffice.sellTicketTo(audience);
    }
}
```
![](/assets/images/post/200115/(7).png)<br>


- TicketSeller 가 TicketOffice 의 구현이 아닌 인터페이스에만 의존하게됨
- 다만 TicketOffice와 Audience 사이의 의존성이 추가됨
- TicketOffice의 자율성 높아짐 -> 의존성의 추가 -> 높은 결합도 -> 변경하기 어려운 설계
- 어떤 방법이 더 좋은 방법일까?

> 첫째, 어떤 기능을 설계하는 방법은 한가지 이상일 수 있다. 둘째, 동일한 기능을 한 가지 이상의 방법으로 설계할 수 있기 때문에 결국 설계는 트레이드오프의 산물이다.

<br>

#### 그래 거짓말이다!

- 의인화
  - 능동적이고 자율적인 존재로 소프트웨어 객체를 설계하는 원칙 
  - 현실에서는 생명이 없는 수동적인 존재일 지라도, 객체지향의 세계로 넘어오는 순간 지능과 의지를 가진 존재로 태어남

<br>

### 04 객체지향 설계

#### 설계가 왜 필요한가

> 설계란 코드를 배치하는 것이다.

- 설계는 코드를 작성하는 매 순간 코드를 어떻게 배치할 것인지를 결정하는 과정에서 나옴.
- 좋은 설계란 두가지 요구사항을 만족해야 함
  - 오늘완성해야 하는 기능을 구현하는 코드를 짬과 동시에 내일 쉽게 변경할 수 있는 코드를 짜야 함
  - 요구사항은 항상 변경되기 때문에 변경을 수용할 수 있는 설계가 중요
  - 코드를 변경할 때 버그를 수반하므로 변경에 유연하게 대응할 수 있는 설계가 중요

#### 객체지향 설계

- 변경에 유연하게 대응할 수 있는 코드 -> 이해하기 쉬운 코드 
- 훌륭한 객체지향 설계란 협력하는 객체 사이의 의존성을 적절하게 관리하는 설계
- 데이터와 프로세스를 하나의 덩어리로 모으는 것은 훌륭한 객체지향 설계로 가는 첫걸음

