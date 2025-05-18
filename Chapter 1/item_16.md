# 📘 Item 16. public 클래스에서는 public 필드가 아닌 접근자 메서드를 사용하라

### ✅ 1. 핵심 개념

- **캡슐화(encapsulation)**는 객체지향 프로그래밍의 기본 원칙 중 하나로, 필드에 직접 접근하는 것을 막고, 대신 메서드를 통해 접근하게 해야 한다.
- `public` 필드를 가지는 클래스는 API를 고정시켜버리는 효과가 있어 향후 확장 및 변경이 어렵다.
- 필드를 `private`으로 숨기고 `getter`, `setter` 메서드를 제공하는 방식은 변경에 유연하게 대응할 수 있다.

---

### ✅ 2. 잘못된 예시

```java
// 잘못된 설계 - public 필드 노출
public class Point {
    public double x;
    public double y;
}
```


### ✅ 3. 올바른 예시

```java
// 올바른 설계 - 필드 은닉과 접근자 메서드 제공
public class Point {
    private double x;
    private double y;

    public double getX() { return x; }
    public double getY() { return y; }
    public void setX(double x) { this.x = x; }
    public void setY(double y) { this.y = y; }
}
```

### ✅ 4. 불변 클래스의 예외

```java
public final class Time {
    public final int hour;
    public final int minute;

    public Time(int hour, int minute) {
        this.hour = hour;
        this.minute = minute;
    }
}
```

### ✅ 5. 정리
•	클래스의 필드는 무조건 감추고 메서드로 제공하라.
•	캡슐화를 지키면 유연성과 안정성이 높아진다.
•	단, 불변 클래스이며 필드 자체가 의미 있는 경우에는 public final 필드를 사용할 수 있다.