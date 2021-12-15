---
layout: post
title: "[안드로이드] 안드로이드와 코루틴 🌀"
date: 2021-12-13 22:11:01 +0700
categories: [안드로이드]
---

## 0️⃣ 프롤로그
 * 메디프레소에서 안드로이드를 개발하면서 RxKotlin을 정말 극한까지 경험헀다. Sign in, Sign out 이벤트를 처리 해 주는 이벤트 버스 패턴부터 ViewModel에서 BehaviorProcessor를 이용한 최신 상태 유지. View를 정말 최대한 멍청하게 만들겠다는 일념으로 지켜온 MVVM 패턴. 나를 안드로이드 개발자로서 더욱 성장시켜주는 프로젝트였다. 엎친데 덮친 격으로 11월 구글의 정책 변경으로 인한 공동인증서 이슈까지.. 참 많은 경험을 하게 해 준 메드나 프로젝트지만 RxKotlin을 사용하다보니 자연스럽게 Coroutine, Flow를 접목시켜보고 싶었다.

## 1️⃣ 코루틴이란?
 * 코루틴은 RxKotlin과 마찬가지로 비동기 프로그래밍을 처리할 수 있는 좋은 방법이다. 코루틴을 사용하면 어떠한 작업을 foreground, background 등으로 손쉽게 왔다 갔다 전환하며 처리가 가능하다. 나도 RxKotlin을 사용하는 개발자였지만 코루틴에 대한 관심이 점점 높아지면서 한 번 정리 해 보고자 한다.

    - ### 주요 핵심 키워드
        * 코루틴을 사용하기 위해서는 RxKotlin과 마찬가지로 여러가지 키워드들을 알아야 한다.

        ---
         - CoroutineScope, GlobalScope
         - CoroutineContext
         - Dispatcher
         - Launch (async)
        ---

        * ### CoroutineScope
            - CoroutineScope는 말 그대로 범위를 나타낸다. 어떤것의 범위? 당연히 코루틴의 범위이다. 코루틴 블록으로 코루틴을 제어할 수 있다.
            - GlobalScope는 CoroutineScope의 한 종류이다. 미리 정의된 방식으로 프로그램 전반적으로 백드라운드에서 동작한다.

        * ### CoroutineContext
            - CoroutineContext는 코루틴을 어떻게 처리 할 것인지에 대한 여러가지 정보의 집합이다. 다들 자주 봤던 요소들로는 Job과 Dispatcher가 있다.
        
        * ### Dispatcher
            - Dispatcher는 CoroutineContext의 주요 요소이다. CoroutineContext를 상속받아서 어떤 스레드를 사용 하여 어떻게 동작 할 것인지를 정한다. Dispatcher에는 3가지 종류가 있는데 다음과 같다.

                * __Dispatchers.Default__ : CPU 사용량이 많은 작업에 주로 사용한다. UI 스레드 즉, 메인 스레드에서 처리하기 버겁고 긴 작업들에 알맞은 형식이다.

                * __Dispatchers.IO__ : 네트워크, 디스크 사용 할때 사용한다. 파일을 읽고, 쓰고, 소켓을 읽고, 쓰고 작업을 멈추는것에 최적화되어 있는 스레드이다.

                * __Dispatchers.Main__ : MainDispatcher는 흔히 UI 스레드라고 불리우는, View를 다루는 작업을 담당한다. 안드로이드 자체에서도 메인 스레드는 UI 스레드이고 Dispatcher.Main은 UI 스레드를 사용하여 동작한다. Dispatchers.Main을 사용할 수 없는 플랫폼도 존재하며, 이러한 플랫폼에서 사용했을 시 IllegalStateException이 발생한다.
                
                > Coroutine 공식 문서를 보면 Dispatchers.Unconfined라는 녀석도 제공한다. 이 Dispatcher는 다른 Dispatcher와 다르게 특정 스레드 또는 특정 스레드 풀을 지정하지 않는다. 일반적으로는 사용하지 않으며 특정 목적을 위해서만 사용하는데 나도 아직 들어보기만 하고 사용 해 본 적은 없다..
                
## 2️⃣ 코루틴과 안드로이드!
 * 코루틴은 다음과 같이 사용할 수 있다.

    1. 사용할 Dispatcher를 선택하고

    2. Dispatcher를 이용해서 CoroutineScope를 만들어주고

    3. CoroutineScope의 launch 또는 async 시에 수행 할 코드 블록을 지정 해 주면 된다.

    - launch와 async는 Coroutine의 확장함수이며, 넘겨받은 코드 블록으로 코루틴을 생성하고 실행 해 주는 코루틴의 빌더이다.
    - launch는 Job 객체를, async는 Deferred 객체를 반환하며 이 객체를 사용하여 수행 결과를 받거나, 작업이 끝나기를 대기하거나 취소하는 등의 제어가 가능하다.
    > [코루틴 제어 (launch, async, Job, Deffered, runBlocking)]()
    
 * 안드로이드에서 코루틴을 실행하는 기본적인 형태

    ```kotlin
    // 이 CoroutineScope 는 메인 스레드를 기본으로 동작합니다
    // Dispatchers.IO 나 Dispatchers.Default 등의 다른 Dispatcher 를 사용해도 됩니다
    val scope = CoroutineScope(Dispatchers.Main)

    scope.launch {
        // Main thread에서 동작하는 포그라운드 작업
    }

    scope.launch(Dispatchers.Default) {
        // CoroutineContext 를 변경하여 백그라운드로 전환하여 작업을 처리합니다
    }
    ```

    - 다음과 같이 Dispatcher를 변경 해 주는 것 만으로 포그라운드, 백그라운드를 오가며 작업 할 수 있다. 아래 코드는 위 코드와 다르게 오직 백그라운드에서만 동작한다.

    ```kotlin
    val scope = CoroutineScope(Dispatchers.Main)

    CoroutineScope(Dispatchers.Default).launch {
        // 새로운 CoroutineScope 로 동작하는 백그라운드 작업
    }

    scope.launch(Dispatchers.Default) {
        // 기존 CoroutineScope 는 유지하되, 작업만 백그라운드로 처리
    }
    ```

    - 기존 scope 변수에 CoroutineScope를 할당시켜 놓고(Main Thread), 새로운 CoroutineScope로 동작하는 launch 블록을 생성한다. 이는 위의 scope 변수의 코루틴 범위와는 다른 제어 범위를 가지게 된다. 아래 생성된 두 번째 블록에서는 scope 변수의 CoroutineScope는 유지되면서 작업이 처리되는 스레드만 변경이 된다.
    다음과 같은 상황이 발생한다면 내부 코루틴 블록은 멈추지 않는 상황이 발생한다.

    ```kotlin
    val scope = CoroutineScope(Dispatchers.Main)

    val job = scope.launch {

      CoroutineScope(Dispatchers.Main).launch {
          // 외부 코루틴 블록이 취소 되어도 끝까지 수행됨
      }
    }

    // 외부 코루틴 블록을 취소
    job.cancel()
    ```

    - 위 예제는 외부 코루틴 블록의 내부에 새로운 CoroutineScope를 만든다. 외부 블록의 제어범위와 내부 블록의 제어범위는 완전히 달라지게 되고, 최종적으로 job 오브젝트의 cancel() 함수는 자신이 해당하는 즉, 외부 Coroutine 블록을 멈춰 줄 수는 있지만, 내부 코루틴 블록은 다른 CoroutineScope로 분리되었기 때문에 멈추지 않는다.

_마지막 수정 2021.12.16(지속 업데이트 예정)_