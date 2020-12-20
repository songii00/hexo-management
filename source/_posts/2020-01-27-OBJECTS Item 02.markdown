---

layout: post
title: "객체지향 프로그래밍"
date: 2020-01-27 11:07:51 +0900
tags: [OBJECTS]
categories: objects
subtitle : 
feature-img: "assets/img/banner.jpg"
---

### "OBJECTS 2장"
---

### 영화 예매 시스템

#### 요구사항 살펴보기

- 온라인 영화 예매 시스템 
- 할인조건(discount condition), 할인 정책(discount policy)
- 할인 조건 : 순서 조건(조조 상영, 10회 상영 등), 기간 조건(월요일 10 ~ 12 사이 상영 시작 등)
- 할인 정책 : 금액 할인 정책(할인액 800원 등), 비율 할인 정책(할인율 10% 등)
- 영화별로 하나의 할인 정책만 할당 할 수 있음. (지정하지 않아도됨)
- 할인 조건은 다수의 할인 조건을 함께 지정할 수 있음. (순서 + 기간 가능)

<!-- more -->

<br>

### 객체지향 프로그래밍을 향해

#### 협력, 객체, 클래스

- 객체지향은 말 그대로 객체를 지향하는 것
- 클래스가 아닌 객체 자체에 초점을 맞춰야 함

1. 첫째, 어떤 클래스가 필요한지를 고민하기 전에 어떤 객체들이 필요한지 고민하라.
2. 둘째, 객체를 독립적인 존재가 아니라 기능을 구현하기 위해 협력하는 공동체의 일원으로 봐야한다. 
   - 객체의 모양이 잡히면 공통된 특성과 상태를 가진 객체들을 타입으로 분류하고 이 타입을 기반으로 클래스를 구현하라.
   - 훌륭한 협력이 훌륭한 객체를 낳고 훌륭한 객체가 훌륭한 클래스를 낳는다.

<br>

#### 도메인의 구조를 따르는 프로그램 구조 

> "도메인" : 문제를 해결하기 위해 사용자가 프로그램을 사용하는 분야

![](/assets/images/post/200127/(1).png)

###### <center> 도메인을 구성하는 타입들의 구조</center>

- 일반적으로 클래스의 이름은 대응되는 도메인의 개념의 이름과 동일하거나 적어도 유사하게 지어야 함
- 클래스 사이의 관계도 최대한 도메인 개념 사이에 맺어진 관계와 유사하게 만들어야 프로그램의 구조를 이해하기 쉬움

![](/assets/images/post/200127/(2).png)

###### <center> 도메인 개념의 구조를 따르는 클래스 구조</center>

<br>

#### 클래스 구현하기

```java
@AllArgsConstructor
/**
* 상영
*/
public class Screening {
    private Movie movie;
    private int sequence;
    private LocalDateTime whenScreened;
    
    public LocalDateTime getStartTime(){
        return whenScreened;
    }
    
    public boolean isSequence(int sequence){
        return this.sequence == sequence;
    }
    
    public Money getMovieFee(){
        return movie.getFee();
    }
}
```

- 클래스를 사용할 때 가장 중요한 것은 클래스의 경계를 구분짓는 것
- 클래스 내부와 외부를 구분해야 하는 이유는 경계의 명확성이 객체의 자율성을 보장하기 때문 

<br>

**자율적인 객체** 

- 객체는 상태와 행동을 함께 가지는 복합적인 존재
- 객체는 스스로 판단하는 자율적인 존재
- 데이터와 기능을 객체 내부로 함께 묶는 것을 캡슐화라고 부름
- 접근 제어, 접근 수정자( ex : public, private.. )
- 외부에서 접근 가능한 부분 : 퍼블릭 인터페이스
  - public 으로 지정된 메서드
- 오직 내부에서만 접근 가능한 부분 : 구현(implementation)
  - private메서드, protected 메서드, 속성 등
- 인터페이스와 구현의 분리(separation of interface and implementation)
- 일반적으로 객체의 상태는 숨기고 행동만 외부에 공개해야 함

<br>

**프로그래머의 자유**

- 프로그래머의 역할을 "클래스 작성자" 와 "클라이언트 프로그래머" 로 구분하는 것이 유용
- 클래스 작성자는 새로운 데이터 타입을 프로그램에 추가
- 클라이언트 프로그래머는 클래스 작성자가 추가한 데이터 타입 사용
- 구현은닉(implementation hiding) : 접근 제어를 통해 내부 구현을 외부로 부터 감추는 것
- 객체의 변경을 관리할 수 있는 기법중 대표적인 것이 바로 접근 제어 
- 변경될 가능성이 있는 세부적인 구현 내용을 private 영역 안에 감춤으로써 변경으로 인한 혼란을 최소화 

