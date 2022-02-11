---
layout: post
title: "합성과 유연한 설계"
date: 2020-04-06:10:22:10 +0900
tags: [OBJECTS]
categories: objects
---

### "OBJECTS 11장"

---

- 상속
  - 부모 클래스 재사용
  - 의존성이 컴파일 타임에 해결
  - is-a 관계
  - 정적인 관계
  - 화이트박스 재사용
  <!-- more -->
  
- 합성
  - 부분 객체의 코드 재사용
  - 의존성이 런타임에 해결
  - has-a 관계
  - 구현에 의존하지 않음
  - 동적인 관계
  - 블랙박스 재사용

<br>

### 상속을 합성으로 변경하기 

```java
public class InstrumentedHashSet<E> implements Set<E> {
    private int addCount = 0;
    private Set<E> set;

    public InstrumentedHashSet(Set<E> set) {
        this.set = set;
    }
		// 포워딩 메서드
    @Override public int hashCode() {
        return set.hashCode();
    }

    @Override public Spliterator<E> spliterator() {
        return set.spliterator();
    }
  
  ...
    
}
```

- 포워딩(forwarding)
- 포워딩 메서드(forwarding maethod)

<br>

### 상속으로 인한 조합의 폭발적인 증가 

#### 기본 정책과 부가 정책의 종류

![](1.png)

<br>

#### 상속을 이용해서 기본 정책 구현하기

#### 기본 정책에 세금 정책 조합하기

```java
public abstract class Phone {
    private List<Call> calls = new ArrayList<>();

    public Money calculateFee() {
        Money result = Money.ZERO;

        for(Call call : calls) {
            result = result.plus(calculateCallFee(call));
        }

        return afterCalculated(result);
    }

    protected Money afterCalculated(Money fee) {
        return fee;
    }

    protected abstract Money calculateCallFee(Call call);
}
```

- 부모 클래스에 추상 메서드를 추가하면 모든 자식 클래스들이 추상 메서드를 오버라이딩 해야함
- 해결책 : 훅 메서드(hook method)
  - 추상 메서드와 동일하게 자식 클래스에서 오버라이딩할 의도로 메서드를 추가하지만 기본 구현을 제공 하는 메서드

<br>

#### 기본 정책에 기본 요금 할인 정책 조합하기 

![](2.png)

<br>

#### 중복 코드의 덫에 걸리다

- 상속을 이용한 해결 방법은 모든 가능한 조합 별로 자식클래스를 하나씩 추가 하는 것 

![](3.png)

- 클래스 폭발 / 조합의 폭발
  - 상속의 남용으로 하나의 기능을 추가하기 위해 필요 이상으로 많은 수의 클래스를 추가해야 하는 경우 
  - 컴파일 타임에 결정된 자식 클래스와 부모 클래스 사이의 관계가 변경될 수 없음
  -  조합의 수 많큼 새로운 클래스를 추가해야함
- 해결책 : 상속의 포기

<br>

### 합성 관계로 변경하기 

- 합성은 퍼블릭 인터페이스에 대해서만 의존

<br>

#### 기본 정책 합성하기

![](4.png)

```java
public class Phone {
    private RatePolicy ratePolicy;
    private List<Call> calls = new ArrayList<>();

    public Phone(RatePolicy ratePolicy) {
        this.ratePolicy = ratePolicy;
    }

    public List<Call> getCalls() {
        return Collections.unmodifiableList(calls);
    }

    public Money calculateFee() {
        return ratePolicy.calculateFee(this);
    }
}

public abstract class BasicRatePolicy implements RatePolicy {
    @Override
    public Money calculateFee(Phone phone) {
        Money result = Money.ZERO;

        for(Call call : phone.getCalls()) {
            result.plus(calculateCallFee(call));
        }

        return result;
    }

    protected abstract Money calculateCallFee(Call call);
}

public class RegularPolicy extends BasicRatePolicy {
    private Money amount;
    private Duration seconds;

    public RegularPolicy(Money amount, Duration seconds) {
        this.amount = amount;
        this.seconds = seconds;
    }

    @Override
    protected Money calculateCallFee(Call call) {
        return amount.times(call.getDuration().getSeconds() / seconds.getSeconds());
    }
}
```

