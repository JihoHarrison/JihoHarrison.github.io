---
layout: post
title: "[안드로이드] Context가 뭐죠? 📑"
date: 2021-11-29 15:34:10 +0700
categories: [안드로이드]
---

## 0️⃣ 프롤로그
 * 안드로이드 개발을 하면서 Context 타입을 정말 많이 사용한다. 이 형식을 잘못 사용하면 ANR이 발생하거나 메모리 Leak이 발생하기도 한다. 안드로이드의 핵심이라고도 할 수 있는 이 Context에 대해서 자세하게 공부 해 보았다.
 > ![https://developer.android.com/reference/android/content/Context]

## 1️⃣ 공식 문서에서 설명하는 Context는?
> Interface to global information about an application environment. This is an abstract class whose implementation is provided by the Android system. It allows access to application-specific resources and classes, as well as up-calls for application-level operations such as launching activities, broadcasting and receiving intents, etc.

 * Context는 어플리케이션 환경에 대한 글로벌 정보를 갖는 인터페이스이다. Context는 Android 시스템에서 구현체를 제공하는 추상 클래스로, 애플리케이션 별 리소스 및 클래스 접근에 사용되며, 액티비티 실행, 브로드캐스트, 인탠트 수신 등과 같은 애플리케이션 수준 작업에 사용된다.
 [android developer context](developer.android.com/reference/android/content/Context)
 * context는 영어의 뜻 그대로 현재 상태의 맥락이라는 의미를 가진다. 컨텍스트는 새로 생긴 객체가 지금 어떤 상태인지 알 수 있도록 해준다.
 * Activity와 Application의 정보를 얻기 위해 사용할 수 있다. 또한 Resource, DB, Shared Preference 등에 접근하기 위해 사용된다. 내가 이해하기로는 현재 상태 즉, 각각의 생명 주기에 따라서 생성되고 소멸되는 시점을 명확히 하고 이를 활용하여 메모리 누수를 방지한다는 것이다.

## 2️⃣ Context의 종류
 ### Application Context
 * 가장 최상위 층의 context라고 생각하면 된다. getApplicationContext() 를 통해서 접근할 수 있고 이 컨택스트는 안드로이드 최상위 계층인 application 클래스의 라이프사이클에 묶여있으며, 현재 컨텍스트가 종료 된 이후에도 컨텍스트가 필요한 작업이나 하나의 액티비티 스코프를 벗어난 컨텍스트가 픽요한 작업에 맞는 컨텍스트이다. 싱글턴 인스턴스로 구성되어있다는 점이 특징이다.
 * ### Application Context는 어디에 사용하지?
 예를 들어, 최상위 어플리케이션 클래스에 싱글턴으로 생성된 오브젝트가 있다고 가정해보자. 컨텍스트가 필요한 작업이면 당연히 해당 생명주기에 맞는 application context를 전달하는 것이 맞다. 만약 Activity 컨텍스트를 전달한다면 해당 오브젝트가 항상 액티비티에 붙어있으므로 액티비티가 화면에 표시되지 않는 순간까지 GC가 진행되지 않아서 메모리 누수가 발생하게 된다. 다음 공식문서에서 나온 것 처럼 하나의 전역적이고 Single 형태의 object를 반환한다.

 ![getApplicationContext](https://user-images.githubusercontent.com/27722059/143963200-ebeaf089-130d-4235-beac-b5c87901bbfe.png)

 ### Activity Context
 * Activity Context는 activity 내에서 유효한 컨택스트이다. 이 컨택스트는 액티비티 라이프사이클과 연결되어 있습니다. 액티비티 컨택스트는 액티비티와 함께 소멸해야 하는 경우에 사용하게 된다. 보통 getContext나 this 키워드를 사용하여 가져올 수 있다. 예를 들어, 액티비티와 라이프사이클이 같은 오브젝트를 생성해야 할 때 액티비티 컨택스트를 사용할 수 있습니다. 공식문서에서도 다음과 같이 말하고 있다.
 ![image](https://user-images.githubusercontent.com/27722059/145498734-b49b0b9f-f4ea-4f52-af20-290185c3ab3d.png)   
 요점만 보면, 최상위 오브젝트인 application 컨텍스트를 아무때나 사용한다면 심각한 메모리 누수를 초래한다는 말이다.

 * 결국 정리해보면 다음과 같은 말이다.
    - Application Context는 Application, Activity 모두에서 사용할 수 있다.
    - MainActivity의 Context는 MainActivity에서만 사용할 수 있다.
    - 하지만 최상위 오브젝트라고 막 갖다 쓰면 메모리 누수가 일어난다.

## 3️⃣ 언제, 어떤 Context를 사용해야 할까?
 * Applications 클래스 하나와 일반 싱글턴 클래스 하나가 있다고 가정 해 보자. 이 일반 싱글턴 클래스가 context가 필요한 상황이라면 application 클래스를 쓰는게 맞다. Activity Context를 전달 했다면 액티비티가 사용되지 않는 동안에도 더 이상의 생명주기가 없는 싱글턴 클래스가 해당 액티비티를 계속 참조하고 있기 때문에 싱글턴 클래스는 항상 application의 context를 전달하는게 맞다.

 * Acticity Context를 사용 할 조건은 말 그대로 Activity를 사용할 때 이다. Toast Message, Dialog 등의 UI 작업을 필요로 하는 시점에는 Activity Context를 사용하는 것이 맞다.

## 4️⃣ getApplicationContext()만 써도 안되는 이유..
 * 아무리 최상위 Application Context라고 Activity Context가 제공하는 모든 기능을 제공하지는 않는다. 이 작업은 특히 UI 관련 Context를 조작 할 때 좋지 않는 방법이라고 할 수 있다.
 * 또한, Activity Context는 자기 자신만의 생명주기의 연속으로, GC가 동작하지만 Application object는 프로세스가 살아있는 동안 계속 남아있다는 특징을 가지고 있다.