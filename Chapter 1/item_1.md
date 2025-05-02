# Item 1. 생성자 대신 정적 팩토리 메서드를 고려하라

## 📑 목차
1. [0. 정적 팩토리 메서드란?](#0-정적-팩토리-메서드란)
2. [1. 정적 팩토리 메서드의 장점](#1-정적-팩토리-매서드의-장점)
    - [1-1. 이름을 가질 수 있어 가독성이 좋다](#1-1-이름을-가질-수-있어-가독성이-좋다)
    - [1-2. 호출할 때마다 인스턴스를 새로 생성하지 않아도 된다](#1-2-호출할-때마다-인스턴스를-새로-생성하지-않아도-된다)
    - [1-3. 반환 타입의 하위 타입 객체를 반환할 수 있다](#1-3-반환-타입의-하위-타입-객체를-반환할-수-있다)
    - [1-4. 입력 매개변수에 따라 다른 클래스의 객체를 반환할 수 있다](#1-4-입력-매개변수에-따라-다른-클래스의-객체를-반환할-수-있다)
    - [1-5. 작성 시점에 반환할 객체 클래스가 없어도 된다](#1-5-정적-팩토리-메서드는-작성-시점에-반환할-객체의-클래스가-존재하지-않아도-된다)
3. [2. 정적 팩토리 메서드의 단점](#2-정적-팩토리-메서드의-단점)
4. [3. 정적 팩토리 메서드 이름 패턴 정리](#-정적-팩토리-메서드-이름-패턴-정리)


## 0. 정적 팩토리 메서드란?
* static factory method
* new 키워드로 생성자를 직접 호출하지 않고, 클래스 내의 정의된 static 메소드를 통해 객체를 반환하는 방s식

```java
public class Person {
    private String name;

    private Person(String name) {
        this.name = name;
    }

    public static Person of(String name) {
        return new Person(name);
    }
}
```

## 1. 정적 팩토리 매서드의 장점
### 1-1. 이름을 가질 수 있어 가독성이 좋다.
```java
public class Person {
    private String name;
    private Genger gender;

    private Person(String name, Gender gender) {
        this.name = name;
        this.gender = gender;
    }

    public static Person createMale(String name) {
        return new Person(name, Gender.MALE);
    }

    public static Person createFemale(String name) {
        return new Person(name, Gender.FEMALE);
    }
}

```
### 1-2. 호출할 때마다 인스턴스를 새로 생성하지 않아도 된다.
* 대표적인 예로 싱글턴 패턴이 존재한다.
```java
public class Settings {
    private static final Settings INSTANCE = new Settings();

    private Settings() {}

    public static Settings getInstance() {
        return INSTANCE;
    }
}

// 사용 예
Settings s1 = Settings.getInstance();
Settings s2 = Settings.getInstance(); // s1 == s2
```
### 1-3. 반환 타입의 하위 타입 객체를 반환할 수 있다
* 정적 팩토리 메서드는 반환 타입으로 인터페이스나 상위 타입을 지정하고, **내부적으로는 하위 타입(구현 클래스)**을 반환할 수 있다.
* 클라이언트 코드가 구현체에 의존하지 않게 된다.
* 내부 구현을 바꿔도 API는 그대료 유진된다.
* 대표적 예시로 Collections 가 있다. 45개의 클래스를 정잭 팩토리 메소드를 제공한다.
  * 이를 통해 클라이언트는 인터페이스만 알면 되고 실제 구현체는 몰라도 된다.
  * 이로 인해 Java 버전이 올라가면서 더 좋은 구현체로 교체해도 기존 코드들에는 아무런 여향이 없다.
```java
public interface Animal {
    void sound();
}

class Cat implements Animal {
    public void sound() {
        System.out.println("Meow");
    }
}

class Dog implements Animal {
    public void sound() {
        System.out.println("Woof");
    }
}

public class AnimalFactory {
    public static Animal getAnimal(String type) {
        if (type.equals("cat")) return new Cat();
        else return new Dog();
    }
}

// 사용 예
Animal a = AnimalFactory.getAnimal("cat"); // 실제로는 Cat 인스턴스지만 Animal 타입 반환
a.sound(); // Meow
```
### 1-4. 입력 매개변수에 따라 다른 클래스의 객체를 반환할 수 있다
* EnumSet 클래스는 원소가 64개 이하이면 long 객체 한 개로 관리하는 RegularEnumSet 클래스 객체를 반환하고, 65개 이상이면 JumboEnumSet을 반환한다.
### 1-5. 정적 팩토리 메서드는 작성 시점에 반환할 객체의 클래스가 존재하지 않아도 된다.
* 정적 팩토리 메서드는 API 설계와 구현을 분리할 수 있다
  1. 1단계: 인터페이스만 정의
  2. 2단계: 외부에서 구현체를 나중에 제공
  3. 3단계: 나중에 구현체를 연결
* JDBC가 대표적인 예시
  * 인터페이스 : Connection (java.sql) — API 사용자에 노출
  * 팩토리 메서드 : DriverManager.getConnection(...)
  * 구현체 : com.mysql.cj.jdbc.ConnectionImpl, org.postgresql.jdbc.PgConnection 등 — 나중에 로드됨

## 2. 정적 팩토리 메서드의 단점
1. 상속에 불리하다
    * Java 에서 상속을 위해서 public 또는 protected 생성자가 필요함
    * 이는 단점이라기 보다도 객체 합성을 유도하기도 함
    * 불변성을 보장하기도 하기 떄문에 꼭 단점이라고 보기도 어려움
2. 프로그래머가 찾기 힘들다

## 📘 정적 팩토리 메서드 이름 패턴 정리

| 이름 | 의미 / 용도 | 사용 예시 |
|------|--------------|------------|
| `from` | 하나의 값을 다른 타입으로 변환할 때 사용 | `Date.from(Instant instant)` → `Instant → Date` |
| `of` | 여러 매개변수를 받아 객체를 생성할 때 사용 (간결함 중시) | `Set.of("a", "b")`, `LocalDate.of(2025, 5, 2)` |
| `valueOf` | `from`과 유사하지만, 더 일반적인 변환 표현. 캐싱과 병행되는 경우도 많음 | `Integer.valueOf("123")` → `String → Integer` |
| `getInstance` / `instance` | 요청에 따라 **매번 새 객체를 만들 필요 없는 경우**, 싱글턴이나 캐시된 인스턴스를 반환할 때 사용 | `Calendar.getInstance()` |
| `create` / `newInstance` | **항상 새로운 객체**를 생성할 때 사용. 내부에서 매번 `new` 수행 | `ObjectInputStream.newInstance()` |
| `getType` | **다른 클래스에 정의된 정적 팩토리 메서드**로, 반환 타입을 드러낼 때 사용 | `FileStore fs = Files.getFileStore(path)` |
| `newType` | `getType`과 유사하지만, 새로운 객체를 생성함을 더 명확히 표현 | `BufferedReader reader = Files.newBufferedReader(path)` |
| `type` | `from`, `of`, `valueOf`, `newType`, `getType`처럼 다양한 의미를 내포하되, 간결하게 타입을 표현하고 싶을 때 사용 | `List list = Collections.list(enumeration)` |