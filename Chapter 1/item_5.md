# 📘 Item 5. 자원을 직접 명시하지 말고 의존 객체 주입을 사용하라

---

## 📑 목차
1. [내용 정리](#내용-정리)
2. [예시 Java Code](#예시-java-code)

---

## 1. 내용 정리

### ✅ 핵심 개념
클래스가 내부에서 사용할 **자원을 직접 생성하거나 획득하지 말고**,  
**외부에서 필요한 자원을 주입받도록 설계**하라. → 이를 **의존 객체 주입(Dependency Injection)**이라고 한다.

---

### ✅ 왜 의존성 주입이 중요한가?

| 직접 자원 생성 방식의 문제점 | 의존 객체 주입의 장점 |
|----------------------------|------------------------|
| 특정 구현체에 강하게 결합됨 | 느슨한 결합 (Low Coupling) |
| 테스트 시 Mock 대체 불가 | 테스트 가능성 향상 (Mock 주입 가능) |
| 변경 시 코드 수정 필요 | 구현체 교체 유연 |
| 재사용성 낮음 | 재사용성 높음 |

---

### ✅ 주입 방식 종류

| 방식 | 특징 |
|------|------|
| 생성자 주입 | 불변 객체, 필수 의존성에 적합 |
| 세터 주입 | 선택적 의존성 또는 초기화 이후 변경 가능 |
| 정적 팩토리 주입 | 유연하지만 테스트 어려움 (팩토리 내부에 자원 결정 로직 존재) |

---

## 2. 예시 Java Code

### ❌ 잘못된 예 - 직접 자원 생성

```java
public class MessageService {
    private final MessageSender sender = new KakaoMessageSender(); // 직접 생성

    public void send(String to, String content) {
        sender.send(to, content);
    }
}
```
* KakaoMessageSender에 강하게 결합됨
* 테스트용 Mock 대체 불가능
* 확장성/유지보수성 낮음

### ✅ 개선된 예

```java
// 💡 인터페이스
public interface MessageSender {
   void send(String to, String content);
}

// 💡 인터페이스를 통한 생성자 주입
public class MessageService {
    private final MessageSender sender;
    
    public MessageService(MessageSender sender) {
        this.sender = sender;
    }

    public void send(String to, String content) {
        sender.send(to, content);
    }
}

// 💡 구현체
public class KakaoMessageSender implements MessageSender {
   public void send(String to, String content) {
      // 실제 카카오 메시지 전송 로직
   }
}

// 💡 테스트를 위한 Mock 구현체
public class MockMessageSender implements MessageSender {
   public String lastTo;
   public String lastContent;

   @Override
   public void send(String to, String content) {
      this.lastTo = to;
      this.lastContent = content;
   }
}
```

### ✅ Mock 을 이용한 테스트 코드
```java
@Test
void testSend() {
    // Given
    MockMessageSender mock = new MockMessageSender();
    // 💡 Mock MessageSender 주입
    MessageService service = new MessageService(mock);

    // When
    service.send("010-1234-5678", "출석 체크 알림입니다.");

    // Then
    assertEquals("010-1234-5678", mock.lastTo);
    assertEquals("출석 체크 알림입니다.", mock.lastContent);
}
```
