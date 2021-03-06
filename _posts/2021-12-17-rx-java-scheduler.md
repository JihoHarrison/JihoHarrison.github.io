---
layout: post
title:  "[RxJava] RxKotlin ๋ง๊ณ  RxJava! ๐ก"
date: 2021-12-17 18:03:10 +0700
categories: [RxKotlin]
---

## 0๏ธโฃ ํ๋กค๋ก๊ทธ
* RxKotlin์ ํ์ตํ๋ค๋ณด๋ RxJava์ ๋ํ ํ์๋ ์ ์ฉ ์์ผ๋ณด๊ณ  ์ถ์๋ค.. ๊ทธ๋์... ๊ณต๋ถํ๋ค... ๋ฐค์.. ๋๋ฌด ํ๋ค๋ค.. ํํ.... ํ์๋ฆฌ ๊ทธ๋งํ๊ณ  RxJava๋ฅผ ์ ๋ฆฌ ํด ๋ณด๊ฒ ์ต๋๋ค.. RxKotlin๊ณผ ๋น๊ตํ์ ๋ ์ ๋ง ๋ค๋ฅธ ์ ์ด ์์ด๋ณด์ด์ง๋ง ๊ทธ๋๋ ๊ธฐ์ด๋ถํฐ ๋ค์ ์ก๋๋ค๋ ๋ง์์ผ๋ก! ํนํ ์ค๋ ๋ ์ง์ ์ ๊ดํ Scheduler์ ์ง์คํ์ฌ ํ์ตํ์ต๋๋ค. ํ๋ฐ๋ถ์ OpenWeather API๋ฅผ ์ฌ์ฉํ ๋ถ๋ถ์ด ๋ณด์ผํ๋ฐ, ๋ธ๋ก๊ทธ ๋ฉ์ธ ํ๋ฉด์ WearWeather ํ๋ก์ ํธ์ ์ฌ์ฉ๋ API ์ด๋ค.

### 1๏ธโฃ ์ค์ผ์ค๋ฌ๋?

: ์ฃผ๋ก ๋น๋๊ธฐ ํ๋ก๊ทธ๋๋ฐ์์ ์ฌ์ฉ, RxJava์ฝ๋๋ฅผ ์ด๋ ์ค๋ ๋์์ ์คํํ ์ง ์ง์ ํ  ์ ์๋ค. 

> **subscribeOn()**
> 
: ๊ตฌ๋์๊ฐ Observable์ subscribe()ํจ์๋ฅผ ํธ์ถํ์ฌ **๊ตฌ๋ํ  ๋ ์คํ๋๋ ์ค๋ ๋๋ฅผ ์ง์ ** 

> **observeOn()**
> 
: **์ดํ ์ถ๊ฐ์ ์ธ ์ฐ์ฐ์ ๋ํ ์์ ์ค๋ ๋๋ฅผ ์ค์ ** 

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

