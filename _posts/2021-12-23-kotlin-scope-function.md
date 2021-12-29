---
layout: post
title:  "[Kotlin] Scope Function"
date: 2021-12-23 15:43:10 +0700
categories: [kotlin]
---

## 0️⃣ 프롤로그
* 코틀린에는 다양한 Scope Function들이 존재한다. 람다 형식으로 사용할 수 있으며 각각의 함수마다 사용 방법 및 특징이 있으니 이참에 한 번 살펴보자.

## 1️⃣ 일반적인 사용 방법
* 일반적으로 객체를 사용하려면 다음과 같은 방법을 사용해야 한다.
```kotlin
data class Person(var name: String, var age: Int)

val person = Person("", 0)
person.name = "James"
person.age = 56
println("$person")

```
>Person(name=James, age=56)

이렇게 data class를 생성하여 하나의 object를 만들고 instance화 한 다음 이를 이용하여 각각 초기화 해 주는 것을 볼 수 있다.
