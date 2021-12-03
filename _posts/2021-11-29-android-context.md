<!-- ---
layout: post
title: "[안드로이드] Context가 뭐죠?"
date: 2021-11-29 15:34:10 +0700
categories: [안드로이드]
--- -->

## 0️⃣ 프롤로그
 * 안드로이드 개발을 하면서 Context 타입을 정말 많이 사용한다. 이 형식을 잘못 사용하면 ANR이 발생하거나 메모리 Leak이 발생하기도 한다. 안드로이드의 핵심이라고도 할 수 있는 이 Context에 대해서 자세하게 공부 해 보았다.

## 1️⃣ 공식 문서에서 설명하는 Context는?
> Interface to global information about an application environment. This is an abstract class whose implementation is provided by the Android system. It allows access to application-specific resources and classes, as well as up-calls for application-level operations such as launching activities, broadcasting and receiving intents, etc.

 * Context는 어플리케이션 환경에 대한 글로벌 정보를 갖는 인터페이스이다. Context는 Android 시스템에서 구현체를 제공하는 추상 클래스로, 애플리케이션 별 리소스 및 클래스 접근에 사용되며, 액티비티 실행, 브로드캐스트, 인탠트 수신 등과 같은 애플리케이션 수준 작업에 사용된다.
 [android developer context](developer.android.com/reference/android/content/Context)
 * context는 영어의 뜻 그대로 현재 상태의 맥락이라는 의미를 가진다. 컨텍스트는 새로 생긴 객체가 지금 어떤 상태인지 알 수 있도록 해준다.
 * Activity와 Application의 정보를 얻기 위해 사용할 수 있다. 또한 Resource, DB, Shared Preference 등에 접근하기 위해 사용된다. 내가 이해하기로는 현재 상태 즉, 각각의 생명 주기에 따라서 생성되고 소멸되는 시점을 명확히 하고 이를 활용하여 메모리 누수를 방지한다는 것이다.

## 2️⃣ Context의 종류
 ### Application Context
 * 가장 최상위 층의 context라고 생각하면 된다. getApplicationContext

 ![getApplicationContext](https://user-images.githubusercontent.com/27722059/143963200-ebeaf089-130d-4235-beac-b5c87901bbfe.png)