<br>

#### 협력하는 객체들의 공동체 

```java
/**
 * 상영
 */
@AllArgsConstructor
public class Screening {
    private Movie movie;
    private int sequence;
    private LocalDateTime whenScreened;

    public LocalDateTime getStartTime(){
        return whenScreened;
    }

    public boolean isSequence(int sequence){
        return this.sequence == sequence;
    }

    public Money getMovieFee(){
        return movie.getFee();
    }

    /**
     * 영화 예매
     * @param customer 예매자 정보 
     * @param audienceCount 인원수 
     * @return
     */
    public Reservation reserve(Customer customer, int audienceCount){
        return new Reservation(customer, this, calculateFee(audienceCount), audienceCount);
    }

    /**
     * 요금 계산 
     * @param audienceCount
     * @return
     */
    private Money calculateFee(int audienceCount){
        return movie.calculateMovieFee(this).times(audienceCount);
    }
}

/**
 * 금액 
 */
public static class Money {
    public static final Money ZERO = Money.wons(0);
    private final BigDecimal amount;

    public static Money wons(long amount){
        return new Money(BigDecimal.valueOf(amount));
    }
    public static Money wons(double amount){
        return new Money(BigDecimal.valueOf(amount));
    }
     Money(BigDecimal amount){
        this.amount = amount;
    }
    public Money plus(Money amount){
        return new Money(this.amount.add(amount.amount));
    }
    public Money minus(Money amount){
        return new Money(this.amount.subtract(amount.amount));
    }
    public Money times(double percent){
        return new Money(this.amount.multiply(BigDecimal.valueOf(percent)));
    }
    public boolean isLessThan(Money other){
        return amount.compareTo(other.amount) < 0;
    }
    public boolean isGreaterThanOrEqual(Money other){
        return amount.compareTo(other.amount) >=0;
    }
}

/**
 * 예매 
 */
@AllArgsConstructor
public class Reservation {
    private Customer customer;
    private Screening screening;
    private Money fee;
    private int audienceCount;
}
```

- 객체지향의 장점은 객체를 이용해 도메인의 의미를 풍부하게 표현할 수 있다는 것 

> 의미를 좀더 명시적이고 분명하게 표현할 수 있다면 객체를 사용해서 해당 개념을 구현해야 하라. 비록 그 개념이 하나의 인스턴스 변수만을 포함하더라고 개념을 명시적으로 표현하는 것은 전체적인 설계의 명확성과 유연성을 높이는 첫 걸음이다.



![](/assets/images/post/200127/(3).png)

- 시스템의 어떤 기능을 구현하기 위해 객체들 사이에 이뤄지는 상호작용을 협력(Collaboration) 이라고 부름.

<br>

#### 협력에 관한 짧은 이야기 

- 객체는 다른 객체의 인터페이스에 공개된 행동을 수행하도록 "요청(request)" 할 수 있음
- 요청 받은 객체는 자율적인 방법에 따라 요청을 처리한 후 "응답(response)" 함
- 객체가 다른 객체와 상호작용 할 수 있는 유일한 방법은 메세지를 전송 하는 것 뿐
- 다른 객체에게 요청이 도착할때 해당 객체가 메시지를 수신했다고 이야기함
- 수신된 메시지를 처리하기 위한 자신만의 방법 : 메서드

<br>

### 할인 요금 구하기

#### 할인 요금 계산을 위한 협력 시작하기 

```java
@AllArgsConstructor
public class Movie {
    private String title;
    private Duration runningTime;
    private Money fee;
    private DiscountPolicy discountPolicy;
    
    public Money getFee(){
        return fee;
    }

    /**
     * 요금 계산
     * calculateDiscountAmount 에서 할인 요금 반환
     * @param screening
     * @return
     */
    public Money calculateMovieFee(Screening screening){
        return fee.minus(discountPolicy.calculateDiscountAmount(screening));
    }
}
```

위 calculateMovieFee 메서드는 영화 요금을 계산하는 메서드인데 어떤 할인 정책을 사용할 것인지 결정하는 코드가 보이지 않는다. 단지 discountPolicy에게 메시지를 전송항 뿐이다. 

이 코드에는 상속과 다형성의 개념과 그 기반에 추상화라는 원리가 숨겨져 있다. 

<br>

#### 할인 정책과 할인 조건 

