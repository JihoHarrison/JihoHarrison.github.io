---
layout: post
title:  "[RxKotlin] 🌊 Flowable과 Observable 그리고 BackPressureStrategy"
categories: [RxKotlin]
---

## 프롤로그
사내 서비스 유지 보수를 해 가며 `RxKotlin`을 집중적으로 학습하다보니 문득 `BehaviorProcessor`와 `BehaviorSubject`의 차이가 궁급해졌고, 이를 탐구하며 들어와보니 `Flowable`과 `Observable`의 차이를 알아야 했다. 열심히 구글링을 해 가며 알게 된 것이 바로 `BackPressure` 즉, 배압의 내장 유무였다.

## 배압 (BackPressure)
### ReactiveX에서 배압이 발생 할 조건 ###
데이터를 발행하는 속도와 구독하는 자가 처리하는 속도의 차이가 있을 때 발생한다.

이 말은 `Observer`가 수신 할 수 없을 만큼의 엄청난 양의 데이터를 `Observable`이 방출 할 때 나타난다는 말이다. 이 경우에 `Out of Memory Error`가 발생하게 된다.
이러한 경우에 대한 예외 처리가 필요한데 이를 `BackPressureStrategy`라고 한다. `Flowable`은 결국 `Observable`의 일종이기 때문에, 이 둘은 이러한 차이점만 뺀다면 거의 동일한 형식이라고 봐도 무방하다. 아래의 사이트에서 다음과 같은 부분을 보면 된다. 
 [링크](https://medium.com/mindorks/rxjava-types-of-observables-404d75605e35)<br><br>
![backpressure](/img/09-15-backpressure/backpressure.png){: width="800" height="600"}

이론적으로 `Flowable`을 사용 할 조건은 대량의 데이터 예를들어, 초당 10000건 이상의 데이터 처리를 해야 할 때, 또는 네트워크 통신이나 DB등의 IO스레드에 접근할 때 라고 말하고, `Observable`을 사용 할 조건은 `Flowable`과 반대되는 상황으로, 소량의 데이터 또는 GUI 이벤트 등을 처리할 때 사용 된다고 한다.

## Flowable의 배압 대응 전략
 - `onBackPressureBuffer()` : 배압 이슈가 발생했을때 별도의 버퍼에 저장한다.<br>
    `Flowable` 클래스는 기본적으로 128개의 버퍼가 있다. 버퍼에 만들고 쌓아두다가 처리한다.

 - `onBackPressureDrop()` : 배압 이슈가 발생했을 때 해당 데이터를 무시한다. 배압 이슈 이후의 데이터는 다 무시한다.

 - `onBackPressureLatest()` : 처리할 수 없어서 쌓이는 데이터를 무시하면서 최신 데이터만 유지한다.

하지만 모든 데이터 스트림에 `Flowable`을 사용하는 것은 결코 바람직하지 않은 방법이 될 것이다. 소량이 데이터나 UI 이벤트 처리와 같은 경우는 `Observable` 에서도 데이터 스트림의 시간차 등을 두어 스트림의 양을 조절하는 `debounce()`라던지, `throttle()`, `sample()` 함수 등이 존재하기 때문에 상황에 따라 적절하게 사용하는 방법을 익히면 될 것 같다.