---
layout: post
title:  "[Kotlin] 7️⃣ 코틀린의 List형 자료구조들"
date:   2020-10-12 18:34:10 +0700
categories: [kotlin]
---

## 0️⃣ List형 자료구조란?

이 포스팅을 읽기 전에 [이 블로그의 다른 포스팅 - 자료구조 List](https://choheeis.github.io/newblog//articles/2020-12/data-structure-list)와 [이 블로그의 다른 포스팅 - 코틀린의 Collection](https://choheeis.github.io/newblog//articles/2020-10/kotlinCollection) 을 보고 와야 한다.

보고 왔다면 List 자료구조가 무엇인지와 코틀린에서 List는 Collection형 자료구조가 구현하고 있는 인터페이스라는 것을 알게 되었을 것이다.

## 1️⃣ List 인터페이스

✍🏻 [kotlin 도큐먼트 - Collection 개요, List 인터페이스](https://kotlinlang.org/docs/reference/collections-overview.html#list) 를 참고하여 작성합니다.

코틀린에서 List 인터페이스는 무엇일까?

<img width="660" alt="06" src="https://user-images.githubusercontent.com/31889335/101981493-ada34080-3cb0-11eb-87f5-01dfbc78f501.png">

먼저 위 다이어그램은 kotlin.collections 패키지에서 제공하는 자료구조들이 구현하는 인터페이스를 나타내는 다이어그램이다.

여기에 List 인터페이스가 존재한다.

그럼 List 인터페이스가 왜 존재하는지와 List 인터페이스에는 어떤 메소드들이 정의되어 있는지 알아보자.

먼저 List 인터페이스는 __특정한 순서가 있는 선형 자료구조__ 를 구현할 때 필요한 함수들을 정의하고 있다. 따라서 이 인터페이스는 선형 자료구조를 구현할 때 사용되기 위해 존재한다.

List 인터페이스를 구현하는 자료구조들은 __index를 통해 자료구조에 저장되어 있는 원소에 접근__ 할 수 있다. 

> 💁🏼‍♀️ __선형 자료구조란?__
>
> 원소들 사이에 선후관계가 명확한 자료구조를 말한다.
>
> 예를 들어, 첫 번째 원소, 두 번째 원소, 세 번째 원소... 등의 개념이 존재하는 Array(배열) 자료구조나 LinkedList(연결 리스트) 자료구조가 선형 자료구조에 속한다.

또 [kotlin 도큐먼트 - List 인터페이스](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-list/) 를 봐보면 이 인터페이스 안에 정의되어 있는 함수들이 무엇인지 알 수 있을 것이다.

추가적으로 MutableList 인터페이스에 대해서도 간략히 알아보자.

위 다이어그램을 보면 알 수 있듯이, MutableList 인터페이스는 List 인터페이스를 상속하는 인터페이스이다.

다만 List 인터페이스는 read-only 특징을 갖고 있어 자료구조에 저장된 원소를 수정하거나 삭제할 수 없지만 Mutable 인터페이스는 mutable 특징을 갖고 있어 자료구조에 저장된 원소를 수정하거나 추가/삭제 작업을 할 수 있게 해준다.

## 2️⃣ List 인터페이스를 구현하고 있는 자료구조들(= List형 자료구조)

이제 위에서 알아본 List 인터페이스를 구현하고 있는 자료구조들은 어떤 것들이 있는지 알아보자. 

* __[ArrayList](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-array-list/)__

    __코틀린에서 List 인터페이스를 구현하고 있는 default 자료구조__ 는 __ArrayList__ 이다.

    하지만 ArrayList에 대한 문서를 보면
    
    <img width="696" alt="07" src="https://user-images.githubusercontent.com/31889335/101981494-b136c780-3cb0-11eb-8236-060a6a8e2f3a.png">

    실제로는 MutableList 인터페이스를 구현하고 있다.

    하지만 MutableList도 결국 List 인터페이스를 상속하고 있기 때문에 List 인터페이스를 구현하고 있는 default 자료구조가 ArrayList라고 말할 수도 있는 것이다.

    이제 ArrayList 자료구조의 특징에 대해 알아보자. ArrayList는 선형 자료구조이고, index로 각 원소 접근이 가능하다.

    또한 언제든지 새로운 원소를 추가하고, 변경하고, 삭제할 수 있다.

    하지만 __ArrayList는 자체적으로 메모리 효율성을 높이는 기능이 없는__ 선형 자료구조이다.

    이 말을 이해하기 위해 [c++의 vector 포스팅](https://choheeis.github.io/newblog//articles/2020-01/C++Vector)에서 vector가 동적으로 크기를 변경하는 과정을 읽어보면 좋다.

    vector는 초반에 임의의 크기의 배열을 만들어 놓고 데이터를 추가하다가 배열이 꽉 차게 되면 기존 배열의 복사본에 임의의 크기(2배 정도)를 더 늘린 새로운 배열을 만드는 형식으로 크기가 변경된다.

    하지만 이러한 방법으로 크기를 늘렸을 때 새로 늘린 크기 만큼의 메모리 공간에 데이터를 꽉 채워 추가하지 않는한 메모리 낭비가 생길 수 있다. 

    ArrayList도 vector와 같은 방법으로 데이터를 추가한다.
    
    그렇기 때문에 메모리 낭비가 발생할 수 있는데 ArrayList에는 이렇게 낭비되는 메모리를 효율적으로 관리하는 기능이 없다.

    예를 들어, 자바스크립트에서는 배열이 꽉 차서 길이를 늘려야 할 때 2배 정도 늘리는 것이 아니라 늘려야 할 만큼만 늘리는 기능이 있다. 따라서 메모리 낭비가 일어나지 않는다.

    ArrayList 자료구조에 사용할 수 있는 여러 조작 함수들과 확장 함수는 [kotlin 도큐먼트 - ArrayList](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-array-list/) 에서 확인할 수 있으니 필요할 때 적절하게 사용하면 된다!

## 3️⃣ MutableList 인터페이스를 구현하고 있는 자료구조들(= MutableList형 자료구조)

* __[ArrayList](https://kotlinlang.org/api/latest/jvm/stdlib/kotlin.collections/-array-list/)__

    위 List 인터페이스를 구현하고 있는 자료구조에서 설명했으므로 생략하겠다.

# 끝!

List 인터페이스를 구현하고 있는 자료구조와 MutableList 인터페이스를 구현하고 있는 자료구조는 공부를 하게 될 경우 계속 추가하자!