```java
/**
 * 할인 정책
 */
public abstract class DiscountPolicy { // DiscountPolicy의 인스턴스 생성이 필요없어 추상 클래스로 구성
    // 하나의 할인 정책은 여러 개의 할인 조건을 포함할 수 있음
    private List<DiscountCondition> conditions = new ArrayList<>();

    public DiscountPolicy(DiscountCondition ... conditions){
        this.conditions = Arrays.asList(conditions);
    }

    public Money calculateDiscountAmount(Screening screening){
        for(DiscountCondition each : conditions){
            if(each.isSatisfiedBy(screening)){
                // 템플릿 메서드 패턴
                return getDiscountAmount(screening);
            }
        }
        return Money.ZERO;
    }

    abstract protected Money getDiscountAmount(Screening screening);

}

public interface DiscountCondition {
    // 인자로 전달된 screening 이 할인이 가능한지 판단하여 리턴
    boolean isSatisfiedBy(Screening screening);
}
```

- 부모 클래스에 기본적인 알고리즘의 흐름을 구현하고 중간에 필요한 처리를 자식클래스에게 위임하는 디자인 패턴을 템플릿 메서드 패턴이라고 부름

<br>

```java
public interface DiscountCondition {
    // 인자로 전달된 screening 이 할인이 가능한지 판단하여 리턴
    boolean isSatisfiedBy(Screening screening);
}
```

```java
/**
 * 순번 할인 조건
 */
@AllArgsConstructor
public class SequenceCondition implements DiscountCondition {
    private int sequence;

    @Override
    public boolean isSatisfiedBy(Screening screening) {
        // screening 의 상영 순번과 일치할 경우 할인 가능
        return screening.isSequence(sequence);
    }
}

/**
 * 기간 할인 조건
 */
@AllArgsConstructor
public class PeriodCondition implements DiscountCondition {
    private DayOfWeek dayOfWeek;
    private LocalTime starTIme;
    private LocalTime endTime;

    /**
     * 상영 시작 시간이 특정 기간 안에 포함되는지 여부 결정
     * @param screening
     * @return
     */
    @Override
    public boolean isSatisfiedBy(Screening screening) {
        return screening.getStartTime().getDayOfWeek().equals(dayOfWeek) &&
                starTIme.compareTo(screening.getStartTime().toLocalTime()) <=0 &&
                endTime.compareTo(screening.getStartTime().toLocalTime()) >=0;
    }
}
```

```java
/**
 * 금액 할인 정책
 */
public class AmountDiscountPolicy extends DiscountPolicy {

    private Money discountAmount;

    public AmountDiscountPolicy(Money discountAmount, DiscountCondition ... conditions){
        super(conditions);
        this.discountAmount = discountAmount;
    }

    @Override
    protected Money getDiscountAmount(Screening screening) {
        return discountAmount;
    }
}

/**
 * 비율 할인 정책
 */
public class PercentDiscountPolicy extends DiscountPolicy {

    private double percent;

    public PercentDiscountPolicy(double percent, DiscountCondition ... conditions){
        super(conditions);
        this.percent = percent;
    }

    @Override
    protected Money getDiscountAmount(Screening screening) {
        return screening.getMovieFee().times(percent);
    }
}
```



![](/assets/images/post/200127/(4).png)

<br>

#### 할인 정책 구성하기 

```java
// 생성자의 파라미터 목록을 이용해 초기화에 필요한 정보를 전달하도록 강제
Movie avator = new Movie("아바타",
        Duration.ofMinutes(120),
        Money.wons(10000),
        new AmountDiscountPolicy(Money.wons(8000),
                new SequenceCondition(1),
                new SequenceCondition(10),
                new PeriodCondition(DayOfWeek.MONDAY, LocalTime.of(10, 0), LocalTime.of(11, 59)),
                new PeriodCondition(DayOfWeek.TUESDAY, LocalTime.of(10,0), LocalTime.of(20, 59))));

Movie titanic = new Movie("타이타닉",
        Duration.ofMinutes(180),
        Money.wons(11000),
        new PercentDiscountPolicy(0.1,
                new PeriodCondition(DayOfWeek.TUESDAY, LocalTime.of(14, 0), LocalTime.of(16, 59)),
                new SequenceCondition(2),
                new PeriodCondition(DayOfWeek.TUESDAY, LocalTime.of(10,0) , LocalTime.of(13, 59))));
```

<br>

### 상속과 다형성

#### 컴파일 시간 의존성과 실행 시간 의존성

![](/assets/images/post/200127/(5).png)

<br>

