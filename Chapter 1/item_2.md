# Item 2. 생성자에 매개변수가 많다면 빌더를 고려하라

## 📑 목차
1. [0. 정적 팩토리 메서드란?](#0-정적-팩토리-메서드란)


## 1. 빌더패턴을 사용하면 좋을 때
1. 매개변수가 4개 이상일 때
2. 매개변수 중에 optional 한 값일 많을 때

## 2. 빌더패턴의 장점
1. 가독성이 좋다
2. 불변객체로 만들 수 있다. ( setter 를 열어놓음으로써 객체가 변경되는 일을 방지할 수 있다. )
3. 계층형 클래스와 잘 맞는다.

## 3. 빌더 패턴의 예시
### 3-1. 기본 빌더 패턴 
```java
public class AttendanceBook {

    // ✅ 필수값
    private final String bookName;
    private final String teacherName;

    // ✅ 선택값
    private final int maxStudents;
    private final boolean allowLateEntry;
    private final String classroom;
    private final String note;

    private AttendanceBook(Builder builder) {
        this.bookName = builder.bookName;
        this.teacherName = builder.teacherName;
        this.maxStudents = builder.maxStudents;
        this.allowLateEntry = builder.allowLateEntry;
        this.classroom = builder.classroom;
        this.note = builder.note;
    }

    // ✅ Builder 클래스
    public static class Builder {
        // 필수값
        private final String bookName;
        private final String teacherName;

        // 선택값 - 기본값 초기화 가능
        private int maxStudents = 30;
        private boolean allowLateEntry = false;
        private String classroom = "A-101";
        private String note = "";

        public Builder(String bookName, String teacherName) {
            this.bookName = bookName;
            this.teacherName = teacherName;
        }

        public Builder maxStudents(int maxStudents) {
            this.maxStudents = maxStudents;
            return this;
        }

        public Builder allowLateEntry(boolean allowLateEntry) {
            this.allowLateEntry = allowLateEntry;
            return this;
        }

        public Builder classroom(String classroom) {
            this.classroom = classroom;
            return this;
        }

        public Builder note(String note) {
            this.note = note;
            return this;
        }

        public AttendanceBook build() {
            return new AttendanceBook(this);
        }
    }

    @Override
    public String toString() {
        return "AttendanceBook{" +
                "bookName='" + bookName + '\'' +
                ", teacherName='" + teacherName + '\'' +
                ", maxStudents=" + maxStudents +
                ", allowLateEntry=" + allowLateEntry +
                ", classroom='" + classroom + '\'' +
                ", note='" + note + '\'' +
                '}';
    }
}
```