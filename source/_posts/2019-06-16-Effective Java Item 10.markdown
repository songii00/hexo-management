---
layout: post
title:  "Effective Java / Item 10"
date:   2019-06-16 12:27:24 +0900
categories: effective java
tags: [effective java]
subtitle : Item 10. equals는 일반 규약을 지켜 재정의하라.
feature-img: "assets/img/banner.jpg"
---
#### Equals 재정의 해? 말아?    
우리는 equals 메서드를 재정의 해야할까? 아니면 하지 말아야할까? Effective Java에서는 다음과 같이 말한다. 다음의 상황 중 하나의 상황에 해당한다면 재정의 하지 않는 것이 최선이라고 말이다.

<br>

<!-- more -->

##### 재정의 하지 말아야 할 때

- 각 인스턴스가 본질적으로 고유하다. 

주로 값을 표현하는게 아니라 동작하는 것을 표현하는 클래스가 여기에 해당된다. Thread가 좋은 예로, Object의 equals 메서드는 이러한 클래스에 딱 맞게 구현되어 있다.

- 인스턴스의 논리적 동치성(logical equality)을 검사할 일이 없다. 

예를들어 java.util.regex.Pattern에서 eqauls을 재정의해서 정규표현식이 같은지 검사하는, 즉 논리적 동치성을 검사할 수 있지만 클라이언트가 원하지 않거나, 애초에 필요없다고 판단된다면 equals를 재정의 하지 않고 기본 Object의 equals를 사용한다.

- 상위 클래스에서 재정의한 equals가 하위 클래스에서도 같은 상황이다.

Set 구현체인 AbstractSet이나 List의 구현체인 AbstractSet 처럼 구현한 equals 를 상속받아 쓰는 경우가 존재한다.

- 클래스가 private 이거나, package-private 이고 equals 메서드를 호출할 일이 없다. 

```java

@Override public boolean equals(Object o){
    throw new AssertionError(); //호출 금지
}

```

- 값이 같은 인스턴스가 2개이상 안 만들어진다는 보장이 있다.
예를들면 인스턴스 통제 클래스(싱글턴 등), Enum 등이 여기에 속한다. 어차피 논리적으로 같은 인스턴스가 2개 이상 만들어지지 않으니 논리적 동치성과 객체 식별성이 사실상 똑같은 의미가 된다. 따라서 Object의 equals가 논리적 동치성까지 확인해준다고 볼수 있다.

<br>

##### 재정의 해야할 때

equals 재정의 해야할 때가 있을까? 바로 다음과 같을 때이다.

> equals 메소드는 '메모리주소를 기반으로 물리적으로 같은가?' 즉 객체의 식별성(object identity) 가 아니라 논리적 동치성(logical equality)를 비교해야할 때 재정의하면 좋다. 주로 값 클래스들에 해당된다. 다시 말하자면 객체가 같은지가 중요한게 아니라, 객체 내 값이 같은지 비교해야할 때 재정의해야한다.
  (Map의 키, Set의 원소 등으로 사용하려면 재정의해야한다.)

<br>

#### equals 메서드 재정의 시 지켜야 할 다섯가지 규약

<br>

##### 반사성(reflexivity) 

> null이 아닌 모든 참조 값 x에 대해 x.equals(x)는 true 이다.

즉, 객체는 자기 자신과 같다는 뜻이다. 

<br>

##### 대칭성(symmetry)

> null이 아닌 모든 참조값 x,y에 대해 x.equals(y)가 true 이면 y.equals(x)도 true 이다.

두 객체는 서로에 대한 동치 여부에 똑같은 결과를 낸다. 

<br>

