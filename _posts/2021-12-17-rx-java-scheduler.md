---
layout: post
title:  "[RxJava] RxKotlin 말고 RxJava! 💡"
date: 2021-12-17 18:03:10 +0700
categories: [RxKotlin]
---

## 0️⃣ 프롤로그
* RxKotlin을 학습하다보니 RxJava에 대한 형식도 적용 시켜보고 싶었다.. 그래서... 공부했다... 밤새.. 너무 힘들다.. 하하.... 헛소리 그만하고 RxJava를 정리 해 보겠습니다.. RxKotlin과 비교했을 때 정말 다른 점이 없어보이지만 그래도 기초부터 다시 잡는다는 마음으로! 특히 스레드 지정에 관한 Scheduler에 집중하여 학습했습니다. 후반부에 OpenWeather API를 사용한 부분이 보일텐데, 블로그 메인 화면에 WearWeather 프로젝트에 사용된 API 이다.

### 1️⃣ 스케줄러란?

: 주로 비동기 프로그래밍에서 사용, RxJava코드를 어느 스레드에서 실행할지 지정할 수 있다. 

> **subscribeOn()**
> 
: 구독자가 Observable에 subscribe()함수를 호출하여 **구독할 때 실행되는 스레드를 지정** 

> **observeOn()**
> 
: **이후 추가적인 연산에 대한 작업 스레드를 설정** 

```java
public class JavaExample {
    public static void main(String[] args) {
        String[] objs = {"1-S", "2-T", "3-P"};
        Observable<String> source = Observable.fromArray(objs)
                .doOnNext(data -> Log.v("Original data = "+data))
                .subscribeOn(Schedulers.newThread())
                .observeOn(Schedulers.newThread())
                .map(Shape::flip);
        source.subscribe(Log::i);
        CommonUtils.sleep(500);
    }
}
```

```
RxNewThreadScheduler-1 | Original data = 1-S
RxNewThreadScheduler-1 | Original data = 2-T
RxNewThreadScheduler-1 | Original data = 3-P
RxNewThreadScheduler-2 | value = (flipped)1-S
RxNewThreadScheduler-2 | value = (flipped)2-T
RxNewThreadScheduler-2 | value = (flipped)3-P
```

