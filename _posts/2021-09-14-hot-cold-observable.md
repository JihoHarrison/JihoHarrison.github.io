---
layout: post
title:  "[RxKotlin] Hot🔥 & Cold❄️ Observable"
categories: [RxKotlin]
---

## Hot Observable과 Cold Observable의 차이

`Reactive Programming`을 배우면서 가장 많이 접한 개념이 `Flow`가 어떻게 흘러가는지이다.
`Observable`은 우리에게 데이터를 배출 시키는 존재이고, 우리는 이를 `subscribe` 함으로써 배출되는 데이터를 얻을 수 있다.

## Cold Observable
먼저 `cold observable`부터 설명하자면, 앞서 다뤘던 기본적인 예제들처럼 동일한 옵저버블을 여러번 구독해도 그 시점에 항상 새로운 데이터 flow를 얻을 수 있었다.

```kotlin
fun main(args : Array<String>){
    val observable : Observable<String> = listOf("String 1", "String 2", "String 3")
    .toObservable()

    observable.subscribe({
        println("Recieved $it")
    }, {
        println("Error ${it.message}")
    }, {
        println("Done")
    })
    
        observable.subscribe({
        println("Recieved $it")
    }, {
        println("Error ${it.message}")
    }, {
        println("Done")
    })
}
```
---
### 출력결과 ###

Recieved String 1<br/>
Recieved String 2<br/>
Recieved String 3<br/>
Done<br/>
Recieved String 1<br/>
Recieved String 2<br/>
Recieved String 3<br/>
Done<br/>

---

위의 예제처럼 여러번 구독을 하더라도 구독을 한 시점부터는 `list`의 요소인 `"String 1", "String 2", "String 3"` 값을 얻을 수 있었다.
이러한 특징을 가지고 있는 Observable을 `Cold Observable`이라고 한다.


## Hot Observable

`Hot Observalbe`은 old와 반대로 구독을 하기 전 부터 데이터를 내보내기 시작한다.
`Cold Observable`을 CD나 레코드로 본다면 `Hot Observable`은 TV 채녈의 특징과 비슷하다. 이는 결국 구독을 하지 않아고 데이터를 배출 시키기 때문에 구독을 할 필요가 없다. 여기서 중요한 `Observable`의 개념이 바로 `ConnectableObservable`이다. 이 `Observable`은 `cold`를 `hot`으로 바꿀 수 있기도 하다.

```kotlin
fun main(args: Array<String>) {
    val connectableObservable = Observable.interval(100, TimeUnit.MILLISECONDS)
        .publish()

    connectableObservable.subscribe({ println("subscription 1: $it") })
    connectableObservable.subscribe({ println("subscription 2: $it") })
    connectableObservable.connect()
    runBlocking{ delay(500) }

    connectableObservable.subscribe({ println("subscription 3: $it") })
    runBlocking{ delay(500) }
}
```
---
### 출력결과 ###

subscription 1: 0<br/>
subscription 2: 0<br/>
subscription 1: 1<br/>
subscription 2: 1<br/>
subscription 1: 2<br/>
subscription 2: 2<br/>
subscription 1: 3<br/>
subscription 2: 3<br/>
subscription 3: 3<br/>
subscription 1: 4<br/>
subscription 2: 4<br/>
subscription 3: 4<br/>
subscription 1: 5<br/>
subscription 2: 5<br/>
subscription 3: 5<br/>
---

위의 출력 결과를 보면 1, 2 구독자가 구독을 한 후 `connect` 한 시점 즉, 데이터가 배출되기 시작한 시점부터 `500ms`를 대기 하면서 총 5개의 데이터를 받게 된다.
그 5초가 지났을 때 부터 또 다른 3 구독자가 구독하기 시작하면서 다시 `500ms` 간 `delay`를 주게 된다. 이렇게 총 10개의 데이터를 시간 순서에 따라 받아 올 것이고,
출력 결과과 같이 3번 구독자는 이 전에 1, 2 구독자가 받았던 데이터는 받지 못 한 것을 알 수 있다. 이것이 바로 `Hot Observable`의 개념이다.
 
 결국 `hot observable`과 `cold observable`의 차이는 구독 한 시점에 다시 새로운 데이터를 받아오느냐, 이미 지나간 데이터는 받아오지 못하고 중간에 흐름에 껴서 받아오느냐의 차이이다.