```java

public final class CaseInsensitiveString(){
    private final String s;

    public CaseInsensitiveString(String s){
        this.s = Objects.requireNonNull(s);
    }
    
    //equalsIgnoreCase은 대소문자 구별없이 문자열 equals 검사
    @Override public boolean equals(Object o) {
        if (o instance of CaseInsensitiveString)
            return s.equalsIgnoreCase(
                ((CaseInsensitiveString) o).s);
        
        if(o instanceof String)
            return s.equalsIgnoreCase((String) o);
        
        return false;
    }
    
    ... // 나머지 
}

```

###### <center> [코드 10-6] 잘못된 코드 - 대칭성 위배! </center>

<br>

```java

CaseInsensitiveString cis = new CaseInsensitiveString("Polish");
String s = "polish";

```

<br>

CaseInsensitiveString과 일반 String 객체가 하나씩 있다고 했을 때, cis.equals(s)는 true를 반환한다.

하지만 CaseInsensitiveString의 equals는 String을 알고 도록 개발자가 설계 했지만 String equals는 CaseInsensitiveString
의 존재를 모르는데 있다. 따라서 s.equals(cis)는 false 를 반환하여 대칭성을 위반한다는 것이다. 

이를 해결하려면 어떻게 해야할까? 두 클래스의 equals 메서드를 연동하겠다는 생각을 버리면 된다. 

<br>

```java
@Override public boolean equals(Object o) {
        return o instanceof CaseInsensitiveString && ((CaseInsensitiveString) o).s.equalsIgnoreCase(s);      
}

```

<br>

##### 추이성(transitivity)

> null이 아닌 모든 참조값 x,y에 대해 x.equals(y)가 true이고 y.equals(z)도 true이면, x.equals(z)도 true이다. 

첫번째 객체와 두번째 객체가 같고, 두번째 객체와 세번째 객체가 같으면, 첫번째 객체와 세번째 객체도 같아야 한다는 뜻이다. 이 규약은 상위클래스에 없는 새로운 필드를 하위 클래스에 추가하는 상황 같은 곳에서 어기기 쉽다.

> ※instanceof 연산자 <br><br>
  이 연산자는 반드시 상속관계일때 사용되야 하며 다음과 같이 인스턴스 타입을 체크하여 다운캐스팅 등의 작업을 수행할 수 있게 해준다. 
  자녀객체 instanceof 부모객체 == true
  부모객체 instanceof 자녀객체 == false
  객체 instanceof 객체타입 ==true

<br>

다음은 2차원에서의 점을 표현하는 클래스를 예로 들어보자.

```java

public class Point { 
    private int x; 
    private int y; 

    public Point(int x, int y) {
         this.x = x; 
        this.y = y; 
    }
     
    public boolean equals(Object o) {
        if (!(o instanceof Point)) {
            return false; 
           } 
        Point p = (Point) o; 
        return p.x == x && p.y == y; 
    } 
} 

public class ColorPoint extends Point { 
    private Color color;
    public ColorPoint(int x, int y, Color color) {
        super(x, y); 
        this.color = color; 
    } 

```

<br>

그대로 둔다면 Point 구현이 상속되어 색상정보는 무시한채 비교를 수행한다. equals 규칙을 어긴건 아니지만 색상정보를 놓치게 되니 받아들일 수 없다. 

<br>

```java

public boolean equals(Object o) {
         if (!(o instanceof ColorPoint)) {
             return false; 
        } 
        return super.equals(o) && ((ColorPoint) o).color == color;    
} 

```
###### <center> [코드 10-7] 잘못된 코드 - 대칭성 위배! </center>

<br>

위의 코드는 비교대상이 또다른 ColorPont이고 위치와 색상이 같을때만 true를 반환한다. 이는 Point를 ColorPoint에 비교한 결과와 그 둘을 바꿔 비교한 결과가 다르다. Point의 equals은 색상을 무시하고, ColorPoint의 equals는 매개변수의 클래스 종류가 다르다며 매번 false를 반환할 것이다.

<br>

```java

ColorPoint cp = new ColorPoint(1, 2, Color.RED);
Point p = new Point(1, 2); 

p.equals(cp); //true
cp.equals(p); //false

```