subscribeOn()과 observeOn()둘다 호출해줘서 **데이터 흐름이 발생하는 스레드와 처리된 결과를 구독자에게 발행하는 스레드를 분리했**다. (subscribeOn(Schedulers.newThread()를 해줬기때문에 main스레드가 아니라 새로운 스레드에서 돌아가는것도 볼 수 있다

observeOn을 주석처리해보자.

```java
public class JavaExample {
    public static void main(String[] args) {
        String[] objs = {"1-S", "2-T", "3-P"};
        Observable<String> source = Observable.fromArray(objs)
                .doOnNext(data -> Log.v("Original data = "+data))
                .subscribeOn(Schedulers.newThread())
//                .observeOn(Schedulers.newThread())
                .map(Shape::flip);
        source.subscribe(Log::i);
        CommonUtils.sleep(500);
    }
}
```

```
RxNewThreadScheduler-1 | Original data = 1-S
RxNewThreadScheduler-1 | value = (flipped)1-S
RxNewThreadScheduler-1 | Original data = 2-T
RxNewThreadScheduler-1 | value = (flipped)2-T
RxNewThreadScheduler-1 | Original data = 3-P
RxNewThreadScheduler-1 | value = (flipped)3-P
```

subscribeOn()만 호출했기때문에 새로 만들어진 하나의 스레드에서 모든 작업이 이루어진다.

그러면 이번엔 subscribeOn()을 주석처리해보자.

```java
public class JavaExample {
    public static void main(String[] args) {
        String[] objs = {"1-S", "2-T", "3-P"};
        Observable<String> source = Observable.fromArray(objs)
                .doOnNext(data -> Log.v("Original data = "+data))
//                .subscribeOn(Schedulers.newThread())
                .observeOn(Schedulers.newThread())
                .map(Shape::flip);
        source.subscribe(Log::i);
        CommonUtils.sleep(500);
    }
}
```

```
main | Original data = 1-S
main | Original data = 2-T
main | Original data = 3-P
RxNewThreadScheduler-1 | value = (flipped)1-S
RxNewThreadScheduler-1 | value = (flipped)2-T
RxNewThreadScheduler-1 | value = (flipped)3-P
```

observeOn()은 호출 이후의 작업을 ()안의 스레드에서 실행하기때문에, 이전에서는 main스레드로 작업이 이루어지고(subscribeOn(Schedulers.newThread()를 하지 않았으니까! 원래 main스케줄러에서 동작), 이후로는 observeOn(Schedulers.newThread())로 만들어준 새로운 스레드에서 작업이 이루어지는걸 볼 수 있다.

그리고 새로운 스레드에서 작업을 하기 때문에 새로운 스레드 작업이 전부 끝날때까지  CommonUtils.sleep()으로 메인 스레드를 기다리게 해야한다. 

> 정리
> 
- **subscribeOn() 함수와 observeOn()함수를 모두 지정**하면 Observable에서 **데이터 흐름이 발생하는 스레드와 처리된 결과를 구독자에게 발행하는 스레드를 분리**할 수 있다.
- subscribeOn()만 호출하면 Observable의 모든 흐름이 동일한 스레드에서 실행된다.
- 스케줄러를 별도로 지정하지 않으면 현재(main)스레드에서 동작을 실행한다.
### 스케줄러의 종류

| 스케줄러 | RxJava 2.x | RxJava 1.x |
| --- | --- | --- |
| 뉴 스레드 스케줄러 | newThread() | newThread() |
| 싱글 스레드 스케줄러 | single() | 지원 안 함 |
| 계산 스케줄러 | computation() | computation() |
| IO 스케줄러 | io() | io() |
| 트램펄린 스케줄러 | trampoline() | trampoline() |
| 메인 스레드 스케줄러 | 지원 안 함 | immediate() |
| 테스트 스케줄러 | 지원 안 함 | test() |

### 뉴 스레드 스케줄러

: 새로운 스레드를 만들어 어떤 동작을 실행하고 싶을 때 Schedulers.newThread()를 인자로 넣어주면, 요청을 받을 때마다 새로운 스래드를 생성한다.

```java
public class JavaExample {
    public static void main(String[] args) {
        String[] orgs = {"1", "3", "5"};
        Observable.fromArray(orgs)
                .doOnNext(data -> Log.v("Original data: "+ data))
                .map(data -> "<<" + data + ">>")
                .subscribeOn(Schedulers.newThread()) //첫 번째 new Thread 
                .subscribe(Log::i);
~~~~        CommonUtils.sleep(500);
        Observable.fromArray(orgs)
                .doOnNext(data -> Log.v("Original data: "+ data))
                .map(data -> "##" + data + "##")
                .subscribeOn(Schedulers.newThread())//두 번째 new Thread
                .subscribe(Log::i);
        CommonUtils.sleep(500);
    }
}
```

```
RxNewThreadScheduler-1 | Original data: 1
RxNewThreadScheduler-1 | value = <<1>>
RxNewThreadScheduler-1 | Original data: 3
RxNewThreadScheduler-1 | value = <<3>>
RxNewThreadScheduler-1 | Original data: 5
RxNewThreadScheduler-1 | value = <<5>>
RxNewThreadScheduler-2 | Original data: 1
RxNewThreadScheduler-2 | value = ##1##
RxNewThreadScheduler-2 | Original data: 3
RxNewThreadScheduler-2 | value = ##3##
RxNewThreadScheduler-2 | Original data: 5
RxNewThreadScheduler-2 | value = ##5##
```

아까 subscribeOn()만 호출하면 Observable의 모든 흐름이 동일한 스레드에서 실행된다고 했다. 그래서 첫번째 Observable에서 subscribeOn(Schedulers.newThread())를 호출했기 때문에, 모두 새로운 스레드인 RxNewThreadScheduler-1에서 실행됐고, 두 번째 Observable에서 subscribeOn(Schedulers.newThread())을 또 호출했기 때문에 모두 새로운 스레드인 RxNewThreadScheduler-2에서 실행됐다.  

### 계산스케줄러

: CPU에 대응하는 계산용 스케줄러, '계산' 작업을 할 때는 대기 시간 없이 빠르게 결과를 도출하는 것이 중요하다. → 입출력(I/O) 작업을 하지 않는 스케줄러

**내부적으로 스레드 풀을 생성하며 스레드 개수는 기본적으로 프로세서 개수와 동일하다.**

```java
public class JavaExample {
    public static void main(String[] args) {
        String[] orgs = {"1", "3", "5"};
        Observable<String> source = Observable.fromArray(orgs)
                .zipWith(Observable.interval(100L, TimeUnit.MILLISECONDS), (a, b) -> a); //시간과 합성, 1 3 5가 100ms마다 발행 
				//첫 번째 구독
        source.map(item -> "<<" + item + ">>")
                //.subscribeOn(Schedulers.computation())
                .subscribe(Log::i);
				//두 번째 구독
        source.map(item -> "##" + item + "##")
                //.subscribeOn(Schedulers.computation())
                .subscribe(Log::i);
        CommonUtils.sleep(1000);
    }
}
```

```
RxComputationThreadPool-1 | value = <<1>>
RxComputationThreadPool-2 | value = ##1##
RxComputationThreadPool-1 | value = <<3>>
RxComputationThreadPool-2 | value = ##3##
RxComputationThreadPool-1 | value = <<5>>
RxComputationThreadPool-2 | value = ##5##
```

(a, b) → a 로 인해 시간은 그대로 두고 1 3 5가 100ms마다 발행되며 subscribeOn(Schedulers.computation())으로 **각각 새로운 스케줄러를 만들어 동시에 출력**한다.  

(여기서는 subscribeOn(Schedulers.computation())으로 계산스케줄러를 명시해줬지만 interval()함수가 기본적으로 계산 스케줄러를 사용하기 때문에 제거하고 실행해도 똑같은 결과가 나온다.) 

여러번 실행하다보면 <<>>와 #### 데이터가 뒤섞일때가 있는데, 이는 첫 번째 구독과 두 번째 구독이 거의 동시에 이루어지기 때문에 RxJava 내부에서 동일한 스레드에 작업을 할당했기 때문이라고 한다.

### IO 스케줄러

: 계산 스케줄러와는 다르게 네트워크상의 요청을 처리하거나 각종 입출력 작업을 실행하기 위한 스케줄러로 기본으로 생성되는 스레드 개수가 다르다.

즉, **계산 스케줄러는 CPU개수만큼 스레드를 생성**하지만, **IO스케줄러는 필요할 때마다 스레드를 계속 생성**한다.

(입출력 작업은 비동기로 실행되지만 결과를 얻기까지 대기 시간이 길다.) 

- `계산 스케줄러` : 일반적인 계산 작업
- `IO 스케줄러`: 네트워크상의 요청, 파일 입출력, DB 쿼리 등

```java
public class JavaExample {
    public static void main(String[] args) {
        String root = "/Users/jihoKevin/Desktop/navigation";
        File[] files = new File(root).listFiles();
        Observable<String> source = Observable.fromArray(files)
                .filter(f -> !f.isDirectory())
                .map(f -> f.getAbsolutePath())
                .subscribeOn(Schedulers.io());
        source.subscribe(Log::i);
        CommonUtils.sleep(500);
    }
}
```

```
RxCachedThreadScheduler-1 | value = /Users/ijieun/Desktop/navigation/expand_more_24px.svg
```

Desktop/navigation폴더에 있는 파일 리스트들을 가져와 잘 출력해준다.

### 트램펄린 스케줄러

: 새로운 스레드를 생성하지 않고 **현재 스레드에 무한한 크기의 대기 행렬을 생성하는 스케줄러**

```java
public class JavaExample {
    public static void main(String[] args) {
        String[] orgs = {"1", "3", "5"};
        Observable<String> source = Observable.fromArray(orgs);
        source.map(item -> "<<" + item + ">>")
                .subscribeOn(Schedulers.trampoline())
                .subscribe(Log::i);
        source.map(item -> "##" + item + "##")
                .subscribeOn(Schedulers.trampoline())
                .subscribe(Log::i);
        CommonUtils.sleep(500);
    }
}
```

```
main | value = <<1>>
main | value = <<3>>
main | value = <<5>>
main | value = ##1##
main | value = ##3##
main | value = ##5##
```

처음에 지정한 스레드로 구독자에게 데이터를 발행한다.

새로운 스레드를 생성하지 않고 **main 스레드에서 모든 작업을 실행**한다. 큐에 작업을 넣은 후 **1개씩 꺼내서 동작하므로 첫 번째 구독과 두 번째 구독 순서대로 값이 출력**된다.

### 싱글 스레드 스케줄러

: RxJava 내부에서 단일 스레드를 별도로 생성하여 구독 작업을 처리한다. (생성된 스레드는 여러 번 구독 요청이 와도 공통으로 사용한다.)

```java
public class JavaExample {
    public static void main(String[] args) {
        Observable<Integer> numbers = Observable.range(100, 5);
        Observable<String> chars = Observable.range(0,5)
                .map(CommonUtils::numberToAlphabet);
        numbers.subscribeOn(Schedulers.single())
                .subscribe(Log::i);
        chars.subscribeOn(Schedulers.single())
                .subscribe(Log::i);
        CommonUtils.sleep(500);
    }
}
```

```
RxSingleScheduler-1 | value = 100
RxSingleScheduler-1 | value = 101
RxSingleScheduler-1 | value = 102
RxSingleScheduler-1 | value = 103
RxSingleScheduler-1 | value = 104
RxSingleScheduler-1 | value = A
RxSingleScheduler-1 | value = B
RxSingleScheduler-1 | value = C
RxSingleScheduler-1 | value = D
RxSingleScheduler-1 | value = E
```

- numberToAlphabet()

    ```java
    public static final String ALPHABET = "ABCDEFGHIJKLMNOPQRSTUVWXYZ";
    
        public static String numberToAlphabet(long x){
            return Character.toString(ALPHABET.charAt((int) x % ALPHABET.length()));
        }
    ```


싱글 스레드 스케줄러에서 실행하면 비록 여러 개 Observable이 있어도 별도로 마련해놓은 **단일 스레드에서 차례로 실행하므로, RxSingleScheduler-1에서 100~104, 0~4가 차례대로 map 되어 나온다**.

### Executor 변환 스케줄러

Executor를 변환하여 스케줄러를 생성할 수 있다. 하지만 Executor 클래스와 스케줄러의 동작 방식과 다르므로 추천방식이 아니라고 하니, 그냥 이런 방법도 있구나~ 참고만 하면 좋을거같다. 

    동시에 다수의 요청을 처리해야할 때, 여러개의 스레드를 계속해서 생성할 시 

    - 쓰레드 생성 및 종료에 따른 오버헤드 발생
    - 생성되는 스레드의 개수에 제한을 해주지 않으면 OutOfMemoryError발생 가능
    - 많은 수의 스레드 실행 → 스레드 스케줄링에 대해 오버헤드 발생

    등 여러가지의 문제점이 발생한다. 

    이를 방지하기 위해 동시에 실행될 수 있는 쓰레드의 개수를 제한해야하는데, 이를 위해 Executor인터페이스라는게 생겨났고 쓰레드 풀, 큐 등 다양한 Executor 구현체를 제공하고 있다고 한다.




```java
public class JavaExample {
    public static void main(String[] args) {
        final int THREAD_NUM = 10;
        String[] data = {"1", "3", "5"};
        Observable<String> source = Observable.fromArray(data);
        Executor executor = Executors.newFixedThreadPool(THREAD_NUM);
        source.subscribeOn(Schedulers.from(executor))
                .subscribe(Log::i);
        source.subscribeOn(Schedulers.from(executor))
                .subscribe(Log::i);
        CommonUtils.sleep(500);
    }
}
```

```
pool-1-thread-1 | value = 1
pool-1-thread-2 | value = 1
pool-1-thread-1 | value = 3
pool-1-thread-2 | value = 3
pool-1-thread-1 | value = 5
pool-1-thread-2 | value = 5
```

THREAD_NUM을 10개로 지정해서 10개의 고정 스레드 풀을 생성한다. 첫 번째 Observable과 두 번째 Observable에서 subscribeOn()을 호출하여 Executor 변환 스케줄러를 지정했기 때문에 각각 다른 스레드 풀에서 동작한다. 

방금예제에서는 Executors.newFixedThreadPool(스래드갯수)를 사용해서 각각 다른 스레드에서 동작했지만, Executors.newSingleThreadExecutor()로 실행하면 스레드 하나로 동작한다.

```java
public class JavaExample {
    public static void main(String[] args) {
        final int THREAD_NUM = 10;
        String[] data = {"1", "3", "5"};
        Observable<String> source = Observable.fromArray(data);
        Executor executor = Executors.newFixedThreadPool(THREAD_NUM);
        source.subscribeOn(Schedulers.from(executor))
                .subscribe(Log::i);
        source.subscribeOn(Schedulers.from(executor))
                .subscribe(Log::i);
        CommonUtils.sleep(500);
    }
}
```

```
pool-1-thread-1 | value = 1
pool-1-thread-1 | value = 3
pool-1-thread-1 | value = 5
pool-1-thread-1 | value = 1
pool-1-thread-1 | value = 3
pool-1-thread-1 | value = 5
```

### 스케줄러를 활용하여 콜백 지옥 벗어나기

- 콜백지옥이란?

    우리가 비동기 작업을 하다보면 콜백 함수를 사용해서 서버통신을 할때가 있는데, 콜백 함수를 사용한다면 리턴된 결과에 따라 계속 들여쓰기를 하며 타고타고 들어가는 코드를 작성하게 된다. 이는 콜백 지옥이라고 불릴만큼 가독성도 매우 떨어지고 좋지않은 방법이다. 😈  하지만 Rxjava의 체이닝을 사용하면 콜백지옥으로부터 벗어날 수 있다!


```java
public class JavaExample {
    public static final String GITHUB_ROOT = "Some Base Url";
    private static final String FIRST_URL = "https://api.github.com/zen";
    public static final String SECOND_URL = GITHUB_ROOT + "/samples/callback_hell";
    private final OkHttpClient client = new OkHttpClient();
    public void run() {
        Request request = new Request.Builder()
                .url(FIRST_URL)
                .build();
        client.newCall(request).enqueue(new Callback() {
            @Override
            public void onFailure(Call call, IOException e) {
                e.printStackTrace();
            }
            @Override
            public void onResponse(Call call, Response response) throws IOException { //성공했을때만 
                Log.i("1번째"+response.body().string());
                Request request = new Request.Builder()
                        .url(SECOND_URL)
                        .build();
                client.newCall(request).enqueue(new Callback() { //또 다시 콜백 추가 
                    @Override
                    public void onFailure(Call call, IOException e) {
                        e.printStackTrace();
                    }
                    @Override
                    public void onResponse(Call call, Response response) throws IOException {
                        Log.i("2번째"+ response.body().string());
                    }
                }); 
            }
        });
    }
    public static void main(String[] args) {
        JavaExample example = new JavaExample();
        example.run();
    }
}
```

```
OkHttp https://api.github.com/... | value = 1번째Speak like a human.
OkHttp https://raw.githubuserc... | value = 2번째Welcome to Callback Hell!!
```

맨 처음 서버통신을 했을때 사용했던 방법이였다. 첫 번째 호출의 성공과 실패를 고려하고, 다시 그것을 기준으로 두 번째 호출의 성공과 실패를 고려하여 코드를 작성했다. 딱 봐도 한눈에 보기 어렵고 정상적인 로직과 예외 처리가 혼재되어있는걸 볼 수 있다. (프로젝트 규모가 커지고 API를 호출할때마다 콜백을 사용한다면..? ㅎㅎ) 

```java
public class JavaExample {
    public static final String GITHUB_ROOT = "Some Base Url";
    private static final String FIRST_URL = "https://api.github.com/zen";
    public static final String SECOND_URL = GITHUB_ROOT + "/samples/callback_hell";
    public static void main(String[] args) {
        CommonUtils.exampleStart();
        Observable<String> source = Observable.just(FIRST_URL)
                .subscribeOn(Schedulers.io())
                .map(OkHttpHelper::get)
                .concatWith(Observable.just(SECOND_URL)
                        .map(OkHttpHelper::get));
        source.subscribe(Log::it);
        CommonUtils.sleep(5000);
        CommonUtils.exampleComplete();
    }
}
```

```
RxCachedThreadScheduler-1 | 1048 | value = Mind your words, they are important.
RxCachedThreadScheduler-1 | 1707 | value = Welcome to Callback Hell!!
```

Rxjava를 사용하지 않은 위의 예제와는 눈에 띄게 간결해지고 명확해졌다. 

subscribeOn(Schedulers.io())를 호출해서 io스케줄러에서 작업을 실행하고, 위의 예제에서는 OkHttpClient의 enqueue()메서드를 호출해서 콜백을 전달받았지만, 이 예제에서는 OkhttpHelper.get()을 통해 execute()를 호출한다. (concat()은 첫 번째 Observable과 두 번째 Observable을 함께 인자로 넣어야하지만 concatWith()은 현재의 Observable에 새로운 Observable을 결합할 수 있다는 차이가 있다.) 첫 번째 호출이 끝나면, 두 번째 호출이 이뤄지게 되고 아까와 똑같은 결과가 나타난다. 

하지만 concatWith()은 첫 번째 Observable이 끝날때까지 기다려야하기때문에 zip()을 사용해서 첫 번째 URL과 두 번째 URL요청을 동시에 수행하고 결과만 결합하는게 더 효율적이다.

```java
public class JavaExample {
    public static final String GITHUB_ROOT = "Some Base Url";
    private static final String FIRST_URL = "https://api.github.com/zen";
    public static final String SECOND_URL = GITHUB_ROOT + "/samples/callback_hell";
    public static void main(String[] args) {
        CommonUtils.exampleStart();
        Observable<String> first = Observable.just(FIRST_URL)
                .subscribeOn(Schedulers.io())
                .map(OkHttpHelper::get);
        Observable<String> second = Observable.just(SECOND_URL)
                .subscribeOn(Schedulers.io())
                .map(OkHttpHelper::get);
        Observable.zip(first, second, (a, b) -> ("\n>> "+ a+ "\n>> " + b)).subscribe(Log::it);
        CommonUtils.sleep(5000);
        CommonUtils.exampleComplete();
    }
}
```

```
RxCachedThreadScheduler-2 | 855 | value = 
>> Favor focus over features.
>> Welcome to Callback Hell!!
```

위의 concatWith()예제에서는 두 가지의 URL을 호출하는 시간이 1707ms 가 걸렸다면, zip()을 사용한 예제는 855ms로 응답을 받기까지의 대기 시간이 줄어든걸 볼 수 있다. 

> RxJava로 비동기 코드 작성했을때의 장점
> 
1. **선언적 동시성**
    
    : 순수한 비즈니스 로직과 비동기 동작을 위한 스레드 부분을 구별
    
2. **가독성**

    : 정상적인 로직과 향후 예외 처리 부분을 분리 


### observeOn() 함수의 활용

앞에서 

- **subscribeOn()** : Observable에서 구독자가 subscribe() 함수를 호출했을 때 데이터 흐름 발행 스레드를 지정하는 함수
- **observeOn()** : 처리된 결과를 구독자에게 전달하는 스레드를 지정하는 함수

라는 것을 배웠다. 하지만 **subscribeOn()함수는 처음 지정한 스레드를 고정**시키므로 다시 subscribeOn()함수를 호출해도 무시한다. 

```java
public class JavaExample {
    public static void main(String[] args) {
        String[] objs = {"1-S", "2-T", "3-P"};
        Observable<String> source = Observable.fromArray(objs)
                .doOnNext(data -> Log.v("Original data = "+data))
                .subscribeOn(Schedulers.newThread())
                .subscribeOn(Schedulers.trampoline())
                .subscribeOn(Schedulers.io())
                .map(Shape::flip);
        source.subscribe(Log::i);
        CommonUtils.sleep(500);
    }
}
```

```
RxNewThreadScheduler-1 | Original data = 1-S
RxNewThreadScheduler-1 | value = (flipped)1-S
RxNewThreadScheduler-1 | Original data = 2-T
RxNewThreadScheduler-1 | value = (flipped)2-T
RxNewThreadScheduler-1 | Original data = 3-P
RxNewThreadScheduler-1 | value = (flipped)3-P
```

맨 처음 newThread()로 설정하고 trampoline, io로 다시 subscribeOn()함수를 호출했지만 RxNewThreadScheduler로 고정되어 출력된다. 

하지만 밑의 사진처럼 subcribeOn과 다르게 observeOn()함수는 스레드가 고정되지않고 계속 변한다. 

![image](https://user-images.githubusercontent.com/53978090/146148441-d8370271-fae1-42c3-8b78-f82e380ae68b.png)

처음에는 파란색 스레드에서 실행되다가, observeOn(주황색)을 호출하면 주황색 스레드에서 계속 호출되고, subscribeOn()을 만나도 변경되지 않다가 또 다른 observeOn(분홍색)을 호출했을때 그제서야 스레드가 변경된다. 이를 통해

1. **subscribe() 함수는 한 번 호출했을 때 결정한 스레드를 고정하여 이후에는 다시 호출해도 스레드가 바뀌지 않는다.**
2. **observeOn() 함수는 여러 번 호출할 수 있으며 호출되면 그 다음부터 동작하는 스레드를 바꿀 수 있다.**

라는 중요한 사실을 알 수 있다.

### OpenWeatherMap 연동

[OpenWeatherMap API]([https://openweathermap.org/api](https://openweathermap.org/api))를 사용해서 특정 도시의 현재 날씨를 얻어오는 예제를 만들어보자.

블로그 메인 화면에 있는 WearWeather 프로젝트에 사용했었던 API 이기도 하다.

```java
public class JavaExample {
    private static final String API_KEY = "발급받은키";
    private static final String URL = "https://api.openweathermap.org/data/2.5/weather?q=Seoul&appid=";
    public void run(){
        Observable<String> source = Observable.just(URL + API_KEY)
                .map(OkHttpHelper::get)
                .subscribeOn(Schedulers.io());
        Observable<String> temperature = source.map(this::parseTemperature);
        Observable<String> city = source.map(this::parseCityName);
        Observable<String> country = source.map(this::parseCountry);
        CommonUtils.exampleStart();
        Observable.concat(temperature, city, country)
                .observeOn(Schedulers.newThread())
                .subscribe(Log::it);
        CommonUtils.sleep(3000);
    }
    private String parseTemperature(String json){
        return parse(json, "\"temp\":[0-9]*.[0-9]*");
    }
    private String parseCityName(String json){
        return parse(json, "\"name\":\"[a-zA-Z]*\"");
    }
    private String parseCountry(String json){
        return parse(json, "\"country\":\"[a-zA-Z]*\"");
    }
    private String parse(String json, String regex){
        Pattern pattern = Pattern.compile(regex);
        Matcher matcher = pattern.matcher(json);
        if(matcher.find()){
            return matcher.group();
        }
        return "N/A";
    }
    public static void main(String[] args) {
        JavaExample exam = new JavaExample();
        exam.run();
    }
}
```

```
RxNewThreadScheduler-1 | 990 | value = "temp":278.21
RxNewThreadScheduler-1 | 1106 | value = "name":"Seoul"
RxNewThreadScheduler-1 | 1207 | value = "country":"KR"
```

위의 예제에서는 temperature, name, country 정보를 얻기 위해 subscribe()를 호출하고, subscribe()할때마다 API가 호출되고 있기 때문에 총 3번의 API를 호출을 한다.

얻어와야할 정보가 많다면, subscribe()를 할때마다 API를 각각 호출하는 방법은 비효율적이다. API를 한 번만 호출해서 정보를 얻을 수는 없을까??

→ 정답은 앞에서 배웠던 ConnectableObservable 클래스를 사용하는 것이다.

- `ConnectableObservable`

    : 차가운 Observable을 뜨거운 Observable로 변환해서 Observable을 여러 구독자에게 공유할 수 있으므로 원 데이터 하나를 여러 구독자에게 동시에 전달할 때 사용한다.

    - **publish()**

        : Observable → ConnectableObservable로 변환해주는 함수

    - **connect()**

        : subscribe()를 호출해도 데이터가 발행되지 않고, 이걸 호출해야만 구독자들에게 데이터가 발행된다.(conect() 이후에 구독을 하면 구독 이후 발행된 데이터만 받고 앞에 발행된 데이터는 받을 수 없다.)

    - **refCount()**

        : 몇명의 구독자가 있는지 알려주는 함수, ConnectableObservable이 일반 Observable처럼 동작하게 한다(더이상 구독하는 Observer가 없을 때 자동으로 자신을 해지하고, 다시 새로운 Observer가 오면 처음부터 다시 발행한다는 뜻!!)→ Subscription count를 계속 세고 있다가 Subscription 개수가 0→1이 되는 시점에 connect()호출하고, refCount()이후에는 subscribe()를 하면 바로 발행이 시작된다. (Subscription 개수가 0이되면 disconnect()수행)


ConnectableObservable클래스의 publish()와 refCount()를 통해 한 번의 API호출로 여러 구독자가 공유할 수 있다. ( = share()) 

![image](https://user-images.githubusercontent.com/53978090/146148477-ac37b96e-4928-48ce-960a-d4c2d6f37426.png)

- share()의 원형

```java
@CheckReturnValue
@SchedulerSupport(SchedulerSupport.NONE)
@NonNull
public final Observable<T> share() {
    return publish().refCount();
}
```

```java
public class JavaExample {
    private static final String API_KEY = "발급받은키";
    private static final String URL = "https://api.openweathermap.org/data/2.5/weather?q=Seoul&appid=";
    public void run(){
        Observable<String> source = Observable.just(URL + API_KEY)
                .map(OkHttpHelper::get)
                .subscribeOn(Schedulers.io())
                .share() // = publish().refCount()
                .observeOn(Schedulers.newThread());
        CommonUtils.exampleStart();
        source.map(this::parseTemperature).subscribe(Log::it);
        source.map(this::parseCityName).subscribe(Log::it);
        source.map(this::parseCountry).subscribe(Log::it);
        CommonUtils.sleep(3000);
    }
    /*...*/
}
```

```
RxNewThreadScheduler-2 | 940 | value = "name":"Seoul"
RxNewThreadScheduler-3 | 940 | value = "country":"KR"
RxNewThreadScheduler-1 | 940 | value = "temp":278.52
```