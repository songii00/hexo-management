---
layout: post
title:  "컴포넌트"
date:   2019-10-20 11:33:21 +0900
tags: [CleanArchitecture]
categories: clean-architecture
subtitle : Clean Architecture - Item 12
feature-img: "assets/img/banner.jpg"
---

### 컴포넌트
컴포넌트는 배포단위다. 즉, 시스템의 구성요소로 배포할 수 있는 가장 작은 단위다.

ex) 자바의 jar, 루비의 gem, 닷넷의 DLL

또한 여러 컴포넌트를 서로 묶어서 .war 파일과 같은 단일 아카이브로 만들 수도있다. 또한 컴포넌트를 각각 .jar나 .dll 과 같이 동적으로 로드할 수 있는 플러그인이나, exe 파일로 만들어서 독립적으로 배포할 수도 있다.

중요한 것은

> 잘 설계된 컴포넌트라면 반드시 독립적으로 배포 가능한, 따라서 독립적으로 개발 가능한 능력을 갖춰야 한다.

<br>

<!-- more -->

### 컴파일 과정

컴파일이란 "어떤 언어로 된 소스 코드를 기계가 인식할 수 있는 네이티브 코드로 변환하는 과정을 의미" 한다. 

컴파일의 역사를 보기 전 간단하게 자바와 C 언어 의 컴파일 과정을 살펴보자.

<br>

#### C 언어의 컴파일 과정 

<br>

![factory method pattern](/assets/images/post/191020/(3).png) 

소스파일을 컴퓨터가 실제로 이해하고 실행하기 위해서 Low Level언어, 이진수 파일로 변환해줘야 하는 과정을 거쳐야 한다. 

이렇게 변환하는 것을 과정을 Compiling 이라하고 변환해주는 주체를 Compiler 라고 한다. 

또한, 컴파일 과정 뒤에 생긴 Low level 언어의 파일이 Object File 이다.

![factory method pattern](/assets/images/post/191020/(4).gif) 

만들어진 Object File 들과 라이브러리 파일들을 링커가 링킹해서 실행파일로 만든다.

###### C 언어의 컴파일 과정 더 알아보기 : [https://jhnyang.tistory.com/40][link01] <br/>
[link01]: https://jhnyang.tistory.com/40

<br>

#### 자바의 컴파일 과정 

자바에서의 컴파일은 자바 언어로 된 코드를 JVM이 인식할 수 있는 JVM 명령어 코드(바이트코드)로 변환하는 것을 의미한다.

![factory method pattern](/assets/images/post/191020/(1).png) 

HelloWorld.java 파일을 실행하기 위해 Run 버튼을 누르면 다음의 과정이 진행된다. 

1> HelloWorld.java 가 javac.exe 를 통해 컴파일 진행

2> HelloWorld.class 가 생성(bytecode) 
- bytecode 는 반 기계어로 컴퓨터가 읽을 수 없음

3> HelloWorld.class 를 java.exe 로 실행

4> 클래스 로더를 통해 HelloWorld.class를 JVM 으로 가져옴

5> Byte Code Verifier 에서 bytecode 를 검증

6> 인터프리터가 byte code 를 binary code 로 변환 

7> Runtime 실행 

![factory method pattern](/assets/images/post/191020/(2).png) 

###### 바이트 코드/바이너리 코드 더 알아보기 : [https://shrtorznzl.tistory.com/82][link04] <br/>
[link04]: https://shrtorznzl.tistory.com/82


<br>

### 컴포넌트의 역사 

<br>

#### 단일 프로그램으로 컴파일

소프트웨어 개발 초창기에는 

> 메모리에서 프로그램 위치와 레이아웃을 프로그래머가 직접 제어해야 했다. 

![factory method pattern](/assets/images/post/191020/(21).jpg)

###### <center> APPLE MACINTOSH : 미국의 애플컴퓨터가 1984년 1월에 발표한 16비트 및 32비트 개인용 컴퓨터  </center>

<br>

요즘 프로그래머는 프로그램을 메모리의 어느 위치에 로드할지 고민할 필요가 없다.

하지만 개발 초창기에는 프로그램의 위치가 한번 결정되면, 재배치가 불가능 했다.

이 시대에는 라이브러리 함수의 소스코드를 애플리케이션 코드에 프로그래머가 직접 포함시켜 단일 프로그램으로 컴파일 했다.

장치는 느리고, 메모리는 너무 비싸고.. 

한정된 자원 때문에 컴파일러는 느린 장치를 이용해 소스 코드를 여러 차례 읽어야 했다. 

때문에 함수 라이브러리가 크면 클수록 컴파일은 더 오래 걸렸다.

<br>

#### 함수 라이브러리의 소스코드 분리

컴파일 시간의 단축을 위해 프로그래머는 함수 라이브러리의 소스코드를 애플리케이션 코드로부터 분리했다.

