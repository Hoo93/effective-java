# Item 2. 생성자에 매개변수가 많다면 빌더를 고려하라

## 📑 목차
1. [Item 2. 생성자에 매개변수가 많다면 빌더를 고려하라](#item-2-생성자에-매개변수가-많다면-빌더를-고려하라)
2. [0. 정적 팩토리 메서드란?](#0-정적-팩토리-메서드란)
3. [1. 빌더패턴을 사용하면 좋을 때](#1-빌더패턴을-사용하면-좋을-때)
4. [2. 빌더패턴의 장점](#2-빌더패턴의-장점)
5. [3. 빌더 패턴의 예시](#3-빌더-패턴의-예시)  
   5.1 [기본 빌더 패턴](#3-1-기본-빌더-패턴)  `
   5.2 [계층형 빌더 패턴](#3-2-계층형-빌더-패턴)  
      - 5.2.1 [상위 계층 (User Class)](#3-2-1-상위-계층-user-class)  
      - 5.2.2 [하위 계층 (Teacher)](#3-2-2-하위-계층-teacher)  
      - 5.2.3 [핵심 개념](#3-2-3-핵심-개념)

## 1. 빌더패턴을 사용하면 좋을 때
1. 매개변수가 4개 이상일 때
2. 매개변수 중에 optional 한 값일 많을 때

## 2. 빌더패턴의 장점
1. 가독성이 좋다
2. 불변객체로 만들 수 있다. ( setter 를 열어놓음으로써 객체가 변경되는 일을 방지할 수 있다. )
3. java beans ( 기본 생성자 + setter )의 1.일관성 깨짐/ 2.불변성 보장 불가/ 3.실행 중 오류 발생 가능성의 문제들을 해결
3. 계층형 클래스와 잘 맞는다.

## 3. 빌더 패턴의 예시
### 3-1. 기본 빌더 패턴 
```java
public class AttendanceBook {

    // ✅ 필수값
    private final String bookName;
    private final String teacherName;

    // ✅ 선택값
    private final boolean allowLateEntry = true; // 기본값 : true 
    private final int maxStudents = 30; // 기본값 : 30
    private final String classroom; // nullable
    private final String note; // nullable

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
        private int maxStudents = 30; // 선택값이지만 기본값을 세팅
        private boolean allowLateEntry = false;
        private String classroom;
        private String note;

        // 필수값을 입력 받으면서 Builder 생성
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
}
```


### 3-2. 계층형 빌더 패턴 ( Lombok : @SuperBuilder로 동일한 구조를 자동화 가능 )
#### 3-2-1. 상위 계층 ( User Class )
```java
public class User {
    protected final String name;
    protected final String email;

    protected User(Builder<?> builder) {
        this.name = builder.name;
        this.email = builder.email;
    }

    // 💡 <T extends Builder<T>> 제네릭을 이용해서 하위타입의 builder 타입이 T 임을 보장해 메서드 체이닝을 이어갈 수 있도록 한다.
    public static class Builder<T extends Builder<T>> {
        protected String name;
        protected String email;

        public T name(String name) {
            this.name = name;
            // 💡 return this 가 아니라 하위 타입에서 self() 함수를 override 해서 하위 타입을 리턴할 수 있게 한다.  
            return self();
        }

        public T email(String email) {
            this.email = email;
            return self();
        }

        // 💡 하위 타입에서 메서드를 호출할 시 자기 저신을 리턴할 수 있도록 한다.
        protected T self() {
            return (T) this;
        }

        public User build() {
            return new User(this);
        }
    }
}
```


#### 3-2-2. 하위 계층 ( Teacher )
```java
public class Teacher extends User {
    private final String subject;

    protected Teacher(Builder builder) {
        super(builder);
        this.subject = builder.subject;
    }

    // 💡 여기서 User.Builder 의 제네릭으로 사용되는 Builder 는 Teacher.Builder 이다.
    public static class Builder extends User.Builder<Builder> {
        private String subject;

        public Builder subject(String subject) {
            this.subject = subject;
            return this;
        }

        // 💡 반드시 self 를 override 해서 하위 타입을 리턴해주어야 한다.
        @Override
        protected Builder self() {
            return this;
        }

        @Override
        public Teacher build() {
            return new Teacher(this);
        }
    }
}

```

#### 3-2-3. 핵심 개념
1. T extends Builder<T> : 재귀적 제네릭 바운딩 (CRTP). 하위 빌더가 자기 타입을 전달하도록 강제
2. self() : this를 T로 캐스팅하여 정확한 빌더 타입으로 반환. 체이닝 유지 핵심
3. 상속 구조 : Teacher.Builder extends User.Builder<Builder>
4. 장점 : 상위 필드는 상위 빌더가, 하위 필드는 하위 빌더가 처리하여 역할 분리 및 유지보수 용이