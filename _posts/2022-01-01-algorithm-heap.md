---
layout: post
title: "[알고리즘] 프로그래머스 더맵게 & HEAP 🎇"
date: 2022-01-01 21:51:01 +0700
categories: [Algorithm]
---

## 0️⃣ 프롤로그
 * 2022년이다! 알고리즘 문제를 본격적으로 풀기 시작한지 약 2개월이 흘렀지만 아직도 상당수의 문제들이 어렵게 느꺼진다. 항상 겸손한 마음가짐으로 CS 공부에 매진하는 내가 되겠다고 다짐하면서 만난 문제가 바로 **더 맵게**라는 문제다. 트리의 구조 중에 **Heap**이라는 형태를 이용한 우선순위 큐를 이용하는 문제였고, 처음에 **isEmpty()**에만 매달렸다가 헤맸던 문제다. 문제를 풀면서 우선순위 큐에 대해서도 공부하는 시간을 가지게 되었다.

## 1️⃣ 우선순쉬 큐
 * 일반적인 큐(Queue)의 형태는 먼저 들어간 데이터가 먼저 나오게 되는 스택과 반대되는 형태이다. **(FIFO)** 이러한 큐의 특성과 달리 우선순위 큐는 들어간 순서에 구애받지 않고 규칙에 따라 우선순위 라는 것을 부여받게 되고, 우선순위가 가장 높은 데이터가 가장 먼저 나오게 된다.

## 2️⃣ 사용할 수 있는 내장함수들
 1. **E peek()** : 큐의 맨 처음 원소를 삭제하지 않고 가져온다. 비어있으면 null을 반환한다.
 2. **Boolean offer(E a)** : 원소를 추가할 때 큐의 용량을 넘어서면 false를 반환한다. offer 키워드 대신 add로도 사용할 수 있다.
 3. **E poll()** : 큐의 맨 처음에 있는 원소를 "꺼내어" 가져온다. 한 번 사용하고 나면 큐에 남아있지 않는다. 또한 큐에 원소가 없으면 null을 반환한다.
 4. **E remove()** : 큐의 처음에 있는 원소를 제거한다. 큐에 원소가 없으면 예외를 발생시킨다.
 5. **Boolean isEmpty()** : 큐가 비어있으면 true, 그렇지 않다면 false를 반환한다.

## 3️⃣ 우선순위 큐를 이용한 "더 맵게" 문제풀이
[프로그래머스_더맵게](https://programmers.co.kr/learn/courses/30/lessons/42626#)
```java
public class Programmers_더맵게 {

    public static Queue<Integer> heap = new PriorityQueue<>();

    public static void main(String[] args) {

        int[] scoville = {1, 2, 3, 9, 10, 12};

        System.out.println(solution(scoville, 7));
    }

    public static int solution(int[] scoville, int K) {
        int answer = 0;

        for (int i = 0; i < scoville.length; i++) {
            heap.offer(scoville[i]);
        }

        return answer = calcScoville(heap, K);
    }

    public static int calcScoville(Queue<Integer> heap, int K) {
        int count = 0;
        int scoville = 0;
        while (heap.peek() <= K) {
            if(heap.size() <= 1) return -1;
            int first = heap.poll();
            int second = heap.poll();
            scoville = first+ (second * 2);
            count++;
            heap.offer(scoville);
        }
        return count;
    }
}
```

> 출력결과 : 2