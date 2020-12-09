---
layout: post
title:  "[자료구조] 2. Linked List(연결 리스트)"
date:   2020-12-09 18:34:10 +0700
categories: [자료구조]
---

자료구조의 개념과 간략한 설명에 대해서는 [이 블로그의 다른 포스팅 - 꼭 알아두어야 할 자료구조](https://choheeis.github.io/newblog//articles/2019-07/BasicDataStructure) 에서 볼 수 있다.

✍🏻 [바킹독 실전 알고리즘 강의 - 4강 연결 리스트편](https://blog.encrypted.gg/932?category=773649) 을 참고하여 작성합니다.

## 1️⃣ Linked List(연결 리스트)의 형태

[이 블로그의 다른 포스팅 - 1. Array(배열)](https://choheeis.github.io/newblog//articles/2020-12/data-structure-array) 을 보고 왔다면 배열이 연속된 메모리 상에 데이터를 저장한다는 것을 알고 있을 것이다.

하지만 __Linked List(연결 리스트)__ 는 배열과 다르게 __데이터를 메모리 상의 이곳 저곳에 연속되지 않게 저장한다.__ 이 때, 각 원소는 다음 원소의 메모리 주소를 함께 저장하고 있다.

![단방향리스트](https://user-images.githubusercontent.com/31889335/61359643-b8895400-a8b7-11e9-89bc-44f64bd6213d.PNG)

Linked List(연결 리스트)의 맨 첫 번째 원소의 위치만 알고 있어도 나머지 원소들의 위치는 각 원소에 저장되어 있으므로 결국 모든 원소의 위치를 알 수 있게 되는 것이다.

따라서 Linked List(연결 리스트)는 메모리 상에 데이터가 연속적이지는 않지만 각 원소가 어디에 위치해 있는지 알 수 있다.

Linked List(연결 리스트)의 종류에는 단방향 연결 리스트(Singly Linked List), 이중 연결 리스트(Doubly Linked List), 원형 연결 리스트(Circular Linked List) 가 있다.

각각에 대해서는 이 포스팅을 끝까지 읽어보면 알게 될 것이다.

## 2️⃣ Linked List(연결 리스트)의 특징

1. __원소들이 메모리 상에 연속되어 있지 않아 Cache hit rate가 낮지만 할당이 다소 쉽다.__

> 위 성질은 다음에 알아보고 보충하자.

## 3️⃣ Linked List(연결 리스트)에서 사용할 수 있는 연산들

1. __임의의 위치에 있는 원소를 확인하거나 변경하는 연산 : O(N)의 시간이 걸림__

    <img width="929" alt="01" src="https://user-images.githubusercontent.com/31889335/101609142-f2796e00-3a49-11eb-8f07-db9d06db913a.png">

    만약 위 그림에서 20을 저장하고 있는 원소를 확인하고 싶다면 20을 저장하는 원소의 위치를 알아야 할 것이다.

    이 위치를 어떻게 알 수 있을까?

    가장 먼저 첫 번째 원소인 10을 저장하고 있는 원소를 방문해서 다음 원소인 5가 존재하는 위치를 확인해야 한다.(연결 리스트를 사용할 때는 가장 첫 번째 원소의 위치만 알고 있게 된다.)

    확인 후, 5를 저장하는 원소를 찾아가 방문한다.

    5를 저장하는 원소를 통해 4를 저장하는 원소의 위치를 알게 되고, 4를 저장하는 원소를 찾아가 방문한다.

    4를 저장하는 원소를 통해 비로소 20을 저장하는 원소의 위치를 알게 되어 20에 찾아갈 수 있다.

    [이 블로그의 다른 포스팅 - 1. Array(배열)](https://choheeis.github.io/newblog//articles/2020-12/data-structure-array) 를 보면 배열에서 k번째 원소를 확인하거나 변경하기 위해서는 간단한 주소값 관련 사칙연산 계산만 하면 되므로 O(1)이 걸린다는 것을 알 수 있을 것이다.

    하지만 Linked List(연결 리스트)는 배열과 다르게 k번째 원소의 위치를 알아내기 위해서는 원소를 타고 타고 방문해야 k번째 원소의 위치를 알 수 있기 때문에 O(k)의 시간이 걸린다.

    이 과정을 통해 k번째 원소가 있는 위치에 도착했다면 해당 원소의 data를 확인하거나 변경하면 된다.

2. __임의의 위치에 원소를 추가하는 연산 : O(1)의 시간이 걸림__

    임의의 위치에 원소를 추가하려면 추가할 원소를 메모리의 어느 위치에 저장한 후, 이전 원소가 가지고 있던 다음 원소 주소를 방금 추가한 원소의 위치로 바꿔주면 된다.

    <img width="925" alt="04" src="https://user-images.githubusercontent.com/31889335/101613779-81d55000-3a4f-11eb-825b-7e52f2aae199.png">

    위 그림처럼 기존에 저장하고 있던 다음 원소 위치를 새로 추가한 원소의 위치로 업데이트 해주기만 하면 되므로 O(1)의 시간이 걸린다.

    단, O(1)이 시간이 걸린다는 것은 O(N)에 걸려 추가할 원소의 바로 이전 원소까지 방문된 상태에서 이 원소의 주소 값만 없데이트 하는데 O(1)이 걸린다는 것이다.

3. __임의의 위치에 있는 원소를 제거하는 연산 : O(1)의 시간이 걸림__

    임의의 위치에 있는 원소를 제거하려면 원소를 제거한 후, 이전 원소가 가지고 있던 다음 원소 주소를 제거한 원소의 다음 원소 주소로 업데이트 해주면 된다.

    <img width="930" alt="05" src="https://user-images.githubusercontent.com/31889335/101614566-76365900-3a50-11eb-9c9b-af9917b98de3.png">

    위 그림처럼 기존에 저장하고 있던 다음 원소 위치를 제거한 원소의 다음 원소 위치로 업데이트 해주기만 하면 되므로 O(1)의 시간이 걸린다.

    단, O(1)이 시간이 걸린다는 것은 O(N)에 걸려 제거할 원소의 바로 이전 원소까지 방문된 상태에서 이 원소의 주소 값만 없데이트 하는데 O(1)이 걸린다는 것이다.
    
## 3️⃣ Linked List(연결 리스트)의 종류
    
1. __단일 연결 리스트(Singly Linked List)__

    단일 연결 리스트는 __각 원소가 자신의 다음 원소의 주소를 저장하고 있는 연결 리스트__ 이다.

    <img width="929" alt="01" src="https://user-images.githubusercontent.com/31889335/101609142-f2796e00-3a49-11eb-8f07-db9d06db913a.png">

2. __이중 연결 리스트(Doubly Linked List)__

    이중 연결 리스트는 __각 원소가 자신의 이전 원소와 다음 원소의 주소를 둘 다 저장하고 있는 연결 리스트__ 이다.

    <img width="939" alt="02" src="https://user-images.githubusercontent.com/31889335/101610931-379e9f80-3a4c-11eb-9232-5f6f460c32ba.png">

    단일 연결 리스트에서는 각 원소의 다음 위치만 알아낼 수 있지만 이중 연결 리스트를 사용하면 각 원소의 이전 위치까지 알아낼 수 있다.

    하지만 각 원소각 이전 위치까지 저장해야 하므로 메모리를 단일 연결 리스트보다 더 사용한다는 단점이 있다.

3. __원형 연결 리스트(Circular Linked List)__

    원형 연결 리스트는 __가장 마지막 원소가 맨 처음 원소의 주소를 저장하고 있는 연결 리스트__ 이다.

    즉, 맨 끝과 맨 처음이 연결되어 있는 형태이다.

    <img width="935" alt="03" src="https://user-images.githubusercontent.com/31889335/101611405-cf9c8900-3a4c-11eb-9f64-1b25a0153e22.png">

## 4️⃣ Array(배열)과 Linked List(연결 리스트)의 공통점과 차이점

* __공통점__

    배열과 연결 리스트는 원소들이 메모리 상에 저장되는 형태가 다르지만 어쨌든 둘 다 원소들 간의 선후 관계가 일대일로 명확한 자료구조이다.

    즉, 원소들 사이에서 첫 번째 원소, 두 번째 원소, ... 등의 개념이 존재한다는 것이다.

    이러한 자료구조를 __선형 자료구조__ 라고 부르고 배열과 연결 리스트는 둘 다 선형 자료구조이다.

* __차이점__

    * 임의의 원소(k번째 원소)에 접근하여 데이터 확인 및 변경하는데 걸리는 시간복잡도

        * 배열 : __O(1)__

        * 연결 리스트 : __O(k)__

    * 임의의 위치에 원소를 추가하거나 임의의 위치에 있는 원소를 제거하는데 걸리는 시간복잡도

        * 배열 : __O(k)__

        * 연결 리스트 : __O(1)__

    * 메모리 상의 배치

        * 배열 : 연속적으로 배치

        * 연결 리스트 : 비연속적으로 배치

    * 추가 메모리 필요성

        * 배열 : 추가 메모리 없음

        * 연결 리스트 : 이전/다음 원소의 위치 주소를 저장할 추가 메모리가 N(원소의 개수)에 비례한 만큼 필요함

    * 언제 사용할까?

        * 배열 : 배열에 저장된 원소를 빠르게 찾아야 하는 경우

        * 연결 리스트 : 중간 중간에 원소를 새로 추가하거나 제거해야 하는 작업이 많은 경우

# 끝!