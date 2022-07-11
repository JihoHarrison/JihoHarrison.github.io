---
layout: post
title:  "[안드로이드] 네비게이션 메뉴에 적용한 BehaviorProcessor, Flowable 🔀"
date: 2021-09-14 16:34:10 +0700
categories: [안드로이드]
---

`RxKotlin`을 계속 공부하다보니 항상 Rx의 메커니즘을 어떻게 안드로이드에 적용 시킬지 고민하며 프로젝트를 진행하는 것 같다.
답은 명확한데 답을 찾아내기까지 정말 많은 시간이 걸렸던 부분 중 하나이다. 바로 `BehaviorProcessor`이다.

## 0️⃣ BehaviorProcessor? BehaviorSubject?
`BehaviorProcessor`를 다루기 전에, `BehaviorSubject`라는 형식을 많이 들어 보았을 것이다.
이 둘의 차이점은 매우 단순했다.. 반환하는 형식이 `Flowable`인지와 `Observable`인지의 차이다.
간략하게 설명하자면 `BackPressure`를 default로 가지고 있는지와 없는지로 구분되는데 `Flowable`이 `BackPressure`를 기본적으로 가지고 있는 녀석이고 `Observable`은 다른 방식으로 구현을 해 주어야 한다. `Flowable`과 `Observable`의 차이는 아래 글에서 확인 하도록 하자.

> [Flowable과 Observable의 차이](https://jihokevin.github.io//articles/2021-09/flowable-observable)

이제 `BehaviorProcessor`가 `Flowable`을 반환 해 준다는 것을 알았다. 그럼 이렇게 반환되는 흐름을 `subscribe` 함으로써 제어를 할 수 있을 것이다.
하지만 이 `flow`에 값을 넘겨주고 꺼내 쓸 수 있어야 하는데 어떤식으로 값을 `flow`에 넣어주고 넣어준 값에 따라 흐름을 제어할 수 있는지 알아보자.
이를 알기 위해서는 `BehaviorProcessor` 클래스의 내부를 들여다 봐야 한다.

`BehaviorProcessor` 타입으로 만들어진 변수명에 .을 찍어보면 `value`라는 형식을 찾을 수 있다.
![value](/img/09-14-android/behavior-processor-value-01.png)
이 `value`라는 타입은 `BehaviorProcessor` 클래스 내부에서 다음과 같은 전역변수로 선언되어있다.
![value](/img/09-14-android/behavior-processor-value-02.png)
이렇게 선언되어있는 `value`는 또 클래스 내부에서 `setCurrent`라는 메서드가 호출될 때 다음과 같이 `lazy`하게 초기화되고,
![value](/img/09-14-android/behavior-processor-value-03.png)
이 `setCurrent` 메서드는 offer가 호출될 때 불려지게 된다.
![value](/img/09-14-android/behavior-processor-value-04.png)
`offer`라고..? 맞다. `Flowable`에서 값을 배출시키는 바로 그 익숙한 연산자이다. 결국 결론적으로는, `value`라는 변수는 타고 타고 타서 우리가 `Flowable`의 데이터들을 배출하기 시작할 때 그 값이 `Parameter`로 전달되는 것이다..!

다음과 같이 `subscribable`이라는 flow를 외부에서 접근할 수 있게 해 주는 선언이 있다.
```kotlin
private val behaviorProcessor: BehaviorProcessor<Animal> =
    BehaviorProcessor.createDefault(Animal.CAT)
    // createDefault는 말 그대로 초기 값 세팅.. null 값이 올 수 없기 때문..
val subscribable: Flowable<Animal> = behaviorProcessor.distinctUntilChanged()
```
이 변수를 가진 클래스 내에서는 `behaviorProcessor`라는 변수로 접근 할 것이고, 이 `Flowable`은 외부에서 `subscribable`이라는 변수명으로 접근을 하여 `Flowable`을 제어 할 것이다. 결국 `Flowable` 형태로 `BehaviorProcessor`를 받는 변수 `subscribable`을 `offer`하고, 그 `flow`를 구독하는 녀석들에게 제어권이 넘어가게 된다.

## 1️⃣ 안드로이드에서는 어떻게 적용 해 볼까..?
간단한 로직을 구현 해 보았다.

```kotlin
fun navigateToDogFragment() {
    behaviorProcessor.offer(Animal.DOG)
}
```
이 함수는 바로 위 코드 블럭에서 사용했던 `behaviorProcessor`를 사용한다. 보이는 그대로 이 함수가 어딘가로 호출될 때 `CAT 프래그먼트`에서 `DOG 프래그먼트`로 화면의 전환이 일어나게 구현 해 볼 것이다. 다음을 살펴봅시다!
```kotlin
// 뷰 단에서 호출되는 함수들 구현
private fun bindViewModels() {
   viewModel.subscribable
       .observeOnMain()
       .subscribeWithErrorLogger {
           navigate(it)
       }
       .addToDisposables()
}

private fun navigate(step: Animal) {
   val fragment = when (step) {
       Animal.DOG -> DogFragment()
       Animal.LION -> LionFragment()
   }

   val currentFragment = supportFragmentManager.primaryNavigationFragment

   if (currentFragment == null || currentFragment.javaClass != fragment.javaClass) {
       supportFragmentManager.beginTransaction()
           .replace(R.id.dog, fragment)
           // add를 쓰게 되면 덮어진 View가 파괴되지 않아 클릭되는 등 버그를 발생 시키므로 replace 사용
           .setPrimaryNavigationFragment(fragment)
           .addToBackStack(fragment.javaClass.simpleName)
           .commit()
   }
}
```
함수 두 개를 구현 해 놓았다. `navigate()` 함수는 `bindViewModels()` 함수에서 IO 스레드 내에서 구독이 되고 있고, 위에서 구현했던 `navigateToDogFragment()`가 어떠한 뷰나 액티비티 또는 프래그먼트로 호출됐을 시 `offer`로 `Dog`라는 이름의 `Enum` 값이 전달 되면서 값을 방출시킬 것이고 이 방출된 값 즉 DOG라는 이름은 뷰 부분의 `bindViewModels()` 함수 안에 구독처리가 되어 `it`의 값으로 `navigate()` 함수의 인자를 결정짓게 되는 것이다. 그럼 `navigate()` 함수는 이 배출된 DOG라는 값을 받아서 `DogFragment()`를 `fragment` 변수에 넣어 줄 것이고, `supportFragmentManager`를 통하여 `navigation`이 일어나게 된다.

정말 어렵고 복잡하다.. 안드로이드 고수가 되는 그날까지 최선을 다해보자..!