함수 라이브러리를 개별적으로 컴파일, 컴파일된 바이너리를 메모리의 특정 위치에 로드했다.

![factory method pattern](/assets/images/post/191020/(24).png)

그래서 애플리케이션을 실행해야 한다면 바이너리 함수 라이브러리를 로드한 다음, 애플리케이션을 로드했다.

하지만 애플리케이션과 함수 라이브러리는 점점 커져서 프로그래머가 할당한 메모리를 넘어섰다.

결국 세분화된 주소 세그먼트로 단편화가 계속되었다.
<br>
###### 세그먼트 더 알아보기 :  [https://study.com/academy/lesson/memory-segmentation-definition-purpose.html][link05] <br/>
[link05]: https://study.com/academy/lesson/memory-segmentation-definition-purpose.html
<br>

#### 재배치성 

해결책은 재배치가 가능한 바이너리였다.

지능적인 로더를 사용해 메모리에 재배치 할 수 있는 형태의 바이너리를 생성하도록 컴파일러를 수정하자는 것이였다.



 **링킹 & 로더**

> 링킹 : 두개, 혹은 더 많은 오브젝트 코드들을 연결하고, 서로의 주소를 연결하는 정보를 제공

> 로더 : 오브젝트 프로그램을 메모리로 로드

![factory method pattern](/assets/images/post/191020/(25).jpg)


로더는 재배치 코드가 자리할 위치정보를 전달받는다. 

그리고 재배치 코드에는 로드한 데이터의 어느 부분을 수정해야 정해진 주소에 로드할 수 있는지 알려주는 메모리의 시작주소가 삽입되었다.

실제로 로더는 여러개의 바이너리를 입력 받은후, 단순히 하나씩 차례로 메모리에 로드하면서 재배치하는 일을 처리한다.



**링킹 로더** 

![factory method pattern](/assets/images/post/191020/(27).png)

컴파일러는 재배치 가능한 바이너리안의 함수 이름을 메타데이터 형태로 생성한다.

만약 프로그램이 라이브러리함수를 호출한다면 컴파일러는 라이브러리함수 이름을 외부 참조로 생성한다.

라이브러리 함수를 정의하는 프로그램이라면 컴파일러는 해당이름을 외부 정의로 생성한다.

외부 정의를 로드할 위치가 정해지기만 하면 로더가 와부참조를 외부 정의에 링크한다.

<br>

#### 링커 

![factory method pattern](/assets/images/post/191020/(26).jpg){: width="60%" height="60%"}
###### <center>자기테이프 </center>
<br>
링킹 로더를 사용하므로써 프로그램을 개별적으로 컴파일하고 로드할 수 있는 단위로 분할가능해 졌다.

하지만 프로그램은 점점 커지고 함수 라이브러리가 저장된 느린 장치(예로 자기테이프)를 이용해 바이너리 라이브러리를 읽고 외부 참조를 해석해야만 했다.

<br>

#### 로드와 링크 분리

이를 해결하기 위해 링커라는 별도의 애플리케이션으로 링크 과정을 처리하도록 했다.

링커가 링크가 완료된 재배치 코드를 만들었고, 그덕분에 로더의 로딩과장이 빨라졌다.

또한 링커가 만들어놓은 실행 파일을 언제라도 로드할수 있게 되었다.

현재 프로그래머가 프로그램을 성장시키는 속도보다 링크 시간이 줄어드는 속도가 더 빨리지기 시작했다.

심지어 컴퓨터가 너무 빨라져서 또다시 링크와 로드를 동시에 할 수 있게 되었다.

다수의 .jar 파일또는 공유 라이브러리를 순식간에 서로 링크한 후, 링크가 끝난 프로그램을 실행할 수 있게 되었다.

<br>

**컴포넌트 플러그인 아키텍처**

![factory method pattern](/assets/images/post/191020/(28).png)
###### 플러그인 아키텍처 더 알아보기 :  [https://medium.com/omarelgabrys-blog/plug-in-architecture-dec207291800][link08] <br/>
[link08]: https://medium.com/omarelgabrys-blog/plug-in-architecture-dec207291800


<br>

### 결론

런타임에 플러그인 형태로 결합할 수 있는 동적 링크파일이 컴포넌트에 해당한다.

여기까지 오는데 50년이 걸렸다.

이제는 쉽게 컴포넌트 플러그인 아키텍처를 적용할수 있다.


<br>

###### 출처 <br/>

###### [https://kim6394.tistory.com/215][link02] <br/>
[link02]: https://kim6394.tistory.com/215

###### [https://atin.tistory.com/346][link03] <br/>
[link03]: https://atin.tistory.com/346

###### [https://sites.google.com/site/fun21stcomputer/howcomhome/history][link09] <br/>
[link09]: https://sites.google.com/site/fun21stcomputer/howcomhome/history


