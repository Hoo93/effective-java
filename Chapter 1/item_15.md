# 📘 Item 15. 클래스와 멤버의 접근권한을 최소화하라

## ✅ 0. 캡슐화 / 정보은닉의 필요성
* 잘 설계된 컴포넌트는 내부 구현을 외부에 노출하지 않는다.
* 구현 세부사항을 감추면 변경에 유연해지고, 모듈 간 의존성을 줄여 유지보수성이 향상된다.
* 정보 은닉(Information Hiding) 을 통해 코드의 재사용성과 테스트 용이성이 증가한다.
* 접근 권한을 최소화하면 오류 가능성도 줄어든다.
* 반대로 public 으로 한 번 열어 놓으면 평생 해당 부분을 유지보수해야 한다.

### 원칙: 프로그램 요소(클래스, 인터페이스, 필드, 메서드 등)의 접근 권한은 가능한 한 좁게 설정해야 한다.

## 🏗️ 1. 톱레벨 클래스(Top-Level Class)

### 📌 정의
* 소스 파일에서 정의된 가장 바깥쪽 클래스
* 다른 클래스에 중첩되지 않은 클래스

### 🔓 접근자 종류
* public: 다른 패키지에서도 사용 가능
* 패키지 전용(package-private): 접근 제한자를 생략한 경우, 같은 패키지 안에서만 접근 가능
* 권장: 가능한 한 package-private 으로 선언하고, 외부에서 꼭 필요한 경우에만 public 으로 공개하라.

### 예시
```java
public class Zoo {

    // 정적 중첩 클래스 (static nested class)
    public static class Keeper {
        public void clean() {
            System.out.println("Keeper is cleaning the cage");
        }
    }

    // 비정적 내부 클래스 (non-static inner class)
    public class Animal {
        public void sound() {
            System.out.println("Zoo animal sound");
        }
    }
}
```
* Zoo는 톱레벨 클래스이고,
* 그 안에 선언된 Keeper와 Animal은 중첩 클래스 또는 내부 클래스.
* 중첩 클래스는 외부 클래스(Zoo)에 종속적임.

## 2. 🔐 멤버(필드, 메서드, 내부 클래스)의 접근 권한

| 접근 제어자                    | 설명 |
|---------------------------|----|
| private                   | 	해당 클래스 내부에서만 접근 가능 | 
| package-private (default) | 	같은 패키지 내에서 접근 가능 | 
| protected                 | 	같은 패키지 + 하위 클래스에서 접근 가능 | 
| public                    | 	어디서든 접근 가능 | 

## 3. 필드의 접근자
* 필드는 가능한 public으로 선언하지 말 것
→ 변경 가능성이 외부에 노출되고 캡슐화가 깨짐
* 특히 불변 클래스에서는 public final 필드가 허용되기도 하나, 이는 상수(static final) 수준에만 적합

* 예외: 상수용 필드는 public static final로 선언 가능 (예: Integer.MAX_VALUE)

## 4. 🚫 가능한 한 private 으로 숨길 것
* 접근 범위가 넓을수록:
* 변경의 여지가 줄어들고,
* 외부 의존성이 늘어나며,
* 테스트와 디버깅이 어려워진다.
* 따라서 기본은 private, 꼭 필요한 경우에만 점진적으로 공개 범위 확대

* 전략: 처음에는 private으로 만들고, 나중에 필요시 package-private, protected, public으로 확장

## 5. 📌 정리
* 모든 클래스와 멤버는 가장 좁은 접근 범위를 부여해야 한다.
* 톱레벨 클래스는 public 또는 package-private만 가능
* 내부 구현은 철저히 숨기고, API로서 꼭 필요한 부분만 공개
* 상수 이외의 public static final 필드는 최대한 피할 것
* 캡슐화를 지키면 유지보수성과 확장성이 높아진다