- Movie클래스가 DiscountPolicy 클래스와 연결
- Movie의 인스턴스는 실행 시에 AmountDiscountPolicy나 PrecentDiscountPolicy의 인스턴스에 의존해야 함
- 코드 수준에서 Movie 클래스는 이 두 클래스 중 어떤 것에도 의존하지 않음
- 오직 추상 클래스 DiscountPolicy에만 의존하고 있음

<br>

```java
// 실행 시에 Movie 는 AmountDiscountPolicy 에 의존 
Movie avator = new Movie("아바타",
        Duration.ofMinutes(120),
        Money.wons(10000),
        new AmountDiscountPolicy(Money.wons(8000), ... ));
```

```java
// 비율 할인 정책 적용 시 PercentDiscountPolicy를 전달하기만 하면 됨 
Movie avator = new Movie("아바타",
        Duration.ofMinutes(120),
        Money.wons(10000),
        new PercentDiscountPolicy(0.1, ... ));
```

> 코드의 의존성과 실행 시점의 의존성이 서로 다를 수 있다는 것이다. 다시 말해 클래스 사이의 의존성과 객체 사이의 의존성은 동일하지 않을 수 있다. 그리고 유연하고, 쉽게 재사용할 수 있으며, 확장 가능한 객체지향 설계가 가지는 특징은 코드의 의존성과 실행 시점의 의존성이 다르다는 것이다. 

<br>


- 코드의 의존성과 실행 시점의 의존성이 다르면 다를 수록 
  - 코드를 이해하기 어려워 짐
  - 디버깅 하기 점점 더 어려워짐
  - 코드는 더 유연해지고 확장 가능해짐
  - 재사용성이 높아짐

훌륭한 객체 지향 설계자로 성장하기 위해서는 항상 유연성과 가독성 사이에서 고민해야 한다. 

<br>

#### 차이에 의한 프로그래밍 

- 상속은 기존 클래스를 기반으로 새로운 클래스를 쉽고 빠르게 추가할 수 있는 간편한 방법을 제공 
- 부모 클래스와 다른 부분만을 추가하여 새로운 클래스를 쉽고 빠르게 만드는 방법을 "차이에 의한 프로그래밍" 이라고 부름

<br>

#### 상속과 인터페이스 

- 상속을 통해 
  - 부모 클래스가 제공하는 모든 인터페이스를 자식 클래스가 물려받을 수 있음
  - 자식 클래스는 자신의 인터페이스에 부모 클래스의 인터페이스를 포함하게 됨
  - 결과적으로 자식 클래스는 부모 클래스가 수신할 수 있는 모든 메시지를 수신할 수 있기때문에 외부 객체는 자식 클래스를 부모 클래스와 동일한 타입으로 간주 할 수 있음 

```java
@AllArgsConstructor
public class Movie {
    /**
     * 요금 계산
     * calculateDiscountAmount 에서 할인 요금 반환
     * @param screening
     * @return
     */
    public Money calculateMovieFee(Screening screening){
        return fee.minus(discountPolicy.calculateDiscountAmount(screening));
    }
}
```

> 자식 클래스는 상속을 통해 부모 클래스의 인터페이스를 물려받기 때문에 부모 클래스 대신 사용될 수 있다. 컴파일러는 코드 상에서 부모 클래스가 나오는 모든 장소에 자식 클래스를 사용하는 것을 허용한다. 

<br>

#### 다형성

- 다형성은 컴파일 시간 의존성과 실행 시간 의존성을 다르게 만들 수 있는 객체지향의 특성을 이용해 서로 다른 메서드를 실행할 수 있게 함 
- Movie는 동일한 메시지를 전송하지만 실제로 어떤 메서드가 실행 될 것인지는 메시지를 수신하는 객체의 클래스가 무엇이냐에 따라 달라짐 
- 다형성이란 동일한 메시지를 수신했을 때 객체의 타입에 따라 다르게 응답할 수 있는 능력을 말함
- 지연 바인딩(lazy binding) or 동적 바인딩(dynamic binding) :  메시지와 메서드를 실행 시점에 바인딩하는 것 
- 초기 바인딩(early binding) or 정적 바인딩(static binding) : 전통적인 함수 호출 처럼 컴파일 시점에 실행될 함수나 프로시저를 결정하는 것 

<br>

**구현 상속과 인터페이스 상속**

상속을 구현상속과 인터페이스 상속으로 나눌 수 있다. 

- 구현 상속 : 서브 클래싱. 코드를 재사용하기 위한 목적으로 상속하는 것.
- 인터페이스 상속 : 서브타이핑. 부모 클래스와 자식 클래스가 인터페이스를 공유할 수 있도록 상속하는 것 

