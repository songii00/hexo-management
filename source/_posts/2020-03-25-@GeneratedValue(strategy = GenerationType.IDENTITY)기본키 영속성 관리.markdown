---
layout: post
title: "@GeneratedValue(strategy = GenerationType.IDENTITY) : 기본키 영속성 관리"
date: 2020-03-25:17:00:32 +0900
tags: [JPA]
categories: jpa
subtitle : 
feature-img: "assets/img/banner.jpg"
---

### "JPA"
---

### 기본키 매핑

JPA 는 크게 직접 할당과 자동생성 으로 기본키를 매핑한다. 여기서 살펴볼 애노테이션은 @GeneratedValue(strategy = GenerationType.IDENTITY) 이다.

<br>

- 직접 할당: @Id만 사용

- 자동 생성(@GeneratedValue)
  - IDENTITY : 데이터베이스에 위임(MYSQL)
      - Auto_Increment 
  - SEQUENCE : 데이터베이스 시퀀스 오브젝트 사용(ORACLE)
    - @SequenceGenerator 필요
  
  <!-- more -->
  
  - TABLE : 키 생성용 테이블 사용, 모든 DB에서 사용
      - @TableGenerator 필요
  - AUTO : 방언에 따라 자동 지정, 기본값

<br>

### @GeneratedValue(strategy = GenerationType.IDENTITY) 

일단 사용법은 간단하다. 기본키 생성을 DB에 위임하도록 어노테이션을 달아주면 끝이다.

<br>

#### 상품 엔티티

```java
/**
 * 상품 
 */
@Entity
public class Product {

    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long productId;

    /**
     * 상품명
     */
    private String productName;
    /**
     * 가격
     */
    private int price;

    public Product(){}

    public Product(String productName, int price){
        this.productName = productName;
        this.price = price;
    }
}
```

<br>

#### 실행 코드

```java
transaction.begin();

Product a = new Product("A", 1000);
Product b = new Product("B", 2000);

entityManager.persist(a);
entityManager.persist(b);

transaction.commit();
```

<br>

다음과 같이 실행된다면 우리는 DB 상에서 제대로 값이 들어가진 것을 확인 할 수 있다. 

![](/assets/images/post/200325/(1).png)

<br>

### 영속성 컨텍스트는 어떻게 관리되고 있을까?

그런데 Auto_Increment 로 생성된 값은 직접 DB에 인서트 되고 나서야 알수 있다. 

그럼 commit 하기 전에는 자동생성된 기본키 값을 사용하지 못하는 걸까?

<br>

결과부터 말하자면 `사용할 수 있다`

<br>

위 물음을 해결하기 위해서는 JPA 의 동작방식에 관한 세가지 사실을 먼저 생각해보자.

![](/assets/images/post/200325/(2).png)

<br>

- 영속성 컨텍스트로 관리하려면 무조건 pk 값이 필요
- JPA 는 entityManager.persist() 을 호출함으로써 영속성 컨텍스트의 쓰기 지연 SQL 저장소에 insert 쿼리를 저장
- transaction.commit() 이 호출된 후, DB에 insert 쿼리를 날림

이러한 사실에 근거하여 아래와 같은 코드를 실행시키면 콘솔창에서 어떤 화면을 볼수 있을까? commit 하기 전에 productId를 가져왔으니 a와 b의 productId는 null 이될까? JPA는 영속성 컨텍스트를 어떻게 관리하고 있을까?

```java
transaction.begin();

Product a = new Product("A", 1000);
Product b = new Product("B", 2000);

System.out.println("///////////////////");
entityManager.persist(a);
System.out.println("a product id : " + a.getProductId());
entityManager.persist(b);
System.out.println("b product id : " + b.getProductId());
System.out.println("///////////////////");

transaction.commit();
```

<br>

#### 실행화면

콘솔창에 출력되는 화면은 아래와 같다. 

```xml
///////////////////
Hibernate: 
    /* insert hellojpa.Product
        */ insert 
        into
            Product
            (productId, price, productName) 
        values
            (null, ?, ?)
a product id : 1
Hibernate: 
    /* insert hellojpa.Product
        */ insert 
        into
            Product
            (productId, price, productName) 
        values
            (null, ?, ?)
b product id : 2
///////////////////
```

<br>

정상적으로 productId 값이 출력되어 짐을 볼 수 있다. JPA 는 @GeneratedValue(strategy = GenerationType.IDENTITY) 에 한하여 플로우를 변경한다. 

<br>

### 결론

@GeneratedValue(strategy = GenerationType.IDENTITY) 사용했을 때,

- transaction.commit()을 호출할 때가 아니라 entityManager.persist() 가 호출될 때 바로 DB에 insert 쿼리 를 날림
- JPA 내부에서 Insert 쿼리 실행 후 바로 생성된 productId 값을 리턴 받음
- productId 를 PK 로 영속성 컨텍스트에 저장 

<br>

