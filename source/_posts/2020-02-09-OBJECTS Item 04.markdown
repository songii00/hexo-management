---

layout: post
title: "설계 품질과 트레이드오프"
date: 2020-02-09:10:25:43 +0900
tags: [OBJECTS]
categories: objects
subtitle : 
feature-img: "assets/img/banner.jpg"
---

### "OBJECTS 4장"
---

책임 주도 설계라는 이름에서 알 수 있는 것처럼 역할, 책임, 협력 중에서 가장 중요한 것은 '책임' 이다. 결국 책임이 객체지향 애플리케이션 전체의 품질을 결정하는 것이다.

> 객체지향 설계란 올바른 객체에게 올바른 책임을 할당하면서 낮은 결합도와 높은 응집도를 가진 구조를 창조하는 활동이다.

<br>

- 훌륭한 설계 : 합리적인 비용안에서 변경을 수용할 수 있는 구조를 만드는 것 
- 응집도가 높고, 서로 느슨하게 결합되 있는 요소로 구성 
- 객체의 상태가 아니라 행동에 초점을 맞춰야함

<br>

<!-- more -->

### 데이터 중심의 영화 예매 시스템 

- 객체지향 설계에서 시스템 분할 방법
  - 상태를 분할의 중심축으로 삼는 방법
    - 객체의 상태는 구현에 속함
    - 불안정하고 변하기 쉬움
    - 변경에 취약
  - 책임을 분할의 중심축으로 삼는 방법
    - 책임은 인터페이스에 속함
    - 변경에 안정적인 설계 

<br>

#### 데이터를 준비하자

- 객체 내부에 저장되는 데이터를 기반으로 시스템을 분할하는 방법
- 데이터가 무엇인가?

```java
public class Movie {
    /**
     * 영화 제목 
     */
    private String title;
    /**
     * 상영시간 
     */
    private Duration runningTime;
    /**
     * 기본 요금 
     */
    private Money fee;
    /**
     * 할인 조건의 목록 
     * movieType 에 따라 할인정책의 종류가 달라짐
     */
    private List<DiscountCondition> discountConditions;
    /**
     * 할인 정책 종류 
     */
    private MovieType movieType;
    /**
     * 할인 금액 
     */
    private Money discountAmount;
    /**
     * 할인 비율 
     */
    private double discountPercent;
    
}
```

```java
public enum  MovieType {
    AMOUNT_DISCOUNT, // 금액 할인 정책
    PERCENT_DISCOUNT, // 비율 할인 정책
    NONE_DISCOUNT // 미적용
}
```

- 객체가 포함해야하는 데이터에 집중

```java
@Getter
@Setter
public class Movie {
...
}
```

- 내부의 데이터를 반환하는 접근자(accessor) 와 데이터를 변경하는 수정자(mutator) 를 추가
- 내부 데이터가 객체를 빠져나가 외부의 다른 객체들을 오염시키는 것을 막기 위해 getter, setter 추가
- 캡슐화를 달성할 수 있는 가장 간단한 방법

```java
@Getter
@Setter
public class DiscountCondition {
    /**
     * 할인 조건 종류 
     */
    private DiscountConditionType type;
    /**
     * 상영 순번 
     */
    private int sequence;
    /**
     * 요일 
     */
    private DayOfWeek dayOfWeek;
    /**
     * 시작 시간 
     */
    private LocalTime startTime;
    /**
     * 종료 시간 
     */
    private LocalTime endTime;

}

public enum DiscountConditionType {
    SEQUENCE, // 순번 조건
    PERIOD // 기간 조건 
}
```

```java
@Getter
@Setter
public class Screening {
    private Movie movie;
    private int sequence;
    private LocalDateTime whenScreened;
    
}

/**
 * 영화 예매 
 */
@AllArgsConstructor
@Getter
@Setter
public class Reservation {
    private Customer customer;
    private Screening screening;
    private Movie fee;
    private int audienceCount;
}

@AllArgsConstructor
public class Customer {
    private String name;
    private String id;
}
```

![](/assets/images/post/200209/(1).png)

<br>

#### 영화를 예매하자