<br>

#### 부가 정책 적용하기

![](5.png)

```java
public abstract class AdditionalRatePolicy implements RatePolicy {
    private RatePolicy next;

    public AdditionalRatePolicy(RatePolicy next) {
        this.next = next;
    }

    @Override
    public Money calculateFee(Phone phone) {
        Money fee = next.calculateFee(phone);
        return afterCalculated(fee) ;
    }

    abstract protected Money afterCalculated(Money fee);
}

public class RateDiscountablePolicy extends AdditionalRatePolicy {
    private Money discountAmount;

    public RateDiscountablePolicy(Money discountAmount, RatePolicy next) {
        super(next);
        this.discountAmount = discountAmount;
    }

    @Override
    protected Money afterCalculated(Money fee) {
        return fee.minus(discountAmount);
    }
```

<br>

#### 기본 정책과 부가 정책 합성하기 

```java
Phone phone = new Phone(new TaxablePolicy(0.05,
        new RateDiscountablePolicy(Money.wons(10000),
                new RegularPolicy(...))));
```

<br>

#### 새로운 정책 추가하기 

![](6.png)



#### 객체 합성이 클래스 상속보다 더 좋은 방법이다

- 상속은 구현상속과 인터페이스 상속으로 나누어짐
- 이번장의 모든 단점은 구현상속에 국한
- 합성 : 코드를 재사용하면서 건전한 결합도를 유지하는 더 좋은 방법

<br>

### 믹스인

- 객체를 생성할 때 코드 일부를 클래스 안에 섞어 넣어 재사용 하는 기법
- 컴파일 시점에 필요한 코드 조각을 조합
- 코드를 다른 코드 안에 섞어 넣기 위한 방법
- 유연하게 관계를 재구성
- 상속은 정적, 믹스인은 동적
- 실제로 트레이트를 믹스인 하는 시점에 가서야만 믹스인 할 대상을 결정
- 추상서브클래스
- 쌓을 수 있는 변경

<br>

#### 믹스인 in 자바

- 클래스가 자신의 "본래타입"에 추가하여 구현할 수 있는 타입
- 선택 가능한 기능을 제공하며, 그 기능을 제공 받고자 하는 클래스에서 선언
- 인터페이스는 믹스인(mixin)을 정의하는데 이상적
> 예를 들어, Comparable은 믹스인 인터페이스로써, 상호 비교 가능한 다른 객체와의 비교를 통해 클래스의 인스턴스가 정렬된다는 것을 그 클래스에서 선언 할 수 있다. 그런 인터페이스를 믹스인이라고 부르는 이유는, 어떤 타입의 본래 기능에 선택 가능한 기능을 "섞는(mixed in)"것이 가능하기 때문이다. 기존 클래스가 추상 클래스로부터 상속받기 위해 개조될 수 없는 것과 같은 이유로, 추상클래스는 믹스인을 정의하는데 사용될 수 없다. 즉, 클래스는 하나 이상의 수퍼 클래스를 가질 수 없으므로, 클래스 상속 계층에 믹스인을 끼워 넣을 만한 마땅한 곳이 없기 때문이다.

<br>

```java
public interface Singer{
    AudioClip sing(Song s);
}

public interface Songwriter{
    Song compose(boolean hit);
}

public interface SingerSongwriter extends Singer,Songwriter{
    AudioClip strum();
    void actSensitive();
}
```

<br>

<br>

###### 출처 <br/>

###### [https://hashcode.co.kr/questions/1131/....][link01] <br/>
[link01]: https://hashcode.co.kr/questions/1131/%EC%B6%94%EC%83%81%ED%81%B4%EB%9E%98%EC%8A%A4%EB%A7%90%EA%B3%A0-%EC%9D%B8%ED%84%B0%ED%8E%98%EC%9D%B4%EC%8A%A4%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EB%9D%BC%EB%8A%94%EB%8D%B0-%EC%9D%B4%EC%9C%A0%EA%B0%80-%EB%AD%94%EA%B0%80%EC%9A%94
