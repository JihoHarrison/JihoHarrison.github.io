---
layout: post
title: "[CS] Builder 패턴 (Design Pattern)"
date: 2021-11-16 09:34:10 +0700
categories: [CS]
---

# 0️⃣ 프롤로그
 * 인턴십을 진행하기 전부터 디자인 패턴에 대한 이해가 필요하다는 것을 절실히 느끼고 공부하다가 이번에 프로젝트 내용들을 블로그에 정리 하면서 긴 포스팅을 남겨볼까 한다.
 특히 안드로이드 개발을 진행하면서 

## 1️⃣ Builder Pattern이란?

복잡한 객체에 대해 `생성(contruction)과 표현(representation)을 분리`함으로써 **똑같은 생성 과정으로 서로 다른 객체 표현**을 가능하게 하는 생성 디자인 패턴

<br>

## 2️⃣ Builder 패턴을 사용해야하는 이유

1. `Immutability` - 객체의 불변성을 유지할 수 있음
2. `Named Parameter with Chaining` - 체이닝을 통한 명명된 매개변수 사용으로 가독성 증진
3. `Design Flexibility` - 필수적인 변수와 선택적인 변수를 각각 생성 가능
4. `Easy Maintenance` - 새로운 멤버가 추가되더라도 기존의 객체 생성 코드를 수정할 필요 없음
5. `Avoid RuntimeException` - 객체 생성 과정에서 유효성 검사를 통해 논리적인 에러를 막을 수 있음

> 💡 불변적인 객체로 구현해야하는 이유  
> - 불변성(`Immutability`)이란? 객체가 초기에 한번 생성된 이후에는 절대 상태를 바꾸지 않는 것을 말한다. 객체 생성시에 모든 정보가 주어지고 객체의 생애 주기 동안에는 상태가 바뀌지 않는 것이 특징이다.
> - 사용이 쉽다
> - Thread Safe 하다. 동기화할 필요가 없다.
> - 자유롭게 공유할 수 있다.

## 3️⃣ Builder 패턴의 한계

코드를 2배정도 많이 사용하게 된다. 따라서 설정해야 할 매개변수가 적을 경우에는 일반 생성자를 통한 생성이 더욱 편할 수도 있다.

<br>

## 4️⃣ 구현

### Builder 패턴 적용 전

일반적으로는 생성자(Constructor)를 통해 객체를 생성할 것이다. 생성자를 사용할 경우 멤버를 선택적으로 생성하기 어렵다.

#### 1. 생성자를 사용한 생성 - 자바빈즈 패턴(JavaBeans Pattern)

매개변수가 없는 기본 생성자를 통해 객체를 생성한 뒤, **Setter 메서드**를 통해 멤버를 설정하는 방식이다.

[클래스 정의]

```java
public User() {

}

public User(String firstName, String lastName, int age, String phone, String address) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = age;
    this.phone = phone;
    this.address = address;
}
```

[객체 생성]

```java
User user1 = new User("Jiho", "Kevin", 6, "010-4640-9435", "서울시 은평구 우리집~~");
User user2 = new User("Tony", "Choi", 5, null, null);

User user3 = new User();
user3.setFirstName("Jin");
user3.setLastName("Lee");
```

#### 2. 생성자를 사용한 생성 방법

필수 매개변수만을 가진 생성자를 만들고 선택 매개변수를 하나씩 추가한 생성자를 만든다. 수많은 **생성자 오버로딩**을 통해 원하는 형태의 객체를 생성하도록 하는 방식이다.

[객체 생성]

```java
public User (String firstName, String lastName, int age) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = age;
    this.phone = null;
    this.address = null;
}

public User (String firstName, String lastName, int phone) {
    this.firstName = firstName;
    this.lastName = lastName;
    this.age = null;
    this.phone = phone;
    this.address = null;
}
```

> 위 예제와 같이 생성자를 통한 객체 생성 시 필수 매개변수와 선택 매개변수를 구분하여 구현하기가 어렵다. 특히 매개변수가 많아진다면 일일히 setter를 부르는 일도, 매개변수 자리를 세주는 것도 일이다. 생성자를 경우의 수 별로 구현하는 것은 더욱 끔찍할 것이다.

### Builder 패턴 적용 후

[클래스 정의]

