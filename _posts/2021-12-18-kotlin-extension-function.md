---
layout: post
title:  "[Kotlin] 코틀린의 확장함수 (Extension Function)"
date: 2021-12-18 14:11:11 +0700
categories: [kotlin]
---

## 0️⃣ 프롤로그
* 인턴 생활을 하면서 느낀게 내가 원하는 확장 함수를 자유 자재로 만들어서 쓸 수 있어야 개발의 속도나 효율 측면에서 많은 효율이 생길 것이라고 느꼈다. 이 참에 그냥 개념부터 확실하게 정리 해 보고자 한다.

## 1️⃣ 확장함수란?
>Kotlin provides the ability to extend a class with new functionality without having to inherit from the class or use design patterns such as Decorator. This is done via special declarations called extensions.   
For example, you can write new functions for a class from a third-party library that you can't modify. Such functions can be called in the usual way, as if they were methods of the original class. This mechanism is called an extension function. There are also extension properties that let you define new properties for existing classes.

* 라고 코틀린 공식 문서에서 말한다. 말 그대로 코틀린은 클래스에서 상속 받거나, Decorator와 같은 특수한 디자인 패턴 등을 사용 할 필요 없이 새로운 기능으로 클래스를 확장할 수 있는 기능을 제공한다. **Extension**이라는 특별한 선언을 통해서 사용이 가능하다. 이는 수정할 수 없는 타사 라이브러리와 같은 기능들을 새로운 함수로 작성하여 원래 내가 구현하고 있던 클래스의 메소드인 것 처럼 일반적인 방법으로 호출 할 수 있다. 이 메커니즘을 확장함수라고 부른다.

* 확장함수의 형식은 아래와 같다.
```kotlin
fun MutableList<Int>.swap(index1: Int, index2: Int) {
    val tmp = this[index1]
    this[index1] = this[index2]
    this[index2] = tmp
}
```
이렇게 첫 번째 prefix로 어떤 자료형 타입 클래스 또는, 여러가지 타 사 라이브러리 클래스들과 같이 어떤 클래스에서 추가적으로 기능을 구현하고 싶은지를 지정한다.
this 키워드가 나오는 것을 확인할 수 있는데 이는 확장함수 내에서는 prefix 타입과 일치한다. 어떻게 생각해보면 java의 this 키워드와 동일한 역할을 한다고 할 수 있다. (어차이 JVM으로 돌아가니까..?)

* 위와 같이 확장 함수를 만들어 줬다면 다음과 같이 해당 MutableList<Int> 자료형을 사용 할 때 어디서든 swap이라는 함수를 불러와 사용할 수 있다.

```kotlin
val list = mutableListOf(1, 2, 3)
list.swap(0, 2)
```

하지만 MutableList 타입 안에 Int 자료형이 들어가있는 것으로 보아 다른 타입의 MutableList 클래스 에서는 해당 확장함수를 호출하지 못하는 것을 확인할 수 있을 것이다. 이런 점을 해결하기 위해 MutableList을 제네릭 **T**타입으로 선언 해 주는 것으로 해결이 가능하다.

```kotlin
fun <T> MutableList<T>.swap(index1: Int, index2: Int) {
    val tmp = this[index1]
    this[index1] = this[index2]
    this[index2] = tmp
}
```

여기서도 똑같이 this 키워드는 prefix로 지정된 타입을 말하는 것을 알 수 있고 제네릭 함수 타입으로 똑같이 확장함수를 만들어 준 것이다.
