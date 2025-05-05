# 📘 Item 6. 불필요한 객체 생성을 피하라

## 1. 내용 정리

### ✅ 핵심 개념
> 객체 생성 비용이 비싸거나, 객체를 자주 반복 생성할 가능성이 있는 경우에는  
> **불필요한 객체 생성을 피하고 재사용을 고려하라.**

---

### ✅ 대표적인 예

| 사례 | 잘못된 예 | 개선된 예 |
|------|------------|------------|
| 불변 객체(String 등) | `new String("hello")` | `"hello"` (문자열 리터럴) |
| 오토박싱 | `Long sum = 0L;` (박싱된 타입 사용) | `long sum = 0L;` (기본 타입 사용) |
| 값이 변하지 않는 객체 | `new Boolean(true)` | `Boolean.TRUE` |
| 정규식 | 매번 `Pattern.compile()` | 정적 상수로 캐싱 |
| 반복되는 계산 | 매 루프마다 동일한 계산 | 한 번만 계산하고 재사용 |

#### ❌ 잘못된 예 1: 불변 객체를 new로 생성
```java
// ❌ 새로운 객체 생성 (불필요)
String a = new String("hello");

// ✅ 개선1) 문자열 리터럴 사용 → JVM 내 String pool 재사용
String b = "hello";

// ✅ 개선2) 정적 팩토리 메소드로 생성한다면 코드 실행 중에 변경되지 않는 경우에는 객체를 재사용한다.
String c = String.valueOf("hello");
```

#### ❌ 잘못된 예 2: 오토박싱을 무심코 사용
```java
// ❌ 박싱된 Long 객체를 사용 → 루프마다 불필요한 객체 생성 (성능 저하)
private static sum() {
    Long sum = 0L;
    // ❌ 반복문을 순회할 때마다 long 을 Long 으로 오토박싱한다... 1_000_000 번의 불필요한 객체를 생성하게 된다.
    for (long i = 0; i < 1_000_000; i++) {
        sum += i;
    }
    
}

// ✅ 개선) 기본 타입 사용 → 오토박싱/언박싱 제거
private static sum() {
    long sum = 0L;
    for (long i = 0; i < 1_000_000; i++) {
        sum += i;
    }
}
```

#### ❌ 잘못된 예 3: 생성 비용이 비싼 객체는 재사용하기
```java
// ❌ 매번 Pattern 객체 생성 → 성능 저하 (컴파일 비용 반복 발생)
public class RomanNumeral {
    public static boolean isRomanNumeral(String s) {
        return s.matches("^(?i)M{0,4}(CM|CD|D?C{0,3})"
                       + "(XC|XL|L?X{0,3})(IX|IV|V?I{0,3})$");
    }
}

// ✅ 개선: 정규식 Pattern을 정적 상수로 캐싱하여 재사용
public class RomanNumeral {
    // 💡 Pattern.compile()은 비용이 크므로 static final로 캐싱
    private static final Pattern ROMAN = Pattern.compile(
            "^(?i)M{0,4}(CM|CD|D?C{0,3})"
                    + "(XC|XL|L?X{0,3})(IX|IV|V?I{0,3})$"
    );

    public static boolean isRomanNumeral(String s) {
        return ROMAN.matcher(s).matches();
    }
}
```
* 생성 비용 자체가 비싼 객체들이 있다.
* 잘못된 예시의 코드는 정규식 패턴을 실행할 때마다 생성하기 때문에 비효율적이고 성능적으로 느리다.
* 자주 사용해야하는 코드는 클래스 생성시 직접 생성하고 캐싱해 두자
  * 한번도 호출되지 않아서 비효율이 발생할 수 있기 때문에 lazy loading 하는 것도 방법이나 코드가 복잡하고 성능개선이 크지 않기 때문에 비추한다.

---

### ✅ 왜 중요한가?

- **객체 생성 비용**은 클래스에 따라 매우 다르다. (예: `String`, `Pattern`, `Calendar`)
- 재사용 가능한 객체를 매번 새로 생성하면 **메모리 낭비 및 GC 부담**이 커진다.
- 특히 루프나 요청마다 반복 생성되면 **성능 저하**가 심해진다.