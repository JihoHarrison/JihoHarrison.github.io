---
layout: post
title: "[안드로이드] 코루틴 제어 (With. Job, Deferred) 🎛"
date: 2021-12-13 23:45:01 +0700
categories: [안드로이드]
---

 ## 0️⃣ 프롤로그
 * 코루틴을 공부하면서 Coroutine에는 어떠한 주요 키워드 들이 있고, 제어를 하기 위해서는 어떻게 해야하는지, 코루틴 블록을 어떻게 생성하는지 등을 학습해왔다. 저번에 배웠던 개념에서 Job 객체, Deferred, runBlocking 등이 있었는데 더 자세히 짚고 넘어가기 위해 이렇게 정리를 진행하게 되었다.

 ## 1️⃣ Launch() -> Job
 ```kotlin
 val job : Job = launch {
     ...
 }
 ```

 * launch 함수로 생성된 코루틴 블록은 결국 Job 객체라는 것을 반환한다. 이렇게 반환받은 Job 객체로 코루틴 블록을 취소하거나 다음 작업의 수행 전 코루틴 블록이 완료되기 까지 기다리는 등의 동작이 가능하다.

 ```kotlin
 val job = launch {
    var i = 0
    while (i < 10) {
        delay(500)
        i++
    }
 }

 job.join() // 완료 대기
 job.cancel() // 취소
 ```

 * 위 코드에서는 join()이라는 함수로 기존에 실행되고 있던 launch 즉, 코루틴을 완료가 될 때 까지 기다려주는 작업을 해 주고 있다.
 * 하지만 launch로 여러개의 코루틴 블록을 실행 할 때가 있을텐데, 이 때는 각각의 job 객체에 대해  join() 함수를 걸어줘도 괜찮지만 _joinAll(job1, job2)_ 이런 식으로 한꺼번에 코루틴 블록을 종료시킬 수 있다.
 
 ```kotlin
 val job1 = launch {
     var i = 0
     while (i < 10) {
         delay(500)
         i++
     }
 }
 
 // 위 블록 과 같은 job1 객체를 사용
 launch(job1) {
    var i = 0
    while (i < 10) {
        delay(1000)
        i++
    }
 }
 
 // 같은 job 객체를 사용하게 되면
 // joinAll(job1, job2) 와 같다
 job1.join()
 ```

 * 여기서 launch 함수로 코루틴 블록을 생성했을 때, 코루틴 블록은 그 즉시 실행되며, 반환받은 Job 객체로 해당 블록을 제어한다. 말 그대로 제어만 할 수 있지 블록이 수행 된 결과를 반환받는 것은 아니다. 그렇다면 수행 된 결과를 반환받기 위해서는 어떻게 해야는지 알아보았다.

 ### Async() -> Deferred
 * 바로 async()라는 함수를 launch 대신 사용하면 된다. 이렇게 async() 함수로 시작된 코루틴 블록은 Deferred 객체를 반환한다.
 ```kotlin
 val deferred : Deferred<T> = async {
     ...
     T // 결과값
 }
 ```
 * 이 Deferred라는 객체는 launch() 함수를 이용하여 코루틴 블록을 만들어서 Job객체로 해당 코루틴 블록을 제어했던 것 처럼 async() 함수로 만들어진 코루틴 블록을 제어한다. 또한 제어가 가능한 것 뿐만 아니라 Job 객체와는 다르게 코루틴 블록 내에서 계산된 결과값을 반환받을 수 있다.
 ```kotlin
         val deferred : Deferred<String> = async {
            var i = 0
            while (i < 10) {
                delay(500)
                i++
            }

            "result"
        }
        
        val msg = deferred.await()
        println(msg) // result 출력
 ```
 * await() 함수를 통해서 async() 함수로 실행됐었던 코루틴 블록이 끝날 때 까지 기다려지고, 끝 마치는 순간 결과값이 반환되어 출력되는 형식이다.
 * launch() 함수를 실행했을 때 joinAll() 함수를 통해서 모든 코루틴 블록을 한꺼번에 대기 시키는 부분을 보았을 것이다. Deferred 객체도 마찬가지로 awaitAll() 함수를 이용하여 모든 async 코루틴 블록이 완료될 때 까지 기다릴 수 있다.
 * 또한 다음과 같이 첫번째 async 코루틴 블록에서 반환받은 Deferred 객체를 두번째 async() 함수의 인자로 사용하면, 동일한 Deferred 객체로 두개의 코루틴 블록을 모두 제어 할수 있다.
 ```kotlin
val deferred = async {
    var i = 0
    while (i < 10) {
        delay(500)
        i++
    }

    "result1"
} // 같은 Deferred 객체 사용

  async(deferred) {
    var i = 0
    while (i < 10) {
        delay(1000)
        i++
      
    "result2"
    }
}
val msg = deferred.await()
println(msg) // 첫번째 블록 결과인 result1 출력
 ```
 * 하지만 여기서 주의해야 할 점은 바로 여러개의 async 코루틴 블록에 같은 Deferred 객체를 사용할경우 await() 함수 호출시 전달되는 최종적인 결과값은 첫번째 async 코루틴 블록의 결과값 만을 전달한다는 것이다.

 ## 2️⃣ 지연 실행(LAZY)
 * 이 지연실행이라는 개념은 launch와 async 코루틴 블록 둘 다 모두에게 적용된다. 코루틴 블록의 처리 시점을 미룰 수 있다는 점이다. 각 코루틴 블록 함수의 start 인자에 CoroutineStart.LAZY 라는 값을 넣어주면 그 코루틴 블록은 지연되어 실행된다.
 
 ```kotlin
val job = launch (start = CoroutineStart.LAZY) {
    ...
}
 ```

OR

  ```kotlin
val deferred = async (start = CoroutineStart.LAZY) {
    ...
}
 ```
 
 * 이렇게 각각의 코루틴 블록들을 지연 실행시켜 준다면 Deferred 클래스는 해당 코루틴 블록을 실행시키려면 deferred.start() 함수나 deferred.await() 함수를 호출하는 시점에 코루틴 블록이 실행된다.
 * 반대로 launch() 함수로 만들어진, Job 객체를 반환하는 코루틴 블록에 대해서는 job.start() 함수나 job.join() 함수를 호출하는 시점에 launch 코루틴 블록이 실행된다.

 ### 지연된 async 코루틴 블록의 경우 start() 함수는 async 코루틴 블록을 실행 시키지만 블록의 수행 결과를 반환하지 않는다. 또한, await() 함수와 다르게 코루틴 블록이 완료되는 시점을 기다리지 않는다.

 ```kotlin
 
println("start")

 val deferred = async(start = CoroutineStart.LAZY) {
     var i = 0
     while (i < 5) {
         delay(500)
         println("lazy async $i")
         i++
     }
 }

 deferred.await()

 println("end")
 ```
 * 위 코드의 실행 결과로는 await() 함수를 적용하였기 때문에 다음고 같은 실행 결과를 얻을 수 있다.
 >start
lazy async 0
lazy async 1
lazy async 2
lazy async 3
lazy async 4
end

* 하지만 deferred.start() 로 바꾸면 출력 결과는 다음과 같다. end 는 start 가 출력 되자 마자 출력되고, 코루틴 블록이 수행된다.
>start
end
lazy async 0
lazy async 1
lazy async 2
lazy async 3
lazy async 4

_2021.12.14 최종 수정. 지속 업데이트 예정_