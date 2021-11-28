---
layout: post
title: "[CS] Factory 패턴 (Design Pattern)"
date: 2021-11-16 10:34:10 +0700
categories: [CS]
---

## 0️⃣ 프롤로그
 * 빌더 패턴에 이어서 팩토리 패턴에 대한 정리 내용이다.
 * 안드로이드 MVVM 구조 중에서 AAC의 뷰모델 클래스를 사용하여 개발을 할 때 Hilt와 같은 의존성 주입 라이브러리를 사용하지 않는 이상 항상 ViewModelFractory를 구현 해 주어야 했다.

## 1️⃣ 뷰모델의 팩토리 패턴?
 * 기본적으로 ViewModel은 View의 상태 변화 즉, configuration change가 발생하는 시점에도 가지고 있는 데이터가 살아남도록 설계가 되어있다. 그 역할을 담당하는 부분이 바로 ViewModelStore라는 클래스이다.
 
 * ViewMmodelProvider의 인자로 ViewModelFactory를 넘겨주지 않을 시 다음과 같은 default ViewModelFactory를 호출하게 되고, default만을 가지고 ViewModelFactory로 각 configuration에 맞춰지지 않은 ViewModel을 가지게 된다.
![defauleViewModelFactory](https://user-images.githubusercontent.com/27722059/141942023-e95321f2-882b-4ef7-ac4c-6b4d7c456979.png)
> 이는 곧 Memory Leak을 유발시키는 원인이 되고, 앞으로 안드로이드를 개발하면서 이러한 문제를 유발하지 않도록 팩토리 패턴에 대한 이해가 필요하다고 판단했다.

## 2️⃣ Factory Method
#### 팩토리 메소드 패턴(Factory Method Pattern)이란 상위 클래스에 알려지지 않은 구현 클래스를 생성하는 패턴이다.
#### 또한 하위 클래스가 어떤 객체를 생성할지 결정하도록 하는 패턴이기도 하다. 그리고 상위 클래스 코드에 구체적인 클래스 이름을 감추기 위한 방법으로도 사용한다.

팩토리는 공장이란 뜻이다. 따라서 팩토리 패턴도 무언가를 가공하기 위한 공장이라고 보면 된다.

```java
// 이름을 통해 신발을 주문 받음
Shoes orderShoes(String name) {
    // 해당 이름의 신발을 찾아서 특정 구상 객체 생성
    Shoes shoes;
    
    if (name.equals("blackShoes"))     shoes = new BlackShoes();
    else if (name.equals("brownShoes"))shoes = new BrownShoes();
    else if (name.equals("redShoes"))  shoes = new RedShoes();
    
    // 신발을 준비하고 포장하는 메소드
    // 모든 신발 공용 메소드
    shoes.prepare(); 
    shoes.packing(); 
 
    return shoes;
}
```
현재 이 신발 매장에는 3개의 신발만 팔고 있다. 그리고 앞으로 판매되는 제품이 늘어나거나 지금 있는 제품이 더 이상 판매 되지 않을 수도 있다. 이 부분은 언제나 변경이 가능 한 부분이다.

그러나 밑에 있는 prepare()과 packing() 두 메소드는 제품에 변화가 생기더라도 변하지 않는 부분이다.

위 코드를 간단하게 캡슐화하여 ShoesFactory라는 클래스로 만들면

> ShoesFactory.java

```java
public class ShoesFactory {
 
    public Shoes makeShoes(String name) {
 
       Shoes shoes = null;
       if (name.equals("blackShoes"))     shoes = new BlackShoes();
       else if (name.equals("brownShoes"))shoes = new BrownShoes();
       else if (name.equals("redShoes"))  shoes = new RedShoes();
       
       return shoes;
    }
}
```

위 코드처럼 만들 수 있다. 그리고 사용은 아래처럼 할 수 있다.

> ShoesStore.java

```java
public class ShoesStore {
     
    ShoesFactory factory;
 
    public ShoesStore(ShoesFactory factory) {
        this.factory = factory;
    }
   
    Shoes orderShoes(String name) {
        Shoes shoes = factory.makeShoes(name);
        shoes.prepare(); 
        shoes.packing(); 
 
        return shoes;
    }
}
```

고객에게 특정 신발에 대한 주문이 들어 왔을 때 매장에서는 공장에 해당 신발 오더를 넣고 받으면 되고, 판매하는 신발이 늘어나거나 단종되면 신발 매장이 아닌 신발 공장에서 그 변화를 처리할 수 있다.

위에서 봤던 신반 매장은 점점 성장하여 다른 나라로 진출하기 시작했다. 일본과 프랑스에도 진출을 하여 매장을 지었다고 하자.

```java
   JapanShoesStore jpStore= new JapanShoeStore (new JapanShoesFactory());
   jpStore.order("blackShoes");
    
   FranceShoesStore  frStore = new FranceShoesStore (new FranceShoesFactory());
   frStore.order("blackShoes");
```

그런데 이 해외 매장들이 본사에서 준 가이드라인 그대로 똑같이 만들지 않고, 현지 상황에 맞춰 일본에서는 약간 굽을 높게 만들고 프랑스에서는 신발 옆에 패턴을 넣기 시작했다. 
뿐 만 아니라 포장까지도 자기 마음대로 하였다.

그래서 본사는 매장과 신발 생산 과정 전체를 묶어주는 아래와 같은 프레임 워크를 만들어 모든 매장에서 이를 따르게 하였다.

> ShoesStore.java

```java
   public abstract class ShoesStore {
 
    public ShoesStore orderShoes(String name) {
        Shoes shoes = makeShoes(name);
        shoes.prepare();
        shoes.packing();
 
        return shoes;
    }
    abstract Shoes makeShoes(String name);
}
```
ShoesStore 추상 클래스를 선언하면, 달라지는 부분은 추상메소드인 신발 제작 뿐이다. 

각 현지 상황메 맞춰 makeShoes 메소드를 오버라이드하여 일본에서는 약간 굽을 높게 만들고, 프랑스에서는 패턴을 넣어 신발을 제작하면 된다.

그대신 제작, 준비, 포장하는 공정은 ShoesStore를 상속하는 전 세계 모든 매장들에서 똑같은 시스템이 적용 될 수 있다.

> JapanShoesStore.java

```java
class JapanShoesStore extends ShoesStore {
 
    @Override
    Shoes makeShoes(String name) {
        if (name.equals("blackShoes")) return new JPStyleBlackShoes();  
        else if (name.equals("brownShoes")) return new JPStyleBrownShoes();
        else if (name.equals("redShoes")) return new JPStyleRedShoes();
        else return null;
   }
}
```

> FranceShoesStore.java

```java
class FranceShoesStore extends ShoesStore {
 
    @Override
    Shoes makeShoes(String name) {
        if (name.equals("blackShoes")) return new FRStyleBlackShoes();  
        else if (name.equals("brownShoes")) return new FRStyleBrownShoes();
        else if (name.equals("redShoes")) return new FRStyleRedShoes();
        else return null;
   }
}
```

여기서 가장 중요한 점은 하위 클래스에서 메소드를 오버라이딩 하였기 때문에, 슈퍼클래스에 있는 orderShoes 메소드에서는 어떤 신발이 만들어 지는지 전혀 모르고 있다는 것이다. 
동적 바인딩되는 그 메소드에서 주는 신발을 받아서 준비하고 포장할 뿐 이다.

#### 신발을 주문받고 생산하는 생산자 클래스, 생산자 클래스에서 생산되는 제품 클래스

팩토리 메소드 패턴의 클래스들은 크게 생산자 클래스와 제품 클래스로 구분할 수 있다.

생산자 클래스인 ShoesStore 클래스에서 사용될 제품 클래스 Shoes 클래스는 다음과 같이 작성될 수 있다.

> Shoes.java

```java
abstract class Shoes {
 
    String name;
    String bottom;
    String leather;
    boolean hasPattern;
 
    void prepare() {
        System.out.println("주문하신 신발을 준비중입니다.");
    }
 
    void packing() {
        System.out.println("준비 완료된 신발을 포장중입니다.");
    }
 
    public String getName() {
        return name;
    }
}
```

> JPStyleBlackShoes.java

```java
class JPStyleBlackShoes extends Shoes {
 
    public JPStyleBlackShoes() { 
        name = "일본 스타일의 검은 구두";
        bottom = "굽이 높은 밑창";
        leather = "스웨이드";
        hasPattern = false;
    }
}

```

> FRStyleBlackShoes.java

```java
class FRStyleBlackShoes extends Shoes {
 
    public FRStyleBlackShoes() { 
        name = "프랑스 스타일의 검은 구두";
        bottom = "일반 굽높이 밑창";
        leather = "스웨이드";
        hasPattern = true;
    }
 
}
```

추상 클래스로 Shoes 클래스를 설계하고, JPStyleBlackShoes, FRStyleBlackShoes에서 멤버변수를 초기화하며 구현해주었다.

> __추상 메소드가 없는 추상 클래스가 여기서 등장한다.__
> 
> Shoes 클래스는 추상 메소드가 존재하지 않지만 추상 클래스로 선언되었기 때문에 new Shoes();로 직접 객체 생성은 불가능하고,
> 
> Shoes 클래스를 상속받는 클래스에서 Shoes를 참조변수로하여 객체 생성을 할 수 있다.
> 
> 추상 메소드 존재 O -> 무조건 클래스도 추상 클래스로 선언
> 
> 추상 메소드 존재 X -> 현재 클래스로 다이렉트 객체 생성을 막고 싶을 때, 추상 클래스로 선언

여기까지 UML에 설계된 클래스들의 구현이 마무리 되었다. 
이제 메인 클래스에서 위에서 설계한 팩토리 메소드 패턴이 어떻게 사용되는지 살펴 보자.

> Main.java

```java
public class Main {
 
    public static void main(String[] args) {
        
        // 일본과 프랑스에 현지 트렌드에 맞춰 매장을 열었음
        ShoesStore jpStore = new JapanShoesStore();
        ShoesStore frStore = new FranceShoesStore();
      
        // 일본 매장에서 검은 신발 주문
        Shoes shoes = jpStore.orderShoes("blackShoes");
        System.out.println("일본 매장에서 주문한 검은 신발 : " + shoes.getName());
        
        System.out.println();
        
        // 프랑스 매장에서 검은 신발 주문
        shoes = frStore.orderShoes("blackShoes");
        System.out.println("프랑스 매장에서 주문한 검은 신발  : " + shoes.getName());
 
    }
 
}
```
위 코드를 실행해보면 아래와 같이 출력될 것이다.


```
> 주문하신 신발을 준비중입니다.
> 준비 완료된 신발을 포장중입니다.
> 일본 매장에서 주문한 검은 신발 : 일본 스타일의 검은 구두
> 
> 주문하신 신발을 준비중입니다.
> 준비 완료된 신발을 포장중입니다.
> 프랑스 매장에서 주문한 검은 신발 : 프랑스 스타일의 검은 구두
```

위 메인 메소드의 프로세스는 아래와 같다. 참고로 일본 매장과 프랑스 매장에서의 프로세스는 동일하니 공통적인 프로세스로 묶이게 된다.

1. 현지 신발 매장이 문을 열었음. (ShoesStore를 참조변수로 하는 현지 ShoesStore 객체 생성)
2. 매장에 신발 종류를 통해 신발을 주문함. (ShoesStore의 orderShoes메소드)
3. ShoesStore의 orderShoes 내부에서 생성된 객체에 맞게 동적바인딩되어 오버라이딩된 makeShoes 메소드가 실행됨
4. 오버라이딩된 makeShoes 메소드에서 주문에 맞는 신발객체를 호출된 orderShoes메소드로 리턴함
5. prepare(), packing() 메소드가 실행됨
6. make, prepare, packing이 완료된 Shoes 객체를 리턴함
7. 주문한 신발이 어떤 객체인지 출력하여 확인

__결론적으로, 팩토리 메소드 패턴은 객체를 만들어내는 부분을 자식 클래스에 위임하는 패턴이다.__

new 키워드를 호출하는 부분을 서브 클래스에 위임하였기 때문에, 상위 클래스인 ShoesStore 클래스 내부에는 new 라는 키워드가 존재하지 않는다. 

즉, 상위 클래스가 아닌 하위 클래스에서 어떤 형태의 클래스를 만들지 결정하게 하도록 하는 것이다. 

하위 클래스에서 추상 메소드인 makeShoes메소드를 오버라이딩 하였기 때문에, 상위 클래스에 있는 orderShoes 메소드에서는 어떤 신발이 만들어 지는지 전혀 모르고 있다.

동적 바인딩된 그 메소드에서 주는 신발을 받아서 준비하고 포장해서 내놓을 뿐 이다.

이상 안드로이드 개발을 하며 궁금했던 팩토리 패턴에 대한 정리였다. 