> 상속은 구현상속이 아니라 인터페이스 상속을 위해 사용해야 한다. 

<br>

#### 인터페이스와 다형성 

![](/assets/images/post/200127/(6).png)

<br>

### 추상화와 유연성

#### 추상화의 힘 

![](/assets/images/post/200127/(7).png)

위 다이어그램은 자식 클래스를 생샥한 코드 구조를 그림으로 표현한 것이다. 

- 추상화를 사용할 경우 장점

  - 추상화의 계층만 따로 떼어 놓고 살펴보면 요구사항의 정책을 높은 수준에서 서술할 수 있음
    - 세부적인 내용은 무시한 채 상위 정책을 쉽고 간단히 표현
    - 디자인 패턴이나 프레임워크 모두 추상화를 이용해 상위 정책을 정의함
  - 추상화를 이용하면 설계가 좀더 유연해짐
    - 기존 구조를 수정하지 않고도 새로운 기능을 쉽게 추가하고 확장 할 수 있음

  <br>

#### 유연한 설계

```java
/**
  * 할인 정책이 없을 경우
  */
public class NoneDiscountPolicy extends DiscountPolicy{
	@Override
  	protected Money getDiscountAmount(Screening screening) {
			return Money.ZERO;
    }
}
  
```
```java
Movie startWars = new Movie("스타워즈",
          Duration.ofMinutes(210),
          Money.wons(2000),
          new NoneDiscountPolicy());
```
<br>
- 컨텍스트 독립성 
<br>

![](/assets/images/post/200127/(8).png)
> 유연성이 필요한 곳에 추상화를 사용하라. 

<br>

#### 추상 클래스와 인터페이스 트레이드 오프 

```java
/**
 * 할인 정책
 */
public abstract class DiscountPolicy {
    private List<DiscountCondition> conditions = new ArrayList<>();
  
    public DiscountPolicy(DiscountCondition ... conditions){
        this.conditions = Arrays.asList(conditions);
      }
  
    public Money calculateDiscountAmount(Screening screening){
      for(DiscountCondition each : conditions){
            if(each.isSatisfiedBy(screening)){
                return getDiscountAmount(screening);
            }
      }
      
      return Money.ZERO;
   }
}
```

- 부모 클래스인 DiscountPolicy 에서 할인 조건이 없을 경우 getDiscountAmount 메서드를 호출하지 않음
  - DiscountPolicy 와 NoneDiscountPolicy 를 개념적으로 결합

**개선**

```java
  // DiscountPolicy 클래스를 인터페이스로 변경
  public interface DiscountPolicy {
      Money calculateDiscountAmount(Screening screening);
  }
  
  public abstract class DefaultDiscountPolicy implements DiscountPolicy {
      ...
  }
  
  public class NoneDiscountPolicy implements DiscountPolicy{
  
      @Override
      public Money calculateDiscountAmount(Screening screening) {
          return Money.ZERO;
      }
}
```

![](/assets/images/post/200127/(9).png)

어느 설계가 더 좋은가?

- 구현과 관련된 모든 것들이 트레이드오프의 대상이 될 수 있다는 사실
- 고민하고 트레이드 오프하라.

<br>

#### 코드 재사용

코드의 재사용을 위해서는 상속보다 합성(composition) 이 더 좋은 방법이다.

> "합성"은 다른 객체의 인스턴스를 자신의 인스턴스 변수로 포함해서 재사용 하는 방법을 말한다. 

<br>

#### 상속

- 객체 지향에서 코드를 재사용 하기 위해 널리 사용하는 기법
- 단점
  - 캡슐화를 위반
    - 부모 클래스의 구현이 자식 클래스에 노출
    - 부모 클래스를 변경 할때 자식 클래스도 함께 변경될 확률 높음
  - 설계를 유연하지 못하게 만듬
    - 부모 - 자식 클래스 사이의 관계를 컴파일 시점에 결정

<br>

#### 합성

인터페이스에 정의된 메세지를 통해서만 코드를 재사용하는 방법 : 합성

- 구현을 효과적으로 캡슐화 
- 의존하는 인스턴스의 교체가 쉬어 설계를 유연하게 만듬
- 메시지를 통해 느슨하게 결합

따라서 코드의 재사용을 위해서는 상속보다는 합성을 선호하는 것이 더 좋은 방법이다. 다만 대부분의 설계에서는 상속과 합성을 함꼐 사용해야 한다. 


> 객체 지향설계의 핵심은 적절한 협력을 식별하고 협력에 필요한 역할을 정의 한 후에 역할을 수행할 수 있는 적절한 객체에게 책임을 할당하는 것이다.

<br>