subscribeOn()๊ณผ observeOn()๋๋ค ํธ์ถํด์ค์ **๋ฐ์ดํฐ ํ๋ฆ์ด ๋ฐ์ํ๋ ์ค๋ ๋์ ์ฒ๋ฆฌ๋ ๊ฒฐ๊ณผ๋ฅผ ๊ตฌ๋์์๊ฒ ๋ฐํํ๋ ์ค๋ ๋๋ฅผ ๋ถ๋ฆฌํ**๋ค. (subscribeOn(Schedulers.newThread()๋ฅผ ํด์คฌ๊ธฐ๋๋ฌธ์ main์ค๋ ๋๊ฐ ์๋๋ผ ์๋ก์ด ์ค๋ ๋์์ ๋์๊ฐ๋๊ฒ๋ ๋ณผ ์ ์๋ค

observeOn์ ์ฃผ์์ฒ๋ฆฌํด๋ณด์.

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

subscribeOn()๋ง ํธ์ถํ๊ธฐ๋๋ฌธ์ ์๋ก ๋ง๋ค์ด์ง ํ๋์ ์ค๋ ๋์์ ๋ชจ๋  ์์์ด ์ด๋ฃจ์ด์ง๋ค.

๊ทธ๋ฌ๋ฉด ์ด๋ฒ์ subscribeOn()์ ์ฃผ์์ฒ๋ฆฌํด๋ณด์.

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

observeOn()์ ํธ์ถ ์ดํ์ ์์์ ()์์ ์ค๋ ๋์์ ์คํํ๊ธฐ๋๋ฌธ์, ์ด์ ์์๋ main์ค๋ ๋๋ก ์์์ด ์ด๋ฃจ์ด์ง๊ณ (subscribeOn(Schedulers.newThread()๋ฅผ ํ์ง ์์์ผ๋๊น! ์๋ main์ค์ผ์ค๋ฌ์์ ๋์), ์ดํ๋ก๋ observeOn(Schedulers.newThread())๋ก ๋ง๋ค์ด์ค ์๋ก์ด ์ค๋ ๋์์ ์์์ด ์ด๋ฃจ์ด์ง๋๊ฑธ ๋ณผ ์ ์๋ค.

๊ทธ๋ฆฌ๊ณ  ์๋ก์ด ์ค๋ ๋์์ ์์์ ํ๊ธฐ ๋๋ฌธ์ ์๋ก์ด ์ค๋ ๋ ์์์ด ์ ๋ถ ๋๋ ๋๊น์ง  CommonUtils.sleep()์ผ๋ก ๋ฉ์ธ ์ค๋ ๋๋ฅผ ๊ธฐ๋ค๋ฆฌ๊ฒ ํด์ผํ๋ค. 

> ์ ๋ฆฌ
> 
- **subscribeOn() ํจ์์ observeOn()ํจ์๋ฅผ ๋ชจ๋ ์ง์ **ํ๋ฉด Observable์์ **๋ฐ์ดํฐ ํ๋ฆ์ด ๋ฐ์ํ๋ ์ค๋ ๋์ ์ฒ๋ฆฌ๋ ๊ฒฐ๊ณผ๋ฅผ ๊ตฌ๋์์๊ฒ ๋ฐํํ๋ ์ค๋ ๋๋ฅผ ๋ถ๋ฆฌ**ํ  ์ ์๋ค.
- subscribeOn()๋ง ํธ์ถํ๋ฉด Observable์ ๋ชจ๋  ํ๋ฆ์ด ๋์ผํ ์ค๋ ๋์์ ์คํ๋๋ค.
- ์ค์ผ์ค๋ฌ๋ฅผ ๋ณ๋๋ก ์ง์ ํ์ง ์์ผ๋ฉด ํ์ฌ(main)์ค๋ ๋์์ ๋์์ ์คํํ๋ค.
### ์ค์ผ์ค๋ฌ์ ์ข๋ฅ

| ์ค์ผ์ค๋ฌ | RxJava 2.x | RxJava 1.x |
| --- | --- | --- |
| ๋ด ์ค๋ ๋ ์ค์ผ์ค๋ฌ | newThread() | newThread() |
| ์ฑ๊ธ ์ค๋ ๋ ์ค์ผ์ค๋ฌ | single() | ์ง์ ์ ํจ |
| ๊ณ์ฐ ์ค์ผ์ค๋ฌ | computation() | computation() |
| IO ์ค์ผ์ค๋ฌ | io() | io() |
| ํธ๋จํ๋ฆฐ ์ค์ผ์ค๋ฌ | trampoline() | trampoline() |
| ๋ฉ์ธ ์ค๋ ๋ ์ค์ผ์ค๋ฌ | ์ง์ ์ ํจ | immediate() |
| ํ์คํธ ์ค์ผ์ค๋ฌ | ์ง์ ์ ํจ | test() |

### ๋ด ์ค๋ ๋ ์ค์ผ์ค๋ฌ

: ์๋ก์ด ์ค๋ ๋๋ฅผ ๋ง๋ค์ด ์ด๋ค ๋์์ ์คํํ๊ณ  ์ถ์ ๋ Schedulers.newThread()๋ฅผ ์ธ์๋ก ๋ฃ์ด์ฃผ๋ฉด, ์์ฒญ์ ๋ฐ์ ๋๋ง๋ค ์๋ก์ด ์ค๋๋๋ฅผ ์์ฑํ๋ค.

```java
public class JavaExample {
    public static void main(String[] args) {
        String[] orgs = {"1", "3", "5"};
        Observable.fromArray(orgs)
                .doOnNext(data -> Log.v("Original data: "+ data))
                .map(data -> "<<" + data + ">>")
                .subscribeOn(Schedulers.newThread()) //์ฒซ ๋ฒ์งธ new Thread 
                .subscribe(Log::i);
~~~~        CommonUtils.sleep(500);
        Observable.fromArray(orgs)
                .doOnNext(data -> Log.v("Original data: "+ data))
                .map(data -> "##" + data + "##")
                .subscribeOn(Schedulers.newThread())//๋ ๋ฒ์งธ new Thread
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

์๊น subscribeOn()๋ง ํธ์ถํ๋ฉด Observable์ ๋ชจ๋  ํ๋ฆ์ด ๋์ผํ ์ค๋ ๋์์ ์คํ๋๋ค๊ณ  ํ๋ค. ๊ทธ๋์ ์ฒซ๋ฒ์งธ Observable์์ subscribeOn(Schedulers.newThread())๋ฅผ ํธ์ถํ๊ธฐ ๋๋ฌธ์, ๋ชจ๋ ์๋ก์ด ์ค๋ ๋์ธ RxNewThreadScheduler-1์์ ์คํ๋๊ณ , ๋ ๋ฒ์งธ Observable์์ subscribeOn(Schedulers.newThread())์ ๋ ํธ์ถํ๊ธฐ ๋๋ฌธ์ ๋ชจ๋ ์๋ก์ด ์ค๋ ๋์ธ RxNewThreadScheduler-2์์ ์คํ๋๋ค.  

### ๊ณ์ฐ์ค์ผ์ค๋ฌ

: CPU์ ๋์ํ๋ ๊ณ์ฐ์ฉ ์ค์ผ์ค๋ฌ, '๊ณ์ฐ' ์์์ ํ  ๋๋ ๋๊ธฐ ์๊ฐ ์์ด ๋น ๋ฅด๊ฒ ๊ฒฐ๊ณผ๋ฅผ ๋์ถํ๋ ๊ฒ์ด ์ค์ํ๋ค. โ ์์ถ๋ ฅ(I/O) ์์์ ํ์ง ์๋ ์ค์ผ์ค๋ฌ

**๋ด๋ถ์ ์ผ๋ก ์ค๋ ๋ ํ์ ์์ฑํ๋ฉฐ ์ค๋ ๋ ๊ฐ์๋ ๊ธฐ๋ณธ์ ์ผ๋ก ํ๋ก์ธ์ ๊ฐ์์ ๋์ผํ๋ค.**

```java
public class JavaExample {
    public static void main(String[] args) {
        String[] orgs = {"1", "3", "5"};
        Observable<String> source = Observable.fromArray(orgs)
                .zipWith(Observable.interval(100L, TimeUnit.MILLISECONDS), (a, b) -> a); //์๊ฐ๊ณผ ํฉ์ฑ, 1 3 5๊ฐ 100ms๋ง๋ค ๋ฐํ 
				//์ฒซ ๋ฒ์งธ ๊ตฌ๋
        source.map(item -> "<<" + item + ">>")
                //.subscribeOn(Schedulers.computation())
                .subscribe(Log::i);
				//๋ ๋ฒ์งธ ๊ตฌ๋
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

(a, b) โ a ๋ก ์ธํด ์๊ฐ์ ๊ทธ๋๋ก ๋๊ณ  1 3 5๊ฐ 100ms๋ง๋ค ๋ฐํ๋๋ฉฐ subscribeOn(Schedulers.computation())์ผ๋ก **๊ฐ๊ฐ ์๋ก์ด ์ค์ผ์ค๋ฌ๋ฅผ ๋ง๋ค์ด ๋์์ ์ถ๋ ฅ**ํ๋ค.  

(์ฌ๊ธฐ์๋ subscribeOn(Schedulers.computation())์ผ๋ก ๊ณ์ฐ์ค์ผ์ค๋ฌ๋ฅผ ๋ช์ํด์คฌ์ง๋ง interval()ํจ์๊ฐ ๊ธฐ๋ณธ์ ์ผ๋ก ๊ณ์ฐ ์ค์ผ์ค๋ฌ๋ฅผ ์ฌ์ฉํ๊ธฐ ๋๋ฌธ์ ์ ๊ฑฐํ๊ณ  ์คํํด๋ ๋๊ฐ์ ๊ฒฐ๊ณผ๊ฐ ๋์จ๋ค.) 

์ฌ๋ฌ๋ฒ ์คํํ๋ค๋ณด๋ฉด <<>>์ #### ๋ฐ์ดํฐ๊ฐ ๋ค์์ผ๋๊ฐ ์๋๋ฐ, ์ด๋ ์ฒซ ๋ฒ์งธ ๊ตฌ๋๊ณผ ๋ ๋ฒ์งธ ๊ตฌ๋์ด ๊ฑฐ์ ๋์์ ์ด๋ฃจ์ด์ง๊ธฐ ๋๋ฌธ์ RxJava ๋ด๋ถ์์ ๋์ผํ ์ค๋ ๋์ ์์์ ํ ๋นํ๊ธฐ ๋๋ฌธ์ด๋ผ๊ณ  ํ๋ค.

### IO ์ค์ผ์ค๋ฌ

: ๊ณ์ฐ ์ค์ผ์ค๋ฌ์๋ ๋ค๋ฅด๊ฒ ๋คํธ์ํฌ์์ ์์ฒญ์ ์ฒ๋ฆฌํ๊ฑฐ๋ ๊ฐ์ข ์์ถ๋ ฅ ์์์ ์คํํ๊ธฐ ์ํ ์ค์ผ์ค๋ฌ๋ก ๊ธฐ๋ณธ์ผ๋ก ์์ฑ๋๋ ์ค๋ ๋ ๊ฐ์๊ฐ ๋ค๋ฅด๋ค.

์ฆ, **๊ณ์ฐ ์ค์ผ์ค๋ฌ๋ CPU๊ฐ์๋งํผ ์ค๋ ๋๋ฅผ ์์ฑ**ํ์ง๋ง, **IO์ค์ผ์ค๋ฌ๋ ํ์ํ  ๋๋ง๋ค ์ค๋ ๋๋ฅผ ๊ณ์ ์์ฑ**ํ๋ค.

(์์ถ๋ ฅ ์์์ ๋น๋๊ธฐ๋ก ์คํ๋์ง๋ง ๊ฒฐ๊ณผ๋ฅผ ์ป๊ธฐ๊น์ง ๋๊ธฐ ์๊ฐ์ด ๊ธธ๋ค.) 

- `๊ณ์ฐ ์ค์ผ์ค๋ฌ` : ์ผ๋ฐ์ ์ธ ๊ณ์ฐ ์์
- `IO ์ค์ผ์ค๋ฌ`: ๋คํธ์ํฌ์์ ์์ฒญ, ํ์ผ ์์ถ๋ ฅ, DB ์ฟผ๋ฆฌ ๋ฑ

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

Desktop/navigationํด๋์ ์๋ ํ์ผ ๋ฆฌ์คํธ๋ค์ ๊ฐ์ ธ์ ์ ์ถ๋ ฅํด์ค๋ค.

### ํธ๋จํ๋ฆฐ ์ค์ผ์ค๋ฌ

: ์๋ก์ด ์ค๋ ๋๋ฅผ ์์ฑํ์ง ์๊ณ  **ํ์ฌ ์ค๋ ๋์ ๋ฌดํํ ํฌ๊ธฐ์ ๋๊ธฐ ํ๋ ฌ์ ์์ฑํ๋ ์ค์ผ์ค๋ฌ**

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

์ฒ์์ ์ง์ ํ ์ค๋ ๋๋ก ๊ตฌ๋์์๊ฒ ๋ฐ์ดํฐ๋ฅผ ๋ฐํํ๋ค.

์๋ก์ด ์ค๋ ๋๋ฅผ ์์ฑํ์ง ์๊ณ  **main ์ค๋ ๋์์ ๋ชจ๋  ์์์ ์คํ**ํ๋ค. ํ์ ์์์ ๋ฃ์ ํ **1๊ฐ์ฉ ๊บผ๋ด์ ๋์ํ๋ฏ๋ก ์ฒซ ๋ฒ์งธ ๊ตฌ๋๊ณผ ๋ ๋ฒ์งธ ๊ตฌ๋ ์์๋๋ก ๊ฐ์ด ์ถ๋ ฅ**๋๋ค.

### ์ฑ๊ธ ์ค๋ ๋ ์ค์ผ์ค๋ฌ

: RxJava ๋ด๋ถ์์ ๋จ์ผ ์ค๋ ๋๋ฅผ ๋ณ๋๋ก ์์ฑํ์ฌ ๊ตฌ๋ ์์์ ์ฒ๋ฆฌํ๋ค. (์์ฑ๋ ์ค๋ ๋๋ ์ฌ๋ฌ ๋ฒ ๊ตฌ๋ ์์ฒญ์ด ์๋ ๊ณตํต์ผ๋ก ์ฌ์ฉํ๋ค.)

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


์ฑ๊ธ ์ค๋ ๋ ์ค์ผ์ค๋ฌ์์ ์คํํ๋ฉด ๋น๋ก ์ฌ๋ฌ ๊ฐ Observable์ด ์์ด๋ ๋ณ๋๋ก ๋ง๋ จํด๋์ **๋จ์ผ ์ค๋ ๋์์ ์ฐจ๋ก๋ก ์คํํ๋ฏ๋ก, RxSingleScheduler-1์์ 100~104, 0~4๊ฐ ์ฐจ๋ก๋๋ก map ๋์ด ๋์จ๋ค**.

### Executor ๋ณํ ์ค์ผ์ค๋ฌ

Executor๋ฅผ ๋ณํํ์ฌ ์ค์ผ์ค๋ฌ๋ฅผ ์์ฑํ  ์ ์๋ค. ํ์ง๋ง Executor ํด๋์ค์ ์ค์ผ์ค๋ฌ์ ๋์ ๋ฐฉ์๊ณผ ๋ค๋ฅด๋ฏ๋ก ์ถ์ฒ๋ฐฉ์์ด ์๋๋ผ๊ณ  ํ๋, ๊ทธ๋ฅ ์ด๋ฐ ๋ฐฉ๋ฒ๋ ์๊ตฌ๋~ ์ฐธ๊ณ ๋ง ํ๋ฉด ์ข์๊ฑฐ๊ฐ๋ค. 

    ๋์์ ๋ค์์ ์์ฒญ์ ์ฒ๋ฆฌํด์ผํ  ๋, ์ฌ๋ฌ๊ฐ์ ์ค๋ ๋๋ฅผ ๊ณ์ํด์ ์์ฑํ  ์ 

    - ์ฐ๋ ๋ ์์ฑ ๋ฐ ์ข๋ฃ์ ๋ฐ๋ฅธ ์ค๋ฒํค๋ ๋ฐ์
    - ์์ฑ๋๋ ์ค๋ ๋์ ๊ฐ์์ ์ ํ์ ํด์ฃผ์ง ์์ผ๋ฉด OutOfMemoryError๋ฐ์ ๊ฐ๋ฅ
    - ๋ง์ ์์ ์ค๋ ๋ ์คํ โ ์ค๋ ๋ ์ค์ผ์ค๋ง์ ๋ํด ์ค๋ฒํค๋ ๋ฐ์

    ๋ฑ ์ฌ๋ฌ๊ฐ์ง์ ๋ฌธ์ ์ ์ด ๋ฐ์ํ๋ค. 

    ์ด๋ฅผ ๋ฐฉ์งํ๊ธฐ ์ํด ๋์์ ์คํ๋  ์ ์๋ ์ฐ๋ ๋์ ๊ฐ์๋ฅผ ์ ํํด์ผํ๋๋ฐ, ์ด๋ฅผ ์ํด Executor์ธํฐํ์ด์ค๋ผ๋๊ฒ ์๊ฒจ๋ฌ๊ณ  ์ฐ๋ ๋ ํ, ํ ๋ฑ ๋ค์ํ Executor ๊ตฌํ์ฒด๋ฅผ ์ ๊ณตํ๊ณ  ์๋ค๊ณ  ํ๋ค.




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

THREAD_NUM์ 10๊ฐ๋ก ์ง์ ํด์ 10๊ฐ์ ๊ณ ์  ์ค๋ ๋ ํ์ ์์ฑํ๋ค. ์ฒซ ๋ฒ์งธ Observable๊ณผ ๋ ๋ฒ์งธ Observable์์ subscribeOn()์ ํธ์ถํ์ฌ Executor ๋ณํ ์ค์ผ์ค๋ฌ๋ฅผ ์ง์ ํ๊ธฐ ๋๋ฌธ์ ๊ฐ๊ฐ ๋ค๋ฅธ ์ค๋ ๋ ํ์์ ๋์ํ๋ค. 

๋ฐฉ๊ธ์์ ์์๋ Executors.newFixedThreadPool(์ค๋๋๊ฐฏ์)๋ฅผ ์ฌ์ฉํด์ ๊ฐ๊ฐ ๋ค๋ฅธ ์ค๋ ๋์์ ๋์ํ์ง๋ง, Executors.newSingleThreadExecutor()๋ก ์คํํ๋ฉด ์ค๋ ๋ ํ๋๋ก ๋์ํ๋ค.

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

### ์ค์ผ์ค๋ฌ๋ฅผ ํ์ฉํ์ฌ ์ฝ๋ฐฑ ์ง์ฅ ๋ฒ์ด๋๊ธฐ

- ์ฝ๋ฐฑ์ง์ฅ์ด๋?

    ์ฐ๋ฆฌ๊ฐ ๋น๋๊ธฐ ์์์ ํ๋ค๋ณด๋ฉด ์ฝ๋ฐฑ ํจ์๋ฅผ ์ฌ์ฉํด์ ์๋ฒํต์ ์ ํ ๋๊ฐ ์๋๋ฐ, ์ฝ๋ฐฑ ํจ์๋ฅผ ์ฌ์ฉํ๋ค๋ฉด ๋ฆฌํด๋ ๊ฒฐ๊ณผ์ ๋ฐ๋ผ ๊ณ์ ๋ค์ฌ์ฐ๊ธฐ๋ฅผ ํ๋ฉฐ ํ๊ณ ํ๊ณ  ๋ค์ด๊ฐ๋ ์ฝ๋๋ฅผ ์์ฑํ๊ฒ ๋๋ค. ์ด๋ ์ฝ๋ฐฑ ์ง์ฅ์ด๋ผ๊ณ  ๋ถ๋ฆด๋งํผ ๊ฐ๋์ฑ๋ ๋งค์ฐ ๋จ์ด์ง๊ณ  ์ข์ง์์ ๋ฐฉ๋ฒ์ด๋ค. ๐  ํ์ง๋ง Rxjava์ ์ฒด์ด๋์ ์ฌ์ฉํ๋ฉด ์ฝ๋ฐฑ์ง์ฅ์ผ๋ก๋ถํฐ ๋ฒ์ด๋  ์ ์๋ค!


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
            public void onResponse(Call call, Response response) throws IOException { //์ฑ๊ณตํ์๋๋ง 
                Log.i("1๋ฒ์งธ"+response.body().string());
                Request request = new Request.Builder()
                        .url(SECOND_URL)
                        .build();
                client.newCall(request).enqueue(new Callback() { //๋ ๋ค์ ์ฝ๋ฐฑ ์ถ๊ฐ 
                    @Override
                    public void onFailure(Call call, IOException e) {
                        e.printStackTrace();
                    }
                    @Override
                    public void onResponse(Call call, Response response) throws IOException {
                        Log.i("2๋ฒ์งธ"+ response.body().string());
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
OkHttp https://api.github.com/... | value = 1๋ฒ์งธSpeak like a human.
OkHttp https://raw.githubuserc... | value = 2๋ฒ์งธWelcome to Callback Hell!!
```

๋งจ ์ฒ์ ์๋ฒํต์ ์ ํ์๋ ์ฌ์ฉํ๋ ๋ฐฉ๋ฒ์ด์๋ค. ์ฒซ ๋ฒ์งธ ํธ์ถ์ ์ฑ๊ณต๊ณผ ์คํจ๋ฅผ ๊ณ ๋ คํ๊ณ , ๋ค์ ๊ทธ๊ฒ์ ๊ธฐ์ค์ผ๋ก ๋ ๋ฒ์งธ ํธ์ถ์ ์ฑ๊ณต๊ณผ ์คํจ๋ฅผ ๊ณ ๋ คํ์ฌ ์ฝ๋๋ฅผ ์์ฑํ๋ค. ๋ฑ ๋ด๋ ํ๋์ ๋ณด๊ธฐ ์ด๋ ต๊ณ  ์ ์์ ์ธ ๋ก์ง๊ณผ ์์ธ ์ฒ๋ฆฌ๊ฐ ํผ์ฌ๋์ด์๋๊ฑธ ๋ณผ ์ ์๋ค. (ํ๋ก์ ํธ ๊ท๋ชจ๊ฐ ์ปค์ง๊ณ  API๋ฅผ ํธ์ถํ ๋๋ง๋ค ์ฝ๋ฐฑ์ ์ฌ์ฉํ๋ค๋ฉด..? ใใ) 

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

Rxjava๋ฅผ ์ฌ์ฉํ์ง ์์ ์์ ์์ ์๋ ๋์ ๋๊ฒ ๊ฐ๊ฒฐํด์ง๊ณ  ๋ชํํด์ก๋ค. 

subscribeOn(Schedulers.io())๋ฅผ ํธ์ถํด์ io์ค์ผ์ค๋ฌ์์ ์์์ ์คํํ๊ณ , ์์ ์์ ์์๋ OkHttpClient์ enqueue()๋ฉ์๋๋ฅผ ํธ์ถํด์ ์ฝ๋ฐฑ์ ์ ๋ฌ๋ฐ์์ง๋ง, ์ด ์์ ์์๋ OkhttpHelper.get()์ ํตํด execute()๋ฅผ ํธ์ถํ๋ค. (concat()์ ์ฒซ ๋ฒ์งธ Observable๊ณผ ๋ ๋ฒ์งธ Observable์ ํจ๊ป ์ธ์๋ก ๋ฃ์ด์ผํ์ง๋ง concatWith()์ ํ์ฌ์ Observable์ ์๋ก์ด Observable์ ๊ฒฐํฉํ  ์ ์๋ค๋ ์ฐจ์ด๊ฐ ์๋ค.) ์ฒซ ๋ฒ์งธ ํธ์ถ์ด ๋๋๋ฉด, ๋ ๋ฒ์งธ ํธ์ถ์ด ์ด๋ค์ง๊ฒ ๋๊ณ  ์๊น์ ๋๊ฐ์ ๊ฒฐ๊ณผ๊ฐ ๋ํ๋๋ค. 

ํ์ง๋ง concatWith()์ ์ฒซ ๋ฒ์งธ Observable์ด ๋๋ ๋๊น์ง ๊ธฐ๋ค๋ ค์ผํ๊ธฐ๋๋ฌธ์ zip()์ ์ฌ์ฉํด์ ์ฒซ ๋ฒ์งธ URL๊ณผ ๋ ๋ฒ์งธ URL์์ฒญ์ ๋์์ ์ํํ๊ณ  ๊ฒฐ๊ณผ๋ง ๊ฒฐํฉํ๋๊ฒ ๋ ํจ์จ์ ์ด๋ค.

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

์์ concatWith()์์ ์์๋ ๋ ๊ฐ์ง์ URL์ ํธ์ถํ๋ ์๊ฐ์ด 1707ms ๊ฐ ๊ฑธ๋ ธ๋ค๋ฉด, zip()์ ์ฌ์ฉํ ์์ ๋ 855ms๋ก ์๋ต์ ๋ฐ๊ธฐ๊น์ง์ ๋๊ธฐ ์๊ฐ์ด ์ค์ด๋ ๊ฑธ ๋ณผ ์ ์๋ค. 

> RxJava๋ก ๋น๋๊ธฐ ์ฝ๋ ์์ฑํ์๋์ ์ฅ์ 
> 
1. **์ ์ธ์  ๋์์ฑ**
    
    : ์์ํ ๋น์ฆ๋์ค ๋ก์ง๊ณผ ๋น๋๊ธฐ ๋์์ ์ํ ์ค๋ ๋ ๋ถ๋ถ์ ๊ตฌ๋ณ
    
2. **๊ฐ๋์ฑ**

    : ์ ์์ ์ธ ๋ก์ง๊ณผ ํฅํ ์์ธ ์ฒ๋ฆฌ ๋ถ๋ถ์ ๋ถ๋ฆฌ 


### observeOn() ํจ์์ ํ์ฉ

์์์ 

- **subscribeOn()** : Observable์์ ๊ตฌ๋์๊ฐ subscribe() ํจ์๋ฅผ ํธ์ถํ์ ๋ ๋ฐ์ดํฐ ํ๋ฆ ๋ฐํ ์ค๋ ๋๋ฅผ ์ง์ ํ๋ ํจ์
- **observeOn()** : ์ฒ๋ฆฌ๋ ๊ฒฐ๊ณผ๋ฅผ ๊ตฌ๋์์๊ฒ ์ ๋ฌํ๋ ์ค๋ ๋๋ฅผ ์ง์ ํ๋ ํจ์

๋ผ๋ ๊ฒ์ ๋ฐฐ์ ๋ค. ํ์ง๋ง **subscribeOn()ํจ์๋ ์ฒ์ ์ง์ ํ ์ค๋ ๋๋ฅผ ๊ณ ์ **์ํค๋ฏ๋ก ๋ค์ subscribeOn()ํจ์๋ฅผ ํธ์ถํด๋ ๋ฌด์ํ๋ค. 

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

๋งจ ์ฒ์ newThread()๋ก ์ค์ ํ๊ณ  trampoline, io๋ก ๋ค์ subscribeOn()ํจ์๋ฅผ ํธ์ถํ์ง๋ง RxNewThreadScheduler๋ก ๊ณ ์ ๋์ด ์ถ๋ ฅ๋๋ค. 

ํ์ง๋ง ๋ฐ์ ์ฌ์ง์ฒ๋ผ subcribeOn๊ณผ ๋ค๋ฅด๊ฒ observeOn()ํจ์๋ ์ค๋ ๋๊ฐ ๊ณ ์ ๋์ง์๊ณ  ๊ณ์ ๋ณํ๋ค. 

![image](https://user-images.githubusercontent.com/53978090/146148441-d8370271-fae1-42c3-8b78-f82e380ae68b.png)

์ฒ์์๋ ํ๋์ ์ค๋ ๋์์ ์คํ๋๋ค๊ฐ, observeOn(์ฃผํฉ์)์ ํธ์ถํ๋ฉด ์ฃผํฉ์ ์ค๋ ๋์์ ๊ณ์ ํธ์ถ๋๊ณ , subscribeOn()์ ๋ง๋๋ ๋ณ๊ฒฝ๋์ง ์๋ค๊ฐ ๋ ๋ค๋ฅธ observeOn(๋ถํ์)์ ํธ์ถํ์๋ ๊ทธ์ ์์ผ ์ค๋ ๋๊ฐ ๋ณ๊ฒฝ๋๋ค. ์ด๋ฅผ ํตํด

1. **subscribe() ํจ์๋ ํ ๋ฒ ํธ์ถํ์ ๋ ๊ฒฐ์ ํ ์ค๋ ๋๋ฅผ ๊ณ ์ ํ์ฌ ์ดํ์๋ ๋ค์ ํธ์ถํด๋ ์ค๋ ๋๊ฐ ๋ฐ๋์ง ์๋๋ค.**
2. **observeOn() ํจ์๋ ์ฌ๋ฌ ๋ฒ ํธ์ถํ  ์ ์์ผ๋ฉฐ ํธ์ถ๋๋ฉด ๊ทธ ๋ค์๋ถํฐ ๋์ํ๋ ์ค๋ ๋๋ฅผ ๋ฐ๊ฟ ์ ์๋ค.**

๋ผ๋ ์ค์ํ ์ฌ์ค์ ์ ์ ์๋ค.

### OpenWeatherMap ์ฐ๋

[OpenWeatherMap API]([https://openweathermap.org/api](https://openweathermap.org/api))๋ฅผ ์ฌ์ฉํด์ ํน์  ๋์์ ํ์ฌ ๋ ์จ๋ฅผ ์ป์ด์ค๋ ์์ ๋ฅผ ๋ง๋ค์ด๋ณด์.

๋ธ๋ก๊ทธ ๋ฉ์ธ ํ๋ฉด์ ์๋ WearWeather ํ๋ก์ ํธ์ ์ฌ์ฉํ์๋ API ์ด๊ธฐ๋ ํ๋ค.

```java
public class JavaExample {
    private static final String API_KEY = "๋ฐ๊ธ๋ฐ์ํค";
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

์์ ์์ ์์๋ temperature, name, country ์ ๋ณด๋ฅผ ์ป๊ธฐ ์ํด subscribe()๋ฅผ ํธ์ถํ๊ณ , subscribe()ํ ๋๋ง๋ค API๊ฐ ํธ์ถ๋๊ณ  ์๊ธฐ ๋๋ฌธ์ ์ด 3๋ฒ์ API๋ฅผ ํธ์ถ์ ํ๋ค.

์ป์ด์์ผํ  ์ ๋ณด๊ฐ ๋ง๋ค๋ฉด, subscribe()๋ฅผ ํ ๋๋ง๋ค API๋ฅผ ๊ฐ๊ฐ ํธ์ถํ๋ ๋ฐฉ๋ฒ์ ๋นํจ์จ์ ์ด๋ค. API๋ฅผ ํ ๋ฒ๋ง ํธ์ถํด์ ์ ๋ณด๋ฅผ ์ป์ ์๋ ์์๊น??

โ ์ ๋ต์ ์์์ ๋ฐฐ์ ๋ ConnectableObservable ํด๋์ค๋ฅผ ์ฌ์ฉํ๋ ๊ฒ์ด๋ค.

- `ConnectableObservable`

    : ์ฐจ๊ฐ์ด Observable์ ๋จ๊ฑฐ์ด Observable๋ก ๋ณํํด์ Observable์ ์ฌ๋ฌ ๊ตฌ๋์์๊ฒ ๊ณต์ ํ  ์ ์์ผ๋ฏ๋ก ์ ๋ฐ์ดํฐ ํ๋๋ฅผ ์ฌ๋ฌ ๊ตฌ๋์์๊ฒ ๋์์ ์ ๋ฌํ  ๋ ์ฌ์ฉํ๋ค.

    - **publish()**

        : Observable โ ConnectableObservable๋ก ๋ณํํด์ฃผ๋ ํจ์

    - **connect()**

        : subscribe()๋ฅผ ํธ์ถํด๋ ๋ฐ์ดํฐ๊ฐ ๋ฐํ๋์ง ์๊ณ , ์ด๊ฑธ ํธ์ถํด์ผ๋ง ๊ตฌ๋์๋ค์๊ฒ ๋ฐ์ดํฐ๊ฐ ๋ฐํ๋๋ค.(conect() ์ดํ์ ๊ตฌ๋์ ํ๋ฉด ๊ตฌ๋ ์ดํ ๋ฐํ๋ ๋ฐ์ดํฐ๋ง ๋ฐ๊ณ  ์์ ๋ฐํ๋ ๋ฐ์ดํฐ๋ ๋ฐ์ ์ ์๋ค.)

    - **refCount()**

        : ๋ช๋ช์ ๊ตฌ๋์๊ฐ ์๋์ง ์๋ ค์ฃผ๋ ํจ์, ConnectableObservable์ด ์ผ๋ฐ Observable์ฒ๋ผ ๋์ํ๊ฒ ํ๋ค(๋์ด์ ๊ตฌ๋ํ๋ Observer๊ฐ ์์ ๋ ์๋์ผ๋ก ์์ ์ ํด์งํ๊ณ , ๋ค์ ์๋ก์ด Observer๊ฐ ์ค๋ฉด ์ฒ์๋ถํฐ ๋ค์ ๋ฐํํ๋ค๋ ๋ป!!)โ Subscription count๋ฅผ ๊ณ์ ์ธ๊ณ  ์๋ค๊ฐ Subscription ๊ฐ์๊ฐ 0โ1์ด ๋๋ ์์ ์ connect()ํธ์ถํ๊ณ , refCount()์ดํ์๋ subscribe()๋ฅผ ํ๋ฉด ๋ฐ๋ก ๋ฐํ์ด ์์๋๋ค. (Subscription ๊ฐ์๊ฐ 0์ด๋๋ฉด disconnect()์ํ)


ConnectableObservableํด๋์ค์ publish()์ refCount()๋ฅผ ํตํด ํ ๋ฒ์ APIํธ์ถ๋ก ์ฌ๋ฌ ๊ตฌ๋์๊ฐ ๊ณต์ ํ  ์ ์๋ค. ( = share()) 

![image](https://user-images.githubusercontent.com/53978090/146148477-ac37b96e-4928-48ce-960a-d4c2d6f37426.png)

- share()์ ์ํ

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
    private static final String API_KEY = "๋ฐ๊ธ๋ฐ์ํค";
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