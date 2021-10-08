---
layout: post
title:  "[RxKotlin] flatmap과 map, MVVM 패턴에서의 적용 과정"
categories: [RxKotlin]
---

## 0️⃣ 프롤로그
 * 인턴십을 진행하면서 정말 다양한 rxKotlin 코드를 접해 보는 것 같다. 하지만 항상 매일 반복해서 보는 코드임에도 도저히 이해가 가지 않는 부분들이 너무 많았다.
그 중에서도 특히 map과 flatmap의 적절성, __Single Steam__ 의 연속적인 분기 등을 계속해서 보고 또 보다 보니 많이 부족하지만 걸음마를 시작하게 되었다..

## 1️⃣ map은 어떨때 사용해야하지?
map은 다음과 같이 구성되어 있다.
![map](/img/10-06-rxkotlin-map-flatmap/map.png){: width="600" height="300"}
마블 다이어그램에 보이는 것과 같이 이 map이라는 연산자는 받은 요소를 다른 형태로 변환시켜 그 형태 그대로를 내보내 준다.
예를 들어서 10이라는 정수를 map을 거쳐 ``` "This is $number." ``` 이러한 형태로 감싸서 변형시켜 줄 수 있다.
다음처럼 간단한 예를 들어보면
```kotlin
fun main(args: Array<String>){
    val observable = listOf(10,9,8,7,6,5,4,3,2,1).toObservable()
    observable.map {
        number -> "This is $number"
    }.subscribe {
        item -> println("Recieved $item")
    }
}
```
---
***출력결과***<br/>
Recieved this is 10<br/>
Recieved this is 9<br/>
Recieved this is 8<br/>
Recieved this is 7<br/>
Recieved this is 6<br/>
Recieved this is 5<br/>
Recieved this is 4<br/>
Recieved this is 3<br/>
Recieved this is 2<br/>
Recieved this is 1<br/>

---
 * 위와 같은 출력 결과를 가질 것이다. 각각의 정수형 요소들이 문자열로 형변환이 진행되었고, __map__ 연산자 안에서 람다를 이용하여 알맞게 가공 된 후 출력이 된다.

## 2️⃣ flatmap은 어떨때 사용해야하지?
__flatmap__ 과 __map__ 의 가장 큰 차이점이라고 하면 __flatMap은 각각의 요소들을 Observable 형태로 변환을 시켜준다.__ 여기서 __Observable__로 변환을 시켜 준다는 소리는 map이 stream에 들어갔던 아이템 그 자체 __1대 1__ 의 개념이라고 생각 한다면 __flatMap은 1대 1 또는 1대 다수__ 라는 형태에 맞게 된다.
![flatmap](/img/10-06-rxkotlin-map-flatmap/flatmap.png){: width="600" height="300"}
 * 마블 다이어그램을 보면 map처럼 Observable에서 방출된 각각의 아이템들을 변환시키기 위한 목적은 같다.
 * map같은 경우는 각각의 아이템 자체를 변환시켜 방출한다면 flatmap은 여러개로 나누어 방출시키고, 각각의 방출된 아이템들을 Observable 형태로 방출 시킨다.

```kotlin
    flatMap{...}.flatmap{...}
```
 * 이러한 형태로 여러 스트림을 이어주는 동작에 유용하다고 볼 수 있다. __flatMap__ 이 위와 같이 여러개로 이어져 있다는 것은 기본적으로 여러개의 __Stream__ 을 다루겠다는 것이고, 하나의 __Stream__ 이 끝난 이후에 그 결과에 따라 다른 스트림을 처리해 준다는 의미이다. 
```kotlin
_surveyCheckProcessor.firstOrError()
// _surveyCheckProcessor는 Boolean 형식을 배출하는 BehaviorProcessor이다.
  .flatMap { isCheck ->
      if (isCheck) {
          surveyRepository.deleteSurvey()
      } else {
          Single.just(true)
      }
  }
  .flatMap { isDeleted ->
      if (!isDeleted) {
          Single.never()
      } else {
          surveyRepository.fetchSurvey()
      }
  }
```
 * ___surveyCheckProcessor__ 에서 방출되는 결과 값들을 여러번의 분기로 처리하는 간단한 예시이다. ___surveyCheckProcessor__ 는 __ture__ or __false__ 값을 내보낼 것이고, 이 결과에 따라서 첫 번째 조건문에서 기존에 설문 조사 결과가 있는지 없는지를 판별하고, 해당하는 로직을 수행 할 것이다. 이 예시에서는 어찌 되었든 새로운 설문을 시작하면 항상 사용자의 마지막 설문 결과를 지우고 다시 설문 문항들에 해당하는 __List__ 를 __fetch__ 해 와야 하기 때문에 설문 기록이 있다는 true 값을 받았을 때는 기존 설문 결과를 지워주고, 그 true값으로 두 번째 __flatMap__ 에서 __else__ 문으로 들어가 설문 문항을 __fetch__ 해 온다. 다음으로 ___surveyCheckProcessor__ 에서 __false__ 를 받았을 경우에는 __just__ 연산자로 ture 값을 배출 시키고, 두 번째 __isDeleted__ 가 수행될때는 항상 true값을 가지고 조건문으로 들어가게 되므로 설문 문항을 __fetch__ 해 온다.

 * 또 주의해야 할 점은, __flatMap은 순서를 보장하지 않는다는 점이다.__ 좋은 예시가 하나 있다. 넥슨의 게임인 피파온라인4의 API를 이용하여 경기 전적 검색을 해주는 서비스를 개인 프로젝트로 만들던 도중에 이러한 이슈를 마주하게 되었다.
![fifaproject](/img/10-06-rxkotlin-map-flatmap/fifaproject.png){: width="600" height="300"}
 * 이러한 형태로 여러번의 __Single__ 데이터를 받는 로직이 존재했다. 애초에 이 목록들을 __List__ 형태로 제공 해 주지를 않았고, 이렇게 되다 보니 __adapter__ 에 연결하여 목록을 표시 해 줄 때 배열의 0번째에 모든 데이터가 몰려서 오거나, __LiveData__ 를 이용하여 리스트를 만들어서 __adapter__ 에 연결 해 주어도 경기 결과를 불러올 때 마다 순서가 뒤죽박죽이 되어서 RecyclerView에 들어가는 이슈가 발생했었다.
 <br>
<img align="left" src="/img/10-06-rxkotlin-map-flatmap/fifamatch01.png"/> <img align="left" src="/img/10-06-rxkotlin-map-flatmap/fifamatch02.png"/>
 <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br> <br>
 * ### cancatMap
![concatmap](/img/10-06-rxkotlin-map-flatmap/concatmap.png){: width="600" height="300"}

    * 위와 같이 flatmap의 순서를 보장하지 않는다는 점을 보완하는 연산자가 바로 __concatMap__ 이다.
보이는 마블 다이어그램과 같이 하나의 __stream__ 이 끝날 때 까지 다음 __stream__ 은 대기를 하고, 순서를 보장하며 순서대로 방출이 되는 것을 확인할 수 있다.