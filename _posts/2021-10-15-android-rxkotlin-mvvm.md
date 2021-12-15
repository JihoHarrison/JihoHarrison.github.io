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
 * 그래서 테스트를 위해서 Controller를 분리하자 라고 나온 아키텍쳐가 바로 MVP 라고 하는데 정확한 차이점을 느끼지 못했다. 결국 Controller나 Presenter를 통해서 비즈니스 로직과 프레젠테이션 로직을 분리시키는 것은 똑같은 원리일텐데 정확히 어떤게 다른 점일까 파고들게 되었다.

 * View와 Model을 연결하는 브릿지가 되어야 하고, Presenter로서 처리해야 할 일들을 명확히 규정해야 했다. 이를 위해서 인터페이스화 된 형태의 View를 담당하는 인터페이스와 Presenter를 담당하는 인터페이스를 구현하여 테스트 환경에서는 데이터에 대한 목업 처리만 진행 해 주면 된다는 의미에서 MVP가 파생되었다. 결국 결론적으로는!
 
 > __테스트 하기 쉽다 == 함수가 잘 나누어져 있다 == 코드가 보기 쉽다 == 유지보수에 좋다__
 라는 의미가 된다..!

## 2️⃣ Android에서의 MVVM
 * __Model__ 에 데이터가 존재하면 __ViewModel__ 이 가져와서 가공하고, View는 그 __ViewModel__ 을 바라보고 있다가 데이터 변경이 발생하면 UI를 변경 시켜주는 형태이다.

 `Model` : 어플리케이션에서 사용되는 데이터와 그 데이터를 처리하는 부분 -> __Repository라고 보면 되겠다.__

 `View` : 말 그대로 __View.__ 사용자에 의한 이벤트가 발생할 수 있는 화면 단을 의미한다.

 `ViewModel` : 개인적인 개발 과정에서의 경험으로는 __Model과 View를 연결시켜주는 다리__ 가 되기도 하고, __Model__ 에서 끌고 올라온 데이터를 __View__ 에 알맞게 표현하기 위해 가공시켜주는 역할도 한다. 주로 AAC의 ViewModel 클래스를 사용한다. Coroutine을 사용하면 __LiveData__ 를 통해 __View__ 와 소통되며, __RxKotlin__ 을 사용 할 경우 나의 경우는 __Processor, Subject__ 를 통하여 __Flowable, Observable, Single__ 등의로 적절한 return 타입을 잡아주면서 개발에 참여했다.

> ### View
1. Activity나 Fragment가 View의 역할을 한다.
2. 사용자의 이벤트를 받는 부분이다.
3. __ViewModel__ 의 데이터를 관찰하며 상태 변화가 생길 시 UI를 즉각 갱신한다.

> ### ViewModel
1. __View__ 에서 발생한 이벤트로부터 전달된 __데이터를 Model로 요청하는 중간 다리 역할.__
2. __Model__ 로 부터 요청한 데이터를 받아오는 중간 다리 역할.

> ### Model
1. __ViewModel__ 이 요청한 데이터를 넘겨준다.
2. 주로 __Repository__ 라는 부분을 포함하면서 불리우며, __Web Service에서 데이터를 요청하는 부분__ 까지 일컫는 부분이다.
3. __Room, Realm, Retrofit__ 등의 __DB 사용__ 이나 __백엔드 API 호출__ 등의 작업을 담당한다. (데이터의 창고같은 역할)

* 결국 __View__ 가 필요로 하는 데이터는 __ViewModel__ 이 쥐고 있고, __View__ 는 그것을 필요로 하기 때문에 __ViewModel__ 이 쥐고 있는 데이터를 관찰 __(Observing)__ 한다. 때문에 __MVC__ 패턴과 다르게, __View__ 가 DB 에 직접 접근하는 것이 아닌 __UI 업데이트에만 집중한다.__

* 또한 관찰하고 있는 만큼 데이터 변화에 더욱 능동적으로 움직이게 된다.

## 3️⃣ MVVM 패턴은 다음과 같은 장점을 가진다.

1. __View__ 가 __ViewModel__ 의 __Data__ 를 관찰하고 있으므로 __UI 업데이트가 간편__
2. __ViewModel__ 이 데이터를 Hold(?)하고 있으므로 잘못 사용하지 않는 이상 __Memory Leak 발생 가능성 이 낮다.__ (View 가 직접 Model 에 접근하지 않아 가져온 데이터는 __Activity__ 나 __Fragment__ 생명주기에 의존하지 않기 때문이다.)
3. 기능별 모듈화가 잘 되어 유지 보수에 용이 (View와 ViewModel은 1:N의 관계를 가질 수 있기 때문에 __한 번 만들어 놓은 viewModel은 다른 View에서도 재사용이 매우 용이__ 하다.)

## 4️⃣ AAC (Android Architecture Component)

![AAC](https://user-images.githubusercontent.com/27722059/146218480-45cb7ae8-89ae-49af-81fe-c2fe0c3de353.png)
* ### ViewModel
    * 화면 변화 시에도 변하거나 소멸되지 않는 데이터를 가지고 있다.

* ### LiveData
    * __View__ 가 __ViewModel__ 을 관찰할 때, 그 관찰 대상이 되는 데이터 타입이다. __LiveData__ 는 __Activity__ 및 __Fragment__ 의 __LifeCycle__ 을 인지하지 못하므로, 화면이 활성화 되어 있을 때만 동작하여 메모리 릭을 줄여준다.

* ### Repository
    * __ViewModel__ 과 데이터를 주고받기 위해, 데이터 __API__ 를 포함하는 클래스다. 사용자 동작에 따라 필요한 데이터나 외부 백엔드 서버 등에서 데이터를 가져오게 된다. __Repository__ 의 존재 덕분에 __ViewModel__ 이 데이터를 관리할 필요가 없게 된다.

* ### RoomDatabase
    * __Room__ 은 __SQLite__ 를 사용함에 있어 별도의 __Query문__ 작성없이 간편하게 __Insert, Delete__ 등의 동작을 할 수 있게끔 도와주는 __ORM 라이브러리__ 이다.

_[마지막 수정 : 2021-12-16, 추후 업데이트 예정]_
