---
layout: post
title:  "[안드로이드] 📲 View Binding을 사용하여 더 쉽게 뷰 객체를 inflate하자!"
date:   2020-09-22 18:34:10 +0700
categories: [안드로이드]
---

# [📲 View Binding을 사용하여 더 쉽게 뷰 객체를 inflate하자!]

## 1️⃣ View Binding이 무엇이고 왜 등장했을까?

View Binding은 안드로이드 스튜디오가 3.6 Canary 11로 버전 업그레이드 하면서 추가된 기능이다. JetPack에 포함된 라이브러리는 아니다.

결론부터 말하자면 View Binding 기능은 뷰와 상호작용하는 코드를 이전보다 더욱 쉽고 간결하게 작성할 수 있도록 해주는 녀석이다.

그렇다면 이전의 뷰와 상호작용하는 코드는 어떠했길래 더 쉬운 방법이 등장한걸까?

> 여기부터는 [참고 블로그 포스팅](https://velog.io/@dev_2dong/Android-%EA%B3%B5%EC%8B%9D%EB%AC%B8%EC%84%9C-%ED%8C%8C%ED%97%A4%EC%B9%98%EA%B8%B0-Architecture-Component2-View-Binding#view-bindng---%EB%8F%84%EC%9E%85) 을 참고하여 작성하였다.

지금까지는 View 컴포넌트들을 코드에서 직접 사용할 수 있는 객체로 만들기 위해 __findViewById()__ 라는 메소드를 사용하였다.

~~~kotlin
// 예시
val textView : TextView = findViewById(R.id.tv_title)
~~~

하지만 이 방법으로 View 컴포넌트들을 객체화 시킬 경우 몇 가지 문제점이 발생하였다.

1. 코드로 제어해야 하는 View 컴포넌트들이 많으면 많을수록 findViewById() 를 호출하는 코드가 View 컴포넌트 개수만큼 늘어났다. (코드가 길어지고 양이 많아지는 문제 발생)

2. findViewById() 함수는 속도가 느리다. (속도가 느려지는 문제 발생)

3. 개발자의 실수로 존재하지 않는 컴포넌트의 id를 불러왔다면 null이 발생하게 된다. 즉, null 관련 에러를 발생시키고 null 값에 안전하지 못하다.

따라서 findViewById() 함수로 View 컴포넌트를 객체화시키는 방법이 개선될 필요가 있었던 것이다!

> 하지만 코틀린에서는 findViewById() 함수를 호출하지 않고도 View를 객체화 할 수 있는 기능이 존재한다. [코틀린 확장 플러그인에 대한 이전 포스팅](https://choheeis.github.io/newblog//articles/2019-08/%EC%95%88%EB%93%9C%EB%A1%9C%EC%9D%B4%EB%93%9C-overview) 을 읽어보면 코틀린에서 findViewById()를 사용하지 않고 View를 객체화하는 방법을 알 수 있을 것이다.
>
> 코틀린 확장 플러그인에서 View를 객체화하는 방법과 View Binding 기능을 사용하여 View를 객체화 하는 방법에 대한 차이도 공부할 필요가 있겠다!

## 2️⃣ View Binding 기능을 사용하여 View 컴포넌트를 객체화시켜 보자!

> 이어서 https://developer.android.com/topic/libraries/view-binding 읽어보자..!
그리고 오픈채팅방에 질문한 거 캡쳐해서 추가로 글 작성하기!


https://www.youtube.com/watch?v=W7uujFrljW0