<br>

그렇다면 ColorPoint.equals가 Point와 비교될때 색상을 무시하도록 하면 해결될까?

<br>

```java

public boolean equals(Object o) {
         if (!(o instanceof Point)) {
             return false; 
        } 
        else if (!(o instanceof ColorPoint)) {
        // o가 일반 Point면 색상을 무시하고 비교한다. 
            return o.equals(this); 
        } 
        // o가 ColorPoint면 색상까지 비교한다. 
        return super.equals(o) && ((ColorPoint) o).color == color; 
} 

```
###### <center> [코드 10-8] 잘못된 코드 - 추이성 위배! </center>

<br>

이방식은 대칭성을 지켜주지만 추이성을 깨버린다.

<br>

```java

ColorPoint p1 = new ColorPoint(1, 2, Color.RED);
Point p2 = new Point(1, 2); 
ColorPoint p3 = new ColorPoint(1, 2, Color.BLUE); 
p1.equals(p2); // true
p2.equals(p3); // true 
p1.equals(p3); // false

```

<br>
그래서 해법은 무엇일까? 구체 클래스를 확장해 새로운 값을 추가하면서 equals 규약을 만족시킬 방법은 존재하지 않는다. 객체 지향적 추상화의 이점을 포기하지 않는한 말이다. 

이는 getClass 검사로 바꾸면 구체클래스 상속이 가능할 수 있다고 들릴지 모르지만 이는 전혀 맞지않다.

> 어떤 타입에 있는 중요한 속성이라면 그 하위타입에서 마찬가지로 중요하다. 따라서 그 타입의 모든 메서드가 하위 타입에서도 똑같이 잘 작동해야 한다. <br> <br> - 리스코프 치환 원칙(Liskov substitution principle)

아래의 equlas는 같은 구현 클래스의 객체와 비교할 때만 true를 반환한다. Point의 하위 클래스는 정의상 여전히 Point이므로 어디서든 Point로써 활용될 수 있어야 한다. 하지만 이 방식에서는 그러하지 못하다.

<br>

```java

public boolean equals(Object o) {
         if (o == null || o.getClass() != getClass()) {
             return false; 
        } 
           Point p = (Point) o;
        return p.x == x && p.y == y;
} 

```
###### <center> [코드 10-9] 리스코프 치환원칙 위배! </center>

<br>

> ※getClass() <br><br> 로딩된 클래스의 주소값을 리턴한다. 만약 B를 상속받는 C라는 클래스가 있을 경우 <br><br>
  B b1 = new B();<br>
  B b2 = new B();<br>
  if (b1.getClass() == b2.getClass()) {<br>
    System.out.println("b1.getClass() == b2.getClass()");<br>
  }<br><br>
  인스턴스는 다른 인스턴스이지만, 실제 로딩된 클래스는 같은 클래스이기 때문에 p1.getClass() == p2.getClass() 의 결과는 true이다.<br><br>
  B b = new B();<br>
  B c = new C();<br>
  if (b.getClass() == c.getClass()) {<br>
     System.out.println("b.getClass() == c.getClass()");<br>
  }<br><br>
  실제로 담겨있는 클래스가 B와 C로 다르기 때문에, 즉 서로 다른 클래스가 로딩되었기때문에 p1.getClass() == p2.getClass() 는 false를 리턴한다.

<br>

##### 상속 대신 컴포지션을 사용해라!

그러면 어떻게 해야 할까? 구체클래스의 하위클래스에서 값을 추가할 방법은 없지만 괜찮은 우회 방법이 하나 있다. 바로 컴포지션을 사용하는 방법이다. 

ColorPoint가 Point를 상속받지 않고, private Point point;를 추가하는 것이다.

<br>