```java
public class ReservationAgency {
    /**
     * 영화 예매 
     */
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

        // 할인 정책에 따라 예매 요금 계산 
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

### 설계 트레이드 오프

> 캡슐화, 응집도, 결합도

<br>

#### 캡슐화

- 변경될 가능성이 높은 부분 : 구현
- 상대적으로 안정적인 부분 : 인터페이스
- 구현과 인터페이스를 분리하고 외부에서는 인터페이스에만 의존하도록 관계를 조절해야 함
- 객체지향의 가장 중요한 원리는 캡슐화 : 대상을 단순화하는 추상화의 한 종류
- 복잡성을 다루기 위한 가장 효과적인 도구 

> 캡술화란 변경 가능성이 높은 부분을 객체 내부로 숨기는 추상화 기법이다.

<br>

#### 응집도와 결합도 

- 응집도 : 모듈에 포함된 내부 요소들이 연관돼 있는 정도 
  - 변경이 발생할 때 모듈 내부에서 발생하는 변경의 정도

![](/assets/images/post/200209/(2).png)

- 결합도 : 의존성의 정도
  - 다른 모듈에 대해 얼마나 많은 지식을 갖고 있는지를 나타내는 척도
  - 한 모듈이 변경되기 위해 다른 모듈의 변경을 요구하는 정도
  - 내부 구현을 변경했을 때 다른 모듈에 영향을 미치는 경우 > 두 모듈 사이의 결합도가 높음
  - 퍼블릭 인터페이스를 수정했을 때만 다른 모듈에 영향을 미치는 경우 > 결합도가 낮음
  - 클래스의 구현이 아닌 인터페이스에 의존하도록 코드를 작성 
  - "인터페이스에 대해 프로그래밍 하라"

![](/assets/images/post/200209/(3).png)

> 좋은 설계란 높은 응집도와 낮은 결합도를 가진 모듈로 구성된 설계를 의미한다. 즉, 오늘의 기능을 수행하면서 내일의 변경을 수용할 수 있는 설계다.

<br>

- 캡슐화의 정도가 응집도와 결합도에 영향을 미침

<br>

### 데이터 중심의 영화 예매 시스템의 문제점 

#### 캡슐화 위반

```java
public class Movie { 
	private Money fee;
  private Money getFee(){
     return fee; }
  public void setFee(Money fee){
     this.fee = fee; }
}
```

- 접근자와 수정자 메서드는 객체 내부의 상태에 대한 어떤 정보도 캡슐화하지 못함
- getFee 메서드와 setFee 메서드는 Movie 내부에 Money 타입의 fee라는 이름의 인스턴스 변수가 존재한다는 사실을 퍼블릭 인터페이스에 노골적으로 드러냄
- 접근자와 수정자에 과도하게 의존하는 설계 방식 : 추측에 의한 설계 전략(design-by-guessing strategy)

<br>

#### 높은 결합도 

- 결합도 측면에서 데이터 중심 설계가 가지는 단점
  - 여러 데이터 객체들을 사용하는 제어 로직이 특정 객체 안에 집중되기 때문에 하나의 제어 객체가 다수의 데이터 객체에 강하게 결합
  - 어떤 데이터 객체를 변경하더라도 제어 객체를 함께 변경 해야 함

![](/assets/images/post/200209/(4).png)

#### 낮은 응집도 

<br>

### 자율적인 객체를 향해 

### 캡슐화를 지켜라

- 캡슐화는 설계의 제 1원리
- 객체는 스스로의 상태를 책임져야 하며 외부에서는 인터페이스에 정의된 메서드를 통해서만 상태에 접근할 수 있어야 함

> 여기서 말하는 메서드는 단순히 속성 하나의 값을 반환하거나 변경하는 접근자나 수정자를 의미하는 것은 아니다. 객체에게 의미있는 메서드는 객체가 책임져야 하는 무언가를 수행하는 메서드다. 속성의 가시성을 private 으로 설정했다고 해도 접근자와 수정자를 통해 속성을 외부로 제공하고 있다면 캡슐화를 위반하는 것이다. 

<br>

```java
class Rectangle {
    private int left;
    private int right;
    private int top;
    private int bottom;
  	// 자신의 크기를 스스로 증가시키도록 책임을 이동
  	// 객체가 자기 스스로를 책임짐
    public void enlarge(int multiple){
        right *= multiple;
        bottom *= multiple;
    }
}
```

<br>

#### 스스로 자신의 데이터를 책임지는 객체 

- 객체는 단순한 데이터 제공자가 아님
- 객체 내부에 저장되는 데이터보다 객체가 협력에 참여하면서 수행할 책임을 정의하는 오퍼레이션이 더 중요 
- 이 객체가 어떤 데이터를 포함해야 하는가? + 이 객체가 데이터에 대해 수행해야 하는 오퍼레이션은 무엇인가?

<br>

```java
public class DiscountCondition {
	// 이 데이터에서 수행할 수 있는 오퍼레이션은 무엇인가?
  // 할인 조건 판단 
  public boolean isDiscounttable(DayOfWeek dayOfWeek, LocalTime time){
            if(type != DiscountConditionType.PERIOD){
                throw new IllegalArgumentException();
            }
            return this.dayOfWeek.equals(dayOfWeek)
                    && this.startTime.compareTo(time) <= 0
                    && this.endTime.compareTo(time) >=0;
  }
  public boolean isDiscountalbe(int sequence){
    if(type != DiscountConditionType.SEQUENCE){
      throw new IllegalArgumentException();
    }
    return this.sequence == sequence;
  }
}
```

```java
public class Movie {
  public Money calculateAmountDiscountedFee(){
    if(movieType != MovieType.AMOUNT_DISCOUNT){
      throw new IllegalArgumentException();
    }
    return fee.minus(discountAmount);
  }
  public Money calculatePercentDiscountedFee(){
    if(movieType != MovieType.PERCENT_DISCOUNT){
      throw new IllegalArgumentException();
    }
    return fee.minus(fee.times(discountAmount));
  }
  public Money calculateNoneDiscountedFee(){
    if(movieType != MovieType.NONE_DISCOUNT){
      throw new IllegalArgumentException();
    }
    return fee;
  }
  // 할인 여부 판단
  public boolean isDiscountable(LocalDateTime whenScreened, int sequence){
            for(DiscountCondition condition : discountConditions){
                // 기간 조건
                if(condition.getType() == DiscountConditionType.PERIOD){
                    if(condition.isDiscountable(whenScreened.getDayOfWeek(), whenScreened.toLocalTime())){
                        return true;
                    }
                }
                // 순번 조건 
                else {
                    if(condition.isDiscountable(sequence)){
                        return true;
                    }
                }
            }
            return false;
        }
}
```

```java
public class Screening {
   public Money calculateFee(int audienceCount){
     switch (movie.getMovieType()) {
       case AMOUNT_DISCOUNT:
         if (movie.isDiscountable(whenScreened, sequence))
           return movie.calculateAmountDiscountedFee().times(audienceCount);
         break;
       case PERCENT_DISCOUNT:
         if (movie.isDiscountable(whenScreened, sequence))
           return movie.calculatePercentDiscountedFee().times(audienceCount);
         break;
       case NONE_DISCOUNT:
         return movie.calculateNoneDiscountedFee().times(audienceCount);
     }
     return movie.calculateNoneDiscountedFee().times(audienceCount);
   }
}
```

```java
public class ReservationAgency {
   Money fee = screening.calculateFee(audienceCount);
          return new Reservation(customer, screening, fee, audienceCount);
}
```

![](/assets/images/post/200209/(5).png)

<br>

### 하지만 여전히 부족하다

#### 캡슐화 위반

- 파급효과
- 내부 구현을 인터페이스에 노출시킴
- 캡슐화가 단순히 객체 내부의 데이터를 외부로부터 감추는 것 이상의 의미를 가짐
- 사실 캡슐화는 변경될 수 있는 어떤 것이라도 감추는 것을 의미
- 내부 속성을 외부로부터 감추는 것은 데이터 캡슐화라고 불리는 캡슐화의 한 종류

<br>

#### 높은 결합도 

#### 낮은 응집도

<br>

### 데이터 중심 설계의 문제점

- 데이터 중심의 설계 본질적으로 너무 이른 시기에 데이터에 관해 결정하도록 강요
- 협력이라는 문맥을 고려하지 않고 객체를 고립시킨 채 오퍼레이션을 결정

<br>

#### 데이터 중심 설계는 객체의 행동보다는 상태에 초점을 맞춘다

- 데이터 중심 설게 방식에 익숙한 개발자들은 일반적으로 데이터와 기능을 분리하는 절차적 프로그래밍 방식을 따름

- 첫번째 설계가 실패한 이유

  - 데이터 중심의 관점에서 객체는 그저 단순한 데이터 집합체

  - 과도한 접근자와 수정자 추가

  - 접근자와 수정자는 public 속성과 큰 차이가 없어 캡슐화가 무너짐

- 두번째 설계가 실패한 이유

  - 데이터를 처리하는 작업과 데이터를 같은 객체 안에 두는 것이 목적이 아님
  - 데이터에 관한 지식이 객체의 인터페이스에 고스란히 드러남
  - 캡슐화 실패, 변경에 취약

- 데이터 중심의 설계가 너무 이른 시기에 데이터에 대해 고민하기 떄문에 캡슐화에 실패

<br>

#### 데이터 중심 설계는 객체를 고립시킨 채 오퍼레이션을 정의하도록 만든다

- 올바른 객체지향 설게의 무게 중심은 항상 객체의 내부가 아니라 외부에 맞춰져 있어야 함
- 중요한 것은 객체가 다른 객체와 협력하는 방법

> 안타깝게도 데이터 중심 설게에서 초점은 객체의 외부가 아니라 내부로 향한다. 실행 문맥에 대한 깊이 있는 고민 없이 객체가 관리할 데이터의 세부 정보를 먼저 결정한다. 객체의 구현이 이미 결정된 상태에서 다른 객체와의 협력 방법을 고민하기 때문에 이미 구현된 객체의 인터페이스를 억지로 끼워맞출 수밖에 없다.

<br>