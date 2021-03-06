---
layout: post
title:  "[RxKotlin] Hot๐ฅ & Coldโ๏ธ Observable"
date: 2021-09-14 15:34:10 +0700
categories: [RxKotlin]
---

## Hot Observable๊ณผ Cold Observable์ ์ฐจ์ด

`Reactive Programming`์ ๋ฐฐ์ฐ๋ฉด์ ๊ฐ์ฅ ๋ง์ด ์ ํ ๊ฐ๋์ด `Flow`๊ฐ ์ด๋ป๊ฒ ํ๋ฌ๊ฐ๋์ง์ด๋ค.
`Observable`์ ์ฐ๋ฆฌ์๊ฒ ๋ฐ์ดํฐ๋ฅผ ๋ฐฐ์ถ ์ํค๋ ์กด์ฌ์ด๊ณ , ์ฐ๋ฆฌ๋ ์ด๋ฅผ `subscribe` ํจ์ผ๋ก์จ ๋ฐฐ์ถ๋๋ ๋ฐ์ดํฐ๋ฅผ ์ป์ ์ ์๋ค.

## Cold Observable
๋จผ์  `cold observable`๋ถํฐ ์ค๋ชํ์๋ฉด, ์์ ๋ค๋ค๋ ๊ธฐ๋ณธ์ ์ธ ์์ ๋ค์ฒ๋ผ ๋์ผํ ์ต์ ๋ฒ๋ธ์ ์ฌ๋ฌ๋ฒ ๊ตฌ๋ํด๋ ๊ทธ ์์ ์ ํญ์ ์๋ก์ด ๋ฐ์ดํฐ flow๋ฅผ ์ป์ ์ ์์๋ค.

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
### ์ถ๋ ฅ๊ฒฐ๊ณผ ###

Recieved String 1<br/>
Recieved String 2<br/>
Recieved String 3<br/>
Done<br/>
Recieved String 1<br/>
Recieved String 2<br/>
Recieved String 3<br/>
Done<br/>

---

์์ ์์ ์ฒ๋ผ ์ฌ๋ฌ๋ฒ ๊ตฌ๋์ ํ๋๋ผ๋ ๊ตฌ๋์ ํ ์์ ๋ถํฐ๋ `list`์ ์์์ธ `"String 1", "String 2", "String 3"` ๊ฐ์ ์ป์ ์ ์์๋ค.
์ด๋ฌํ ํน์ง์ ๊ฐ์ง๊ณ  ์๋ Observable์ `Cold Observable`์ด๋ผ๊ณ  ํ๋ค.


## Hot Observable

`Hot Observalbe`์ old์ ๋ฐ๋๋ก ๊ตฌ๋์ ํ๊ธฐ ์  ๋ถํฐ ๋ฐ์ดํฐ๋ฅผ ๋ด๋ณด๋ด๊ธฐ ์์ํ๋ค.
`Cold Observable`์ CD๋ ๋ ์ฝ๋๋ก ๋ณธ๋ค๋ฉด `Hot Observable`์ TV ์ฑ๋์ ํน์ง๊ณผ ๋น์ทํ๋ค. ์ด๋ ๊ฒฐ๊ตญ ๊ตฌ๋์ ํ์ง ์์๊ณ  ๋ฐ์ดํฐ๋ฅผ ๋ฐฐ์ถ ์ํค๊ธฐ ๋๋ฌธ์ ๊ตฌ๋์ ํ  ํ์๊ฐ ์๋ค. ์ฌ๊ธฐ์ ์ค์ํ `Observable`์ ๊ฐ๋์ด ๋ฐ๋ก `ConnectableObservable`์ด๋ค. ์ด `Observable`์ `cold`๋ฅผ `hot`์ผ๋ก ๋ฐ๊ฟ ์ ์๊ธฐ๋ ํ๋ค.

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
### ์ถ๋ ฅ๊ฒฐ๊ณผ ###

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

์์ ์ถ๋ ฅ ๊ฒฐ๊ณผ๋ฅผ ๋ณด๋ฉด 1, 2 ๊ตฌ๋์๊ฐ ๊ตฌ๋์ ํ ํ `connect` ํ ์์  ์ฆ, ๋ฐ์ดํฐ๊ฐ ๋ฐฐ์ถ๋๊ธฐ ์์ํ ์์ ๋ถํฐ `500ms`๋ฅผ ๋๊ธฐ ํ๋ฉด์ ์ด 5๊ฐ์ ๋ฐ์ดํฐ๋ฅผ ๋ฐ๊ฒ ๋๋ค.
๊ทธ 5์ด๊ฐ ์ง๋ฌ์ ๋ ๋ถํฐ ๋ ๋ค๋ฅธ 3 ๊ตฌ๋์๊ฐ ๊ตฌ๋ํ๊ธฐ ์์ํ๋ฉด์ ๋ค์ `500ms` ๊ฐ `delay`๋ฅผ ์ฃผ๊ฒ ๋๋ค. ์ด๋ ๊ฒ ์ด 10๊ฐ์ ๋ฐ์ดํฐ๋ฅผ ์๊ฐ ์์์ ๋ฐ๋ผ ๋ฐ์ ์ฌ ๊ฒ์ด๊ณ ,
์ถ๋ ฅ ๊ฒฐ๊ณผ๊ณผ ๊ฐ์ด 3๋ฒ ๊ตฌ๋์๋ ์ด ์ ์ 1, 2 ๊ตฌ๋์๊ฐ ๋ฐ์๋ ๋ฐ์ดํฐ๋ ๋ฐ์ง ๋ชป ํ ๊ฒ์ ์ ์ ์๋ค. ์ด๊ฒ์ด ๋ฐ๋ก `Hot Observable`์ ๊ฐ๋์ด๋ค.
 
 ๊ฒฐ๊ตญ `hot observable`๊ณผ `cold observable`์ ์ฐจ์ด๋ ๊ตฌ๋ ํ ์์ ์ ๋ค์ ์๋ก์ด ๋ฐ์ดํฐ๋ฅผ ๋ฐ์์ค๋๋, ์ด๋ฏธ ์ง๋๊ฐ ๋ฐ์ดํฐ๋ ๋ฐ์์ค์ง ๋ชปํ๊ณ  ์ค๊ฐ์ ํ๋ฆ์ ๊ปด์ ๋ฐ์์ค๋๋์ ์ฐจ์ด์ด๋ค.