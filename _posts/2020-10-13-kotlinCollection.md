---
layout: post
title:  "[Kotlin] 코틀린의 Collection"
date:   2020-10-13 18:34:10 +0700
categories: [kotlin]
---

> 사실 이 포스팅은 코틀린의 List, ArrayList, MutableList 등을 먼저 공부한 후 작성하는 것이다.
> 
> 위의 것들을 공부하다 보니 Collection 패키지에 대해 궁금하게 되었고, 한 번 공부를 해야겠다고 생각했다.

## 0️⃣ Collection이 뭔가?

✍🏻 [kotlin 도큐먼트 - Collection 개요](https://kotlinlang.org/docs/reference/collections-overview.html) 를 참고하여 작성합니다.

Collection이라는 것은 코틀린 외에도 다양한 프로그래밍 언어에 존재하는 개념인데 __여러 원소를 저장할 수 있는 자료구조__ 를 통틀어 말하는 개념이다.

여러 원소를 저장할 수 있는 자료구조에는 보통 __Array(배열)__, __List(리스트)__, __Map__, __Set__ 등이 포함된다.

하지만 프로그래밍 언어마다 Collection 개념에 포함하는 자료구조들이 조금씩 다를 수 있다.

## 1️⃣ Kotlin(코틀린)의 Collection에 대해서!

위에서 언급한 것과 같이, 프로그래밍 언어마다 Collection 개념에 포함되는 자료구조의 종류가 조금씩 다를 수 있다.

Kotlin(코틀린)에서는 __List__, __Set__, __Map__ 이 3가지 자료구조가 Collection 개념에 포함된다.

> 각 자료구조에 대해서는 이 블로그의 자료구조 카테고리에서 알아볼 수 있을 것이다. 😁

Kotlin(코틀린)은 이러한 Collection 개념에 속하는 자료구조들을 쉽게 생성하고, 조작하고, 관리할 수 있도록 __Kotlin Standard Library(코틀린 표준 라이브러리)에 각 자료구조와 관련된 인터페이스, 클래스, 메소드들을 제공__ 하고 있다.

> 코틀린 표준 라이브러리가 무엇인지는 [이 블로그의 다른 포스팅 - Kotlin Standard Library(코틀린 표준 라이브러리)](https://choheeis.github.io/newblog//articles/2020-12/kotlin-stdlib) 를 읽어보면 알 수 있을 것이다 😃

Collection에 포함되는 각 자료구조와 관련된 인터페이스, 클래스, 메소드들을 Kotlin Standard Library 안의 __[kotlin.collections](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/)__ 패키지 안에 들어있다.

## 2️⃣ kotlin.collections 패키지에 구현되어 있는 자료 구조들은 어떻게 구현되어 있나?

Kotlin Standard Library는 Collection 개념에 속하는 list, set, map 과 관련된 자료구조들을 구현하여 제공하고 있다.

이 자료구조들을 구현하고 있는 Class에서는 아래와 같은 2가지 특징으로 분류되는 인터페이스들을 구현하고 있다.

* __read-only 특징을 갖는 인터페이스__ : 이 인터페이스를 구현하는 자료구조는 각 원소가 저장하고 있는 데이터를 수정할 수 없다. 데이터 확인만 가능하다.

* __mutable 특징을 갖는 인터페이스__ : 이 인터페이스를 구현하는 자료구조는 read-only 인터페이스를 확장한 것으로 각 원소가 저장하고 있는 데이터를 수정할 수도 있다. (원소 확인, 원소 추가, 원소 삭제, 원소 수정이 모두 가능하다)

    알아두면 좋은 것은 mutable 특징을 갖는 인터페이스를 구현하는 자료 구조는 원소를 변경하기 위해 굳이 __var__ 키워드로 선언하지 않아도 된다는 것이다.

    __val__ 키워드로 선언해도 된다.

    ~~~kotlin
    fun main() {
        // 1) mutable 인터페이스를 구현하는 mutableList라는 자료구조를 val 키워드를 사용하여 선언하였다.
        // 2) 3개의 원소에 각각 1, 2, 3 이라는 Int 형 데이터를 저장하였다.
        val mutableList = mutableListOf<Int>(1, 2, 3)

        // 3) 저장된 원소를 확인하기 위해 출력 코드를 작성하였다.
        // 출력결과 ) 1(개행) 2(개행) 3(개행)
        mutableList.forEach{
            println(it)
        }

        // 4) val로 선언했던 mutableList 자료구조에 새로운 원소 추가 작업을 하였다.
        mutableList.add(4)
        mutableList.add(5)

        // 5) 저장된 원소를 확인하기 위해 출력 코드를 작성하였다.
        // 출력결과 ) 1(개행) 2(개행) 3(개행) 4(개행) 5(개행)
        mutableList.forEach {
            println(it)
        }
    }
    ~~~

    <img width="27" alt="05" src="https://user-images.githubusercontent.com/31889335/101980263-93189980-3ca7-11eb-80fc-8d270cd198f5.png">

    출력 결과는 위와 같다.

    출력 결과를 통해 __val__ 키워드로 선언해도 새로운 원소 추가가 정상적으로 작동하는 것을 확인할 수 있다.

    하지만 __val__ 키워드로 선언한 mutable 자료구조에는 __재할당이 불가__ 하다.

    <img width="421" alt="06" src="https://user-images.githubusercontent.com/31889335/101980332-e4288d80-3ca7-11eb-96ba-468558fa7d7d.png">

    이렇게 컴파일 에러가 난다.

    따라서 mutable 특징을 갖는 인터페이스를 구현하는 자료구조가 재할당을 해야할 경우에는 __var__ 키워드를 사용해서 선언해야 한다.

    ~~~kotlin
    fun main() {
        // 1) var 키워드로 mutableList 자료구조 선언
        var mutableList = mutableListOf<Int>(1, 2, 3)
        mutableList.forEach{
            println(it)
        }

        // 2) 재할당 작업
        mutableList = mutableListOf<Int>(1)
        mutableList.forEach {
            println(it)
        }
    }
    ~~~

    <img width="34" alt="07" src="https://user-images.githubusercontent.com/31889335/101980381-43869d80-3ca8-11eb-8e8e-d79f78da154f.png">

    출력 결과는 위와 같다.

    출력 결과를 통해 __var__ 키워드로 선언하면 컴파일 에러 없이 재할당이 정상적으로 동작하는 것을 확인할 수 있다.

이렇게 kotlin.collections 패키지에 구현되어 있는 자료구조들은 read-only 특징을 갖는 인터페이스와 mutable 특징을 갖는 인터페이스를 구현하고 있음을 알게 되었다.

이제 read-only, mutable 특징을 갖는 인터페이스에는 실제로 어떤 인터페이스들이 있는지 알아보자! 

<img width="733" alt="08" src="https://user-images.githubusercontent.com/31889335/101980427-8e081a00-3ca8-11eb-81c4-d049e4c69f3f.png">

위 다이어그램은 kotlin.collections 패키지에서 제공하는 여러 자료구조들이 구현하고 있는 인터페이스를 나타낸다.

아래와 같이 분류할 수 있다.

* __read-only 특징을 갖는 인터페이스__ 

    * Iterable

    * Collection

    * List

    * Set

    * Map

* __mutable 특징을 갖는 인터페이스__

    * MutableIterable

    * MutableCollection

    * MutableList

    * MutableSet

    * MutableMap

또한 위 다이어그램에 존재하는 화살표는 각 인터페이스의 상속 관계를 나타낸다.

예를 들어, Collection 인터페이스는 Iterable 인터페이스를 상속하고 있다.

다이어그램의 진위여부를 하기 위해 [Collection 인터페이스](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-collection/) 문서를 확인해보자.

<img width="783" alt="09" src="https://user-images.githubusercontent.com/31889335/101980569-a88ec300-3ca9-11eb-90f5-5332b22ac41c.png">

정말 Collection 인터페이스는 Iterable 인터페이스를 SuperInterface(부모 인터페이스)로 가지고 있다!

이제 위 다이어그램에 존재하는 각 인터페이스를 구현하는 자료구조들의 종류에 대해 알아보자!

## 3️⃣ kotlin.collections 패키지가 제공하는 자료구조들!

kotlin.collections 패키지 안에 있는 자료구조들은 각 자료구조들을 공부한 내용의 다른 포스팅에서 알아보자!

* [List 형 자료구조들을 공부한 포스팅](https://choheeis.github.io/newblog//articles/2020-10/kotlinList)

* Map 분류의 자료구조들을 공부한 포스팅 --> 공부 전

* Set 분류의 자료구조들을 공부한 포스팅 --> 공부 전

# 끝!