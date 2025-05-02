# Item 1. 생성자 대신 정적 팩토리 메서드를 고려하라

## 정적 팩토리 메서드란?
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

## 정적 팩토리 매서드의 장점
1. 이름을 가질 수 있어 가독성이 좋다.
2. 호출힐 대마다 인스턴스를 새로 생성하지 않아도 된다.
3. 반환 타입의 하위 타입 객체를 반환할 수 있다
4. 입력 매개변수에 따라 다른 클래스의 객체를 반환할 수 있다
5. 생성자보다 코드를 읽고 쓰기 쉬운 유연한 API 설계가 가능하다