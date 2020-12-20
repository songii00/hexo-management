---
layout: post
title:  "웹은 세부사항이다"
date:   2019-11-17 21:35:18 +0900
tags: [CleanArchitecture]
categories: clean-architecture
subtitle : Clean Architecture - Item 31
feature-img: "assets/img/banner.jpg"
---

### "CleanArchitecture 31장"
---

### 끝없이 반복하는 추

![](/assets/images/post/191125/(17).png)

- 천공카드 -> 그린스크린 단말기가 연결되는 메인프레임 -> 중앙집중식 미니컴퓨터 -> 클라이언트-서버 아키텍처
- 연산 능력을 중앙에 집중하는 방식과 분산하는 방식 사이에서 끊임없이 움직임
- 웹은 수많은 진동 중 하나에 불과
- 앞으로도 진동은 지속됨

###### 클라이언트-서버 아키텍처 더 알아보기 : [https://mingrammer.com/translation-10-common-software-architectural-patterns-in-a-nutshell/...][link01] <br/>
[link01]: https://mingrammer.com/translation-10-common-software-architectural-patterns-in-a-nutshell/#2-%ED%81%B4%EB%9D%BC%EC%9D%B4%EC%96%B8%ED%8A%B8-%EC%84%9C%EB%B2%84-%ED%8C%A8%ED%84%B4-client-server-pattern
<br>

<!-- more -->

### 요약

> GUI 는 세부사항이다. 웹은 GUI 이다. 따라서 웹은 세부사항이다. 그리고 아키텍트라면 이러한 세부사항을 핵심 업무 로직에서 분리된 경계 바깥에 두어야 한다.

- 업무로직은 다수의 유스케이스로 구성
- 각 유스케이스는 일부 함수를 수행 
- 유스케이스 = 입력데이터 -> 수행할 처리과정 -> 출력 데이터 를 기반으로 기술 
- 입력 데이터 구성 -> 유스케이스 실행 -> 종료 시 결과 데이터를 UI와 애플리케이션으로 돌려줌
- 완전한 입력 / 출력 데이터 구조를 만들어 유스케이스를 실행하는 처리과정의 입출력 값으로 사용

이 방식을 따르면 각 유스케이스가 장치 독립적인 방식으로 UI 라는 입출력 장치를 동작시킨다고 간주할수 있다. 

<br>

### 결론

- 위 추상화는 만들기 쉽지 않지만 불가능 하지 않다. 
- 세상은 마케팅 귀재로 가득하므로 이런 추상화는 꼭 필요하다.
- 웹은 세부사항이다.
- GUI를 업무규칙으로부터 분리하라.