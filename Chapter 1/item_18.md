# 📘 Item 18. 상속보다는 Composition을 사용하라

### ✅ 1. 핵심 개념
> 상속은 강한 결합을 초래하고, 변경에 취약하다.
> **대신 Composition(구성)을 사용하여 유연하고 확장 가능한 설계를 하라.**
> Composition은 상속보다 유연하고, 변경에 강하며, 코드 재사용성을 높인다.


### ✅ 2. 대표적인 예
```java
public class InstrumentedSet<E> implements Set<E> {
    private final Set<E> set;
    private int addCount = 0;

    public InstrumentedSet(Set<E> set) {
        this.set = set;
    }

    @Override
    public boolean add(E e) {
        addCount++;
        return set.add(e);
    }

    @Override
    public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return set.addAll(c);
    }

    public int getAddCount() {
        return addCount;
    }

    // 나머지 Set 메서드는 위임(delegate)
}
```
장점: 내부 Set 구현이 바뀌어도 영향을 받지 않음.
→ 버그 발생 가능성 감소, 확장성 향상