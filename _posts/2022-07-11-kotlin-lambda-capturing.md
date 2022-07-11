---
layout: post
title: "[Kotlin] 람다 캡쳐링(Lambda Capturing)📷"
date: 2022-07-11 20:38:01 +0700
categories: [kotlin]
---

## 0️⃣ 프롤로그
 * 이펙티브 코틀린을 읽던 중에 람다 캡쳐링이라는 현상을 알게되었다. 람다식 내부에서 식 외부에 존재하는 지역변수를 사용할 때 발생하는 문제점을 이야기하며 소개되었다. 회사에서 동료들과 이야기 하면서도 다시 생각하게 되는 개념이라 정리해본다.
   (Kotlin 카테고리지만 근본이되는 자바의 개념으로 정리)
## 1️⃣ 람다 캡쳐링
 * 람다식은 기본적으로 **(파라미터) -> 바디**의 구조를 가지고있고, 바디에서는 파라미터로 넘겨진 변수를 활용하여 작업을 수행한다. 이 때 람다 캡쳐링이라는 개념이 등장하는데 파라미터로 넘겨받은 데이터가 아닌 람다식 외부에서 정의된 변수를 참조하는 데이터나 변수를 람다식 내부에 저장하고 사용하는 동작을 의미한다. 코틀린의 람다를 살펴보기 전에 자바의 람다 형식부터 살펴보았다.
```java
void lambda() { 
    int captureValue = 20; 
    Runnable r = () -> System.out.println(captureValue); 
}
```
`captureValue`라는 이름의 지역변수를 람다에 캡쳐하고 해당 람다식을 `r`이라는 변수에 할당하는 코드이다.
위 코드에서 지역변수 `captureValue`와 람다식 내부에서 사용되는 자유변수인 `captureValue`는 별개의 데이터라는 점이다.

## 2️⃣ 람다 캡쳐링의 제약조건
 * 람다식 내부에서 클래스의 static 필드나 인스턴스 등을 참조하는데에는 아무런 문제가 없다. 그치만 일반 지역변수를 람다식 내부에서 재할당하는 동작을 구현한다면 다음과 같은 제약사항에 걸리게 된다.
   - `local variables referenced from a lambda expression must be final or effectively final`   
   -> `람다식 내부에서는 값이 단 한 번만 할당되는(final) 지역변수만 캡쳐할 수 있다`
```java
void lambdaCapturing_localVarMustBeFinal() { 
    int captureValue = 20; 
    Runnable r = () -> System.out.println(captureValue);
    captureValue = 33; // 컴파일 에러 발생
}
```
위 처럼 람다에서 사용된 지역변수를 재할당 시도해도 컴파일 에러를 발생시킨다.
```java
void lambdaCapturing_localVarMustBeFinal() { 
    int captureValue = 20; 
    Runnable r = () -> captureValue = 33; // 컴파일 에러 발생
}
```
또한 이렇게 람다 내부에서 외부 지역변수를 재할당해줘도 동일하게 컴파일 에러가 발생한다.

## 3️⃣ 자유변수와 JVM 메모리 구조
 * `자유변수(free variable)`란 이처럼 람다식 외부에서 정의된 지역변수를 람다식 내부에서 참조하는 변수를 의미한다. 그럼 자유변수를 사용하는것이 왜 문제가 될까..? JVM의 메모리 구조에 답이있다.
   
![image](https://user-images.githubusercontent.com/27722059/178284883-bb8e305a-3dc8-4d20-b754-a409d7fe6c49.png)
JVM의 구조를 보면 `RunTime Data Area`가 JVM의 메모리 영역으로 자바 애플리케이션을 실행할 때 사용되는 데이터들을 적재하는 영역이다.
 1. **Method area (메소드 영역)**
    * 클래스 멤버 변수의 이름, 데이터 타입, 접근 제어자 정보같은 필드 정보와 메소드의 이름, 리턴 타입, 파라미터, 접근 제어자 정보같은 메소드 정보, Type정보(Interface인지 class인지), Constant Pool(상수 풀 : 문자 상수, 타입, 필드, 객체 참조가 저장됨), static 변수, final class 변수등이 생성되는 영역이다.
 2. **Heap area (힙 영역)**
    * `new` 키워드로 생성된 객체와 배열이 생성되는 영역이다. 메소드 영역에 로드된 클래스만 생성이 가능하고 `Garbage Collector`가 참조되지 않는 메모리를 확인하고 제거하는 영역이다.
 3. **Stack area (스택 영역)**
    * 지역 변수, 파라미터, 리턴 값, 연산에 사용되는 임시 값등이 생성되는 영역이다.
    `int a = 10;` 이라는 소스를 작성했다면 정수값이 할당될 수 있는 메모리공간을 a라고 잡아두고 그 메모리 영역에 값이 10이 들어간다. 
    즉, 스택에 메모리에 이름이 a라고 붙여주고 값이 10인 메모리 공간을 만든다.
    클래스 `Person p = new Person();` 이라는 소스를 작성했다면 `Person p`는 스택 영역에 생성되고 
    new로 생성된 Person 클래스의 인스턴스는 힙 영역에 생성된다.
 4. **PC Register (PC 레지스터)**
    * Thread(쓰레드)가 생성될 때마다 생성되는 영역으로 현재 쓰레드가 실행되는 부분의 주소와 명령을 저장하고 있는 영역이다. 이 영역을 이용해서 쓰레드를 돌아가면서 수행할 수 있게 한다.


여기서 중요한 부분은 Heap영역과 Stack 영역이다.
스레드가 생성되었을 때를 기준으로 메소드 영역과 힙 영역은 모든 쓰레드가 공유하게된다.

    * 지역변수는 스택 영역에 올라가게되고, 어떠한 메서드의 실행이 종료되었을 때 해당 메서드에서 선언되고
    사용되고있던 모든 지역변수의 할당은 해제된다. 하지만 힙 영역에 올라간 static 필드나 인스턴스 클래스는 남아있게 된다.

## 4️⃣ 느낀점!
정리하자면 람다식은 파라미터만이 아니라 람다식 외부의 선언된 변수를 활용할 수 있으며, 이러한 동작을 람다 캡처링이라고 부른다. 이때 컴파일러는 캡처하려는 지역 변수에 대해 불변성을 강제하는데, 이는 딱히 문제라고 보기 어렵다. 불변 데이터가 지니는 다양한 이점도 있겠지만, 외부 변수의 값을 변화시키는 일반적인 절차형/명령형 프로그래밍 패턴을 문법 차원에서 예방한다는 이점도 크다고 볼 수 있다. 다만, 스택 영역에 저장되지 않는 각종 인스턴스 필드와 static 변수의 경우 이러한 제약이 존재하지 않기 때문에 side effect가 발생하지 않도록 주의해야 할 것이다.

