---
layout: post
title:  "[Kotlin] 코틀린 Iterator에 대해서!"
date:   2020-12-11 18:34:10 +0700
categories: [kotlin]
---

## 1️⃣ Iterator가 무엇인가?

[이 블로그의 다른 포스팅 - 2. Linked List(연결 리스트)](https://choheeis.github.io/newblog//articles/2020-12/data-structure-linked-list) 를 먼저 읽어보고 와야 한다.

읽어보고 왔다면 Linked List에서 임의의 위치에 원소를 추가하거나 삭제하는데는 O(N) + O(1)의 시간복잡도가 걸린다는 사실을 알 수 있을 것이다.

즉, 만약 아래와 같은 코드를 작성했다고 가정해보자.

~~~kotlin
fun main() {
    // Linked List에 데이터 4개 삽입
    var linkedList = LinkedList<Int>()
    linkedList.add(1)
    linkedList.add(2)
    linkedList.add(3)
    linkedList.add(4)

    // 마지막 원소 확인
    print(linkedList[3])
    print(linkedList[2])
}
~~~

먼저 첫 번째 출력을 해결해보자.

linkedList[3]은 위 코드의 Linked List(연결 리스트)의 가장 마지막 원소를 확인하는 코드이다.

Linked List(연결 리스트)에서 가장 마지막 원소를 확인하는 과정은 어떻게 될까?

먼저 첫 번째 원소에 방문하여 두 번째 원소의 위치를 확인해야 한다.

두 번째 원소에 방문하여 세 번째 원소의 위치를 확인해야 한다.

세 번째 원소에 방문하여 가장 마지막 원소의 위치를 확인한다.

가장 마지막 원소에 방문하여 linkedList[3]에 대한 데이터를 얻음으로써 출력을 완료할 수 있다.

이제 두 번째 출력을 해결해보자.

linkedList[2]는 위 코드의 Linked List(연결 리스트)의 세 번째 원소를 확인하는 코드이다.

이번에도 마찬가지로 첫 번째 원소부터 차례대로 방문하여 세 번째 원소 위치를 알아내야 한다.

즉, Linked List(연결 리스트)는  __임의의 위치에 존재하는 원소를 확인하려면 가장 첫 번째 원소부터 차례대로 방문해야 한다__ 는 특징이 있다.

하지만 이 과정의 시간복잡도는 __O(N)__ 이기 때문에 속도가 느리고, 위 코드와 같이 Linked List 의 원소를 검색하는 작업이 많으면 시간이 배로 늘어난다.

__Linked List의 검색 속도를 조금 더 빠르게 할 순 없을까?__

빠르게하기 위한 것이 바로 __Iterator__ 이다.

Iterator는 List 형태 자료 구조의 __각 원소 접근 방법을 일반화한 것__ 이다. 따라서 List 형태의 자료 구조에서 원소 검색 작업을 할 때 유용하게 사용된다.

> Linked List가 아닌 List가 무엇인지 궁금하다면? [이 블로그의 다른 포스팅 - 3. List(리스트)](https://choheeis.github.io/newblog//articles/2020-12/data-structure-list) 에 대해서 보면 된다!

## 2️⃣ Iterator 함수가 정의되어 있는 곳

Iterator의 작동 원리를 알아보기 전에 [이 블로그의 다른 포스팅 - 3. List(리스트)](https://choheeis.github.io/newblog//articles/2020-12/data-structure-list) 를 먼저 읽고, List 인터페이스가 무엇인지 알아보고 와야 한다.

List 인터페이스에 대해 알아보았다면 List 인터페이스에 정의된 함수들이 어떤 것들이 있는지 보았을 것이다.

> 코틀린과 자바가 거의 비슷하기 때문에 더 자세히 나와있는 Java 도큐먼트를 참고하였다.

<img width="1006" alt="01" src="https://user-images.githubusercontent.com/31889335/101933519-ab44d600-3c1f-11eb-96bb-29a3c07794ef.png">

위 함수들 중 초록색으로 표시한 함수들을 보면 __iterator()__, __listIterator()__ 라는 함수가 있다.

__즉, List 인터페이스를 구현하는 다양한 List관련 자료구조에서 iterator(), listIterator() 함수를 구현하고 있고, 사용할 수 있다는 것이다.__

List 인터페이스가 부모 인터페이스로 [Iterable](https://docs.oracle.com/javase/8/docs/api/java/lang/Iterable.html) 이라는 인터페이스를 상속하고 있는데

<img width="978" alt="03" src="https://user-images.githubusercontent.com/31889335/101656422-05119880-3a86-11eb-93c5-dbaaf0cdc2aa.png">

이 Iterable 인터페이스 안에 

<img width="974" alt="02" src="https://user-images.githubusercontent.com/31889335/101940494-f06e0580-3c29-11eb-8d00-712bbfa50d93.png">

iterator() 라는 메소드가 정의되어 있다.

먼저 __Iterable 인터페이스__ 에 대해서 알아보자.

<img width="829" alt="04" src="https://user-images.githubusercontent.com/31889335/101941670-adad2d00-3c2b-11eb-8a63-13aea2dc1649.png">

위 설명에서 알 수 있듯이, Iterable 인터페이스는 객체가 for-each 반복문의 타겟이 될 수 있게 하려고 구현된 인터페이스이다.

여기서 객체라는 것은 배열이나 리스트 같이 여러 원소들을 저장할 수 있는 객체를 말한다.

따라서 해당 객체를 구현하고 있는 Class가 Iterable 인터페이스를 구현하고 있다면 해당 객체는 For-each 반복문을 사용해 원소들을 순회할 수 있는 것이다.

> 💁🏼‍♀️ __For-each 반복문이란?__
>
> For-each 반복문은 어떤 객체가 저장하고 있는 모든 원소를 처음부터 가장 마지막까지 순서대로 순회하는 반복문이다.

따라서 아래 그림처럼 Iterable 인터페이스를 구현하고 있는 클래스(자료구조)는 정말 다양하다!

<img width="1020" alt="03" src="https://user-images.githubusercontent.com/31889335/101941521-72126300-3c2b-11eb-8f00-1d7966a30a7c.png">

Iterable 인터페이스에 정의된 메소드를 더 살펴보자.

<img width="1026" alt="05" src="https://user-images.githubusercontent.com/31889335/101942244-8c007580-3c2c-11eb-8354-5dde625bcc2e.png">

가장 먼저 forEach 반복문이 정의되어 있다.

그 다음으로는 __iterator()__ 라는 추상 메소드가 정의되어 있다.(이게 우리의 주목적!)

Iterable 인터페이스를 구현하는 클래스에서는 위 메소드들을 반드시 구현해야 한다. 따라서 Iterable 인터페이스의 목적은 iterator() 추상 메소드를 subClass(자식클래스)에서 반드시 구현하도록 하기 위함이다.

iterator() 추상 메소드는 __[Iterator](https://docs.oracle.com/javase/8/docs/api/java/util/Iterator.html)__ 라는 인터페이스를 반환한다. 

> 아ㅏㅏㅏㅏ 다시하기

이 메소드

따라서 Iterable 클래스를 구현하고 있는 subClass(자식클래스)에서는 iterator

이 메소드는 

따라서 List 인터페이스에 정의된 함수 목록에 iterator() 함수가 존재하는 것이다.

## 3️⃣ Iterator() 함수로 할 수 있는 작업들

위에서 iterator() 함수는 






# 끝!