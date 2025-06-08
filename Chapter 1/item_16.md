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
import java.time.LocalDate;

// 올바른 설계 - 필드 은닉과 접근자 메서드 제공
public class Student {
    private LocalDate enrollmentDate;
    private Long fee;
    private String name;
    
    public Student(LocalDate enrollmentDate, Long fee, String name) {
        this.enrollmentDate = enrollmentDate;
        this.fee = fee;
        this.name = name;
    }
    
    public LocalDate getEnrollmentDate() {
        return enrollmentDate;
    }
    public Long getFee() {
        return fee;
    }
    public String getName() {
        return name;
    }
    public void setFee(Long fee) {
        this.fee = fee;
    }
    public void setName(String name) {
        this.name = name;
    }
}
```

### ✅ 4. getter, setter 를 사용하면 public 과 같은 것 아닌가 ?
- 현재 Student 의 상태를 봤을 때는 public 필드와 다를 바가 없어 보인다.
- 하지만 요구사항 추가로 등록일 이후 1년이 지나면 수업료를 10_000원 할인 해준다고 하자.
- 이 경우 getFee() 메서드에 로직을 추가하면 된다.
```java
public Long getFee() {
    if (enrollmentDate.plusYears(1).isBefore(LocalDate.now())) {
        return fee - 10_000;
    }
    return fee;
}
```
- 만약 public 필드였다면, fee 를 조회하는 모든 로직에 이 로직을 추가해야 한다.
- 이렇게 되면 코드가 중복되고, 유지보수가 어려워진다.
- 따라서, 처음에는 public과 같아 보인다고 하더라도, 필드를 감추고 메서드를 통해 접근하는 것이 캡슐화를 지키는 올바른 방법이다.

### ✅ 5. 정리
•	클래스의 필드는 무조건 감추고 메서드로 제공하라.
•	캡슐화를 지키면 유연성과 안정성이 높아진다.
•	단, 불변 클래스이며 필드 자체가 의미 있는 경우에는 public final 필드를 사용할 수 있다.