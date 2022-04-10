---
layout: post
title: "[Kotlin] 공변성, 반공변성, 무변성🦎"
date: 2022-04-10 15:22:01 +0700
categories: [kotlin]
---

## 0️⃣ 프롤로그
 - 이펙티브 코틀린을 읽다가 그냥 가볍게 알고 있었던 키워드들에 대한 깊은 학습이 필요하다고 느껴서 여러가지 키워드들을 검색해가며 공부하던 중에 covariance, contravariance, invariance 즉 **변성**에 대한 설명들이 많길래 한 번 정리해보는게 좋을 것 같아서 적어본다.

## 1️⃣ 변성(variance)
 - 프로그래밍 언어에서의 변성이란 제네릭 형식 매개변수가 클래스 계층에 어떤 영향을 미치는지를 말한다.    
 쉽게 예를들어 `List<String>`과 `List<Any>`처럼 기저 타입은 같지만 제네릭 타입의 인자가 다른 여러 타입이 서로 어떤 관계가 있는지 설명하는 개념이라고 생각하면 된다.   
 
   ### 하위 타입에 대한 관계
   String 타입은 Any 타입의 하위 타입이다.    
   그렇다고 해서 `List<String>`은 `List<Any>`의 하위 타입은 아니다. 코틀린은 **무공변성**을 디폴트로 하기 때문에 아래와 같은 코드를 실행한다면 컴파일 오류를 발생 시키게 된다.
   ```kotlin
    fun printList(list: List<Any>) {
        for(i in list) {
            println(i)
        }
    }

    fun main() {
        val lists = listOf<String>("1","2","3")
        printList(lists) // compile error
    }
   ```
 `List<String>`과 `List<Any>`는 아무런 관계가 없기 때문이다.   
 이 두 타입의 관계를 정의해주고, printList() 함수의 활용성을 확장할 수 있도록 해 주는 개념이 바로 **변성**이다.

## 2️⃣ 무변성(invariance)
 - 제네릭 클래스를 인스턴스화 할 때 서로 다른 자료형을 사용하기 위해서는 자료형 사이의 상, 하위 관계를 잘 따져야 한다.`형식 매개변수` 를 선언할 때 `Out` `In` 키워드 없이 그냥 선언한다면 기본 변성인 **무변성**으로 클래스가 선언된다. 아무리 상하관계가 명확한 자료형이라도 클래스가 무변성으로 선언되면 자료형 불일치 오류를 발생시킨다.

 ```kotlin
    class Num<T>(val size: Int)
    fun main() {
        val anys: Num<Any> = Num<Int>(10) // 자료형 불일치 오류
        val nothings: Num<Nothing> = Num<Int>(10) // 자료형 불일치 오류
    }
```

## 3️⃣ 공변성(covariance)과 반공변성(contravariance)
 - `형식 매개변수` 의 상, 하위 관계가 성립하고, 그 관계가 **그대로 인스턴스 자료형으로 이어지는 경우** 이를 `공변성` 이라고 한다.
 ```kotlin
    class Num<Out T>(val size: Int)
    fun main() {
        val anys: Num<Any> = Num<Int>(10) // 상, 하위 관계 자료형 일치
        val nothings: Num<Nothing> = Num<Int>(10) // Nothing은 Int의 하위 타입이므로 불일치 오류
    }
 ```
 `Out` 키워드를 사용해서 공변성으로 클래스를 선언했다. Any의 경우 Int의 상위 클래스이므로 Int형으로 생성한 객체도 Any에 포함할 수 있다.   
 하지만 **Nothing의 경우 Int의 하위 클래스**이므로 Int형으로 생성한 객체를 포함할 수 없다.   

   ### 공변성, 반공변성에서 자료형 제한하기!
   ```kotlin
    open class Animal(val size : Int) {
	  fun feed() = println("Feeding")
    }

    class Cat(val jump : Int) : Animal(50)
    class Dog(val weight : Int) : Animal(80)

    class Box<out T : Animal>(val element : T) {
	    // Out 키워드를 사용해서 공변성으로 선언
        // In 키워드를 사용하면 반공변성으로 선언된다.
	    // 따라서 하위 자료형이 상위 자료형으로 변환하는 것은 괜찮다.
    }

    fun main() {
	
    // 객체 생성
	val c1 : Cat = Cat(10)
	val c2 : Dog = Dog(20)
	
	var a1 : Animal = c1 // 하위 자료형이 상위 자료형으로의 변환은 가능.
	
	val c3 : Box<Cat> = Box<Animal>(10) // 상위 자료형이 하위 자료형으로의 변환은 불가능
	val c4 : Box<Any> = Box<Cat>(!0) // 자료형을 Animal로 제한했으므로 Animal, Cat, Dog를 제외한 다른 자료형은 사용불가.
}
 ```
   
[마지막 수정 : 2022.04.10]