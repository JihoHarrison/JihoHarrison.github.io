---
layout: post
title:  "[안드로이드] 안드로이드 & RxKotlin을 이용한 MVVM으로의 접근 💡"
date: 2021-10-15 18:34:10 +0700
categories: [안드로이드]
---

## 0️⃣ 프롤로그 

 __rxKotlin & Android__
 * MVVM 패턴 학습을 시작하면서 적용될 수 있는 로직들에 대해서 정말 많은 고민을 했다. 나같이 안드로이드에서 이제 막 걸음마를 걷기 시작한 개발자는 분명 coroutine과 rxJava 이 두개의 비동기 처리 방식에 대해 고민을 했을 것이다. 비동기 처리에 대한 고민을 시작했을 때 처음 접했던게 rxJava였다. MVVM이 흥행하기까지 널리 사용되었던 MVC, MVP에 대한 이해도 필요하다고 생각했다.rxJava에 대한 설명은 다음 포스팅에 있다.
 
> [rxKotlin_연산자_01](https://jihokevin.github.io//articles/2021-09/rx-kotlin-01)

> [rxKotlin_연산자_02](https://jihokevin.github.io//articles/2021-09/rx-kotlin-02)

> [Hot and Cold Observable](https://jihokevin.github.io//articles/2021-09/hot-cold-observable)

## 1️⃣ MVC? MVP?

 MVVM 패턴을 살펴보기 전에 MVC와 MVP에 대한 이해를 할 필요가 분명히 있디고 생각했다.
 MVC, MVP와 같은 디자인 패턴들은 여러 곳에서 많이 들어보고, 접해보고, 익숙해져 있는 상태이다.

 __MVC__
 * Activity나 Fragment에서 Controller와 Presentation Login을 한꺼번에 처리하는 구조로, 앱이 커질수록 View기 굉장히 거대해질 수 있는 형태이다. 결국 Controller와 View가 같은 곳에서 놀고있기 때문에 Controller 그 자체에 대한 테스트가 어려운 상황이 발생했다. 안드로이드 View 속에 Controller가 존재했기 때문이다.
 
 __MVP__
 * 그래서 테스트를 위해서 Controller를 분리하자 라고 나온 아키텍쳐가 바로 MVP 라고 하는데 정확한 차이점을 느끼지 못했다. 결국 Controller나 Presenter를 통해서 비즈니스 로직과 프레잰테이션 로직을 분리시키는 것은 똑같은 원리일텐데 정확히 어떤게 다른 점일까 파고들게 되었다.

 * View와 Model을 연결하는 브릿지가 되어야 하고, Presenter로서 처리해야 할 일들을 명확히 규정해야 했다. 이를 위해서 인터페이스화 된 형태의 View를 담당하는 인터페이스와 Presenter를 담당하는 인터페이스를 구현하여 테스트 환경에서는 데이터에 대한 목업 처리만 진행 해 주면 된다는 의미에서 MVP가 파생되었다. 결국 결론적으로는!
 
 > __테스트 하기 쉽다 = 함수가 잘 나누어져 있다 = 코드가 보기 쉽다 = 유지보수에 좋다__
 라는 의미가 된다..!

## 2️⃣ Android에서의 MVVM
[마지막 수정 : 2021-10-15, 추후 업데이트 예정]