```java

public class ColorPoint{ 
    private final Color color;
    private final Point point;
    public ColorPoint(int x, int y, Color color) {
        point = new Point(x,y);
        this.color = Objects.requireNonNull(color);
    } 

    pulic Point asPoint(){
        return point;
    }
     
    public boolean equals(Object o) {
         if (!(o instanceof ColorPoint)) {
             return false; 
        } 
        ColorPoint cp = (ColorPoint ) o;
        return cp.point.equlas(point) && cp.color.equals(color);
}   

```

<br>

##### 일관성(consistency)

> null이 아닌 모든 참조값 x,y에 대해 x.equals(y)를 반복해서 호출하면 항상 true를 반환하거나 항상 false를 반환한다.

두 객체가 같다면(어느 하나 혹은 두 객체 모두 수정되지 않는 한) 앞으로도 영원히 같다. 

<br>

##### null-아님

null 이 아닌 모든 참조값 x에 대해, x.equals(null)은 false이다.

모든 객체가 null과 같지 않아야한다. 또한, null 에 대한 명시적 검사보단 instanceof 가 묵시적으로 해주니까 이를 사용하길 권장한다.

<br>

#### 강추 equals 메서드 구현 방법

1. == 연산자를 사용해 자기 자신의 참조인지 확인하라. 
- 자기자신이면 true반환

2. instanceof 연산자로 입력이 올바른 타입인지 확인하라. 
- null검사도 해줌

3. 입력을 올바른 타입으로 형변환하라.
4. 입력 객체와 자기 자신의 대응되는 핵심 필드들이 모두 일치하는지 하나씩 검사하라.
5. 성능을 위해 다를 가능성이 더 크거나, 비용이 싼 필드를 먼저 비교해라.

위의 구현방법을 다 지켜 구현했다면 세가지만 자문해보자. 대칭적인가? 추이성이 있는가? 일관적인가? 자문에 끝내지 말고 단위테스트를 작성해 돌려보거나 AutoValue 애노테이션을 사용하자. 

<br>

#### 추가 주의사항 

- equals를 재정의 할 땐 hashCode도 반드시 재정의하자(Item 11)
- 너무 복잡하게 해결하려 들지 말자. 
- 필드들의 동치성만 검사해도 equals 규약을 어렵지 않게 지킬 수 있다. 또한 일반적으로 별칭(alias)는 비교하지 않는게 좋다. 
- Object 외의 타입을 매개변수로 받는 equals 메서드는 선언하지 말자. 

```java

//잘못된 예 - 입력 타입은 반드시 Object 여야 한다!
//컴파일 되지 않음
@Override public boolean equals(MyClass o){
    ....
}

```

<br>

#### 결론은?

> 꼭 필요한 경우가 아니면 equals를 재정의하지 말자. 많은 경우 Object의 equals가 여러분이 원하는 비교를 정확히 수행해준다. 재정의해야 할때는 그 클래스의 핵심 필드 모두를 빠짐없이, 다섯가지의 규약을 확실히 지켜가며 비교해야 한다.

<br>

##### 사람 < IDE < AutoValue

equals 를 작성하고 테스트하는 일은 지루하고 뻔하다. 이런 작업을 대신해줄 Auto Value 프레임워크가 있다. 클래스에 애너테이션 하나만 추가하면 AutoValue가 이메서드들을 알아서 작성해주며 이는 직접 작성한 메서드와 근본적으로 같을것이다. 

대다수의 IDE도 같은 기능을 제공하지만 생성된 코드가 AutoValue만큼 깔끔하지 못하고, 나중에 클래스가 수정된걸 자동으로 알아차리지 못해 이를 대비하는 테스트코드가 필요하다. 

하지만 이런 단점을 감안하더라도, 사람보다 낫다. 

이에 사람보다 훌륭한 두가지 대안책을 살펴보며 글을 마칠까 한다.

<br>

#### Auto Value 

첫번째는 Auto Value이다. Auto Value란 Value 타입을 정확히 만들도록 도와줄 자바 라이브러리이다. 다음의 클래스를 살펴보자.