```java
// 클래스를 Final로 설정하여 확장이 불가능하며 불변성이 유지됨
public final class User 
{
    // 불변성을 유지하기 위해 private final로 설정
    private final String firstName;     // 필수 변수
    private final String lastName;      // 필수 변수
    private final int age;              // 선택 변수
    private final String phone;         // 선택 변수
    private final String address;       // 선택 변수

    private User(UserBuilder builder) {
        this.firstName = builder.firstName;
        this.lastName = builder.lastName;
        this.age = builder.age;
        this.phone = builder.phone;
        this.address = builder.address;
    }
 
    // Setter를 구현하지 않음으로써 불변성 유지
    public String getFirstName() {
        return firstName;
    }
    public String getLastName() {
        return lastName;
    }
    public int getAge() {
        return age;
    }
    public String getPhone() {
        return phone;
    }
    public String getAddress() {
        return address;
    }
 
    @Override
    public String toString() {
        return "User: "+this.firstName+", "+this.lastName+", "+this.age+", "+this.phone+", "+this.address;
    }
 
    // 객체 내부에 Builder 정의(중첩 클래스)
    public static class UserBuilder 
    {
        // 필수적인 변수만 final로 설정
        private final String firstName;     // 필수 변수
        private final String lastName;      // 필수 변수
        private int age;                    // 선택 변수
        private String phone;               // 선택 변수
        private String address;             // 선택 변수

        // Builder 생성자 매개변수는 필수 변수만을 포함
        public UserBuilder(String firstName, String lastName) {
            this.firstName = firstName;
            this.lastName = lastName;
        }
        // 선택적인 변수는 추가적인 메서드를 구현하여 생성
        public UserBuilder age(int age) {
            this.age = age;
            return this;
        }
        public UserBuilder phone(String phone) {
            this.phone = phone;
            return this;
        }
        public UserBuilder address(String address) {
            this.address = address;
            return this;
        }
        // Builder로 생성된 객체 반환
        public User build() {
            User user =  new User(this);
            if (!validateUserName(user)) throw new NoNameException();
            if (!validateUserAge(user)) throw new InvalidAgeException();
            return user;
        }
        private boolean validateUserName(User user) {
            if (user.firstName==null || user.lastName==null) {
                if (user.age!=null || user.phone!=null || user.address!=null) return false;
            }
            return true;
        }
        private boolean validateUserAge(User user) {
            if (user.age<0) return false;
            return true;
        }
    }
}
```

[객체 생성]

```java
User user1 = new User.UserBuilder("jiho", "Kevin")
                     .age(26)
                     .phone("010-4640-9435")
                     .address("서울시 은평구 우리집 ~~")
                     .build();

User user2 = new User.UserBuilder("Tony", "Choi")
                     .age(25)
                     // no phone
                     // no address
                     .build();

User user3 = new User.UserBuilder("Jin", "Lee")
                     // no age
                     // no phone
                     // no address
                     .build();
```

> 빌더 패턴을 이용해 위와 같이 하나의 생성자만으로 여러 상태의 객체를 생성할 수 있게되었다.

- 멤버를 `final`로 설정하여 불변성을 유지할 수 있다.
- 선택적인 변수의 경우 `null`로 설정할 필요가 없다. 또한 생성자 오버로딩을 하지 않아 선택적인 변수를 가진 객체도 동일한 방법으로 생성할 수 있다.
- 각 변수의 이름에 해당하는 메서드를 chaining 방식으로 접근하여 초기화할 수 있다. 따라서 **생성자의 매개변수 순서를 기억할 필요가 없고, 생성 과정에서의 가독성이 훨씬 좋아진다**.
- 만약 **새로운 멤버 변수가 추가되더라도 기존 객체 생성 코드를 수정하지 않아도 된다**. 새롭게 추가된 멤버 변수도 선택적인 매개변수와 동일하게 처리하기 때문이다.
- 추가적으로 **빌더 클래스 내부에 유효성 검사 메서드를 추가**한다면 멤버 생성 과정에서의 논리적인 에러를 사전에 차단할 수 있다.

<hr>

#### References

[howtodoinjava Builder Pattern](https://howtodoinjava.com/design-patterns/creational/builder-pattern-in-java/)  
[dzone Immutability and Builder Pattern](https://dzone.com/articles/immutability-with-builder-design-pattern)  
[StackExchange Why do we need a builder class](https://softwareengineering.stackexchange.com/questions/380397/why-do-we-need-a-builder-class-when-implementing-a-builder-pattern)