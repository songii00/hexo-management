---
layout: post
title:  "메인 컴포넌트"
date:   2019-11-17 20:39:14 +0900
tags: [CleanArchitecture]
categories: clean-architecture
subtitle : Clean Architecture - Item 26
feature-img: "assets/img/banner.jpg"
---

### "CleanArchitecture 26장"
---

### 메인 컴포넌트

> 모든 시스템에서 최소한 하나의 컴포넌트가 존재하고, 이 컴포넌트가 나머지 컴포넌트를 생성하고, 조정하며, 관리한다

<br>![factory method pattern](/assets/images/post/191117/(6).gif)

<br>

<!-- more -->

### 궁극적인 세부사항

#### 메인

- 시스템의 초기 진입점
- 운영체제를 제외하면 어떤것도 메인에 의존하지 않음
- 모든 팩토리와 전략, 시스템 전반을 담당하는 나머지 기반 설비를 생성
- 궁극적인 세부사항
- 가장 낮은 수준의 정책
- 의존성 주입 프레임워크를 이용해 의존성을 주입하는 일 담당
- 가장 지저분한 컴포넌트
- 클린 아키텍처에서 가장 바깥 원에 위치
- 고수준의 시스템을 위한 모든 것을 로드 후, 제어권을 고수준의 시스템에게 넘김

<br>

``` java
public class Main implements HtwMessageReceiver {
  private static HuntTheWumpus game;
  private static int hitPoints = 10;
  private static final List<String> caverns = new ArrayList<>();
  // 코드의 나머지 핵심 영역에서 구체적인 문자열을 알지 못하게 함
  private static final String[] environments = new String[]{
    "bright",
    "humid",
    "dry",
    "creepy",
    "ugly",
    "foggy",
    "hot",
    "cold",
    "drafty",
    "dreadful"
  };
  
   private static final String[] shapes = new String[] {
    "round",
    "square",
    "oval",
    "irregular",
    "long",
    "craggy",
    "rough",
    "tall",
    "narrow"
  };

  ...

public static void main(String[] args) throws IOException {
  //게임 만듬
    game = HtwFactory.makeGame("htw.game.HuntTheWumpusFacade", new Main());
  // 맵 생성
    createMap();
    BufferedReader br = new BufferedReader(new InputStreamReader(System.in));
    game.makeRestCommand().execute();
    while (true) {
      System.out.println(game.getPlayerCavern());
      System.out.println("Health: " + hitPoints + " arrows: " + game.getQuiver());
      HuntTheWumpus.Command c = game.makeRestCommand();
      System.out.println(">");
      String command = br.readLine();
      if (command.equalsIgnoreCase("e"))
        // 명령어를 실제로 처리하는 일은 고수준 컴포넌트로 위임
        c = game.makeMoveCommand(EAST);
      else if (command.equalsIgnoreCase("w"))
        c = game.makeMoveCommand(WEST);
      else if (command.equalsIgnoreCase("n"))
        c = game.makeMoveCommand(NORTH);
      else if (command.equalsIgnoreCase("s"))
        c = game.makeMoveCommand(SOUTH);
      else if (command.equalsIgnoreCase("r"))
        c = game.makeRestCommand();
      else if (command.equalsIgnoreCase("sw"))
        c = game.makeShootCommand(WEST);
      else if (command.equalsIgnoreCase("se"))
        c = game.makeShootCommand(EAST);
      else if (command.equalsIgnoreCase("sn"))
        c = game.makeShootCommand(NORTH);
      else if (command.equalsIgnoreCase("ss"))
        c = game.makeShootCommand(SOUTH);
      else if (command.equalsIgnoreCase("q"))
        return;

      c.execute();
    }
  }
}
```

<br>

- 메인은 클린 아키텍처에서 가장 바깥 원에 위치
- 지저분한 저수준 모듈
- 고수준의 시스템을 위한 모든 것을 로드 후 제어권을 고수준의 시스템에 넘김

<br>

### 결론

> 메인을 애플리케이션의 플러그인이라고 생각하자. 예를 들어 개발용 메인 플러그인, 별도의 테스트용 메인 플러그인 다른 상용 메인 플러그인 등을 만들 수 있다. 배포하려는 국가별로, 관할 영역별로, 고객별로 메인 플러그인을 만들 수 있다. 