<br>

```java

public final class Transaction {

  private long id;

  private String user;

  public Transaction(long id, String user) {
    this.id = id;
    this.user = user;
  }

  public long getId() {
    return id;
  }

  public String getUser() {
    return user;
  }

  @Override
  public boolean equals(Object o) {
    if (this == o)
      return true;
    if (!(o instanceof Transaction))
      return false;
    Transaction that = (Transaction) o;
    return id == that.id && Objects.equals(user, that.user);
  }

  @Override
  public int hashCode() {
    return Objects.hash(id, user);
    }

  @Override
  public String toString() {
    return "Transaction{" + "id=" + id + ", user='" + user + '\'' + '}';
  }
}

```

###### <center> [코드 10-1] equals 메서드를 오버라딩 하여 구현 </center>

<br>

위 클래스는 id와 user 라는 private 멤버변수를 가지고 있고 getter 를 구현했다. 또한 이 아이템에서 제일 집중적으로 살펴볼 equals 메서드를 오버라이딩 하여 구현했는데 이는 아래에서 무슨 규약을 위배했는지 살펴보겠다. 

<br>

```java

package auto.demo1;

import com.google.auto.value.AutoValue;

@AutoValue
public abstract class Transaction {

  public static Transaction of(long id, String user) {
    return new AutoValue_Transaction(id, user);
  }

  public abstract long id();

  public abstract String user();
}

```

###### <center> [코드 10-2] AutoValue 애노테이션 사용  </center> 

<br>

위 코드는 구글의 AutoValue 를 사용하여 같은 내용을 구현한 것이다. 단지 우리는 우리가 사용할 코드를 작성하고 나머지는 Google AutoValue 애노테이션이 하도록 내버려 두면 된다. 

![autovalue](/assets/images/post/190616/(1).png) 

###### <center> [그림 10-3] 비교표 </center> 

<br>

이렇듯 AutoValue를 사용하면 클래스를 조금 더 작은 노력으로 생성하는 것이 가능해진다. 이 라이브러리는 hashCode(), equals(), toString() 메서드를 당신을 위해 생성해 줄것이다. 또한 복잡한 클래스드를 만들때를 위해 builder pattern 또한 생성해 줄 것이다. 

<br>

#### Lombok 의 @EqualsAndHashCode

우리가 자주 사용하는 Lombok도 훌륭한 대안이 될 수 있다. Lombok의 @EqualsAndHashCode는 equals()와 hashCode()를 자동으로 생성해준다. 다음의 코드를 살펴보자.

<br>

```java

@EqualsAndHashCode(callSuper = true)
public class User extends Domain {
  private String username;
  private String password;
}

```

###### <center> [코드 10-4] @EqualsAndHashCode 를 사용한 클래스 구현 </center> 

<br>

보기에도 훨씬 간결하다. 또한 callSuper 속성을 통해 equals()와 hashCode() 메서드 자동 생성시 부모클래스의 필드까지 고려할 것인지 결정할 수있다. callSuper = true 라면 부모클래스의 필드값까지 같은지 확인하고 false라면 자신의 클래스만 고려한다.

```java

User user1 = new User();
user1.setId(1L);
user1.setUsername("user");
user1.setPassword("pass");

User user2 = new User();
user1.setId(2L); // 부모 클래스의 필드가 다름
user2.setUsername("user");
user2.setPassword("pass");

user1.equals(user2);
// callSuper = true 이면 false, callSuper = false 이면 true

```
###### <center> [코드 10-5] callSuper 속성  </center>

<br>

###### 출처 <br/>
###### [https://mincong-h.github.io/2018/08/21/why-you-should-use-auto-value-in-java/][link01] <br/>
[link01]: https://mincong-h.github.io/2018/08/21/why-you-should-use-auto-value-in-java/
###### [http://sjh836.tistory.com/169][link02] <br/>
[link02]: http://sjh836.tistory.com/169

<br>

