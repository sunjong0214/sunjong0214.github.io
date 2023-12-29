---
title : Immutable Object(불변 객체) 및 final 사용 이유
categories : [Java]
tags : [Immutable Object, final]
---



## Immutable Object(불변 객체)란

객체 생성 이후 내부의 상태가 변하지 않는 객체를 뜻 한다.

read-only 메소드만 제공하며, 객체의 내부 상태를 제공하는 메소드를 제공하지 않거나 **방어적 복사(defensive-copy)**를 제공한다. 대표적으로 String 객체가 있다.

```java
String name = "old";
name.toCharArray()[0] = 'E';
```

위와 같이 String 내부의 char형 배열을 얻어 수정하여도 반영이 되지 않는다. Java에서는 배열이나 객체 등의 참조(Reference)를 전달한다. 그렇기 때문에 참조를 통해 값을 수정하면 원본의 상태가 변하기 때문에 **내부를 복사하여 전달**하는데, 이를 방어적 복사(defensive-copy)라고 한다.

String의 toCharArray는 다음과 같다.

```java
public char[] toCharArray() {
    char result[] = new char[value.length];
    System.arraycopy(value, 0, result, 0, value.length); // 
    return result;
}
// toCharArray란 String 문자열을 char형 배열로 바꿔서 반환해주는 메소드
```

**[ 불변 객체(Immutable Object) 및 final을 사용해야 하는 이유 ]**

1. **Thread-Safe하여 병렬 프로그래밍에 유용하며, 동기화를 고려하지 않아도 된다.**

   멀티 쓰레드 환경에서 동기화 문제가 발생하는 이유는 공유 자원에 동시에 쓰기(Write) 때문이다. 하지만 만약 공유 자원이 불변이라면 더 이상 동기화를 고료하지 않아도 된다. 왜냐면 항상 동일한 값을 반환하기 때문이다. 이는 안정성을 보장하고, 동기화 하지 않음으로써 성능상의 이점도 가져다준다.

2. **실패 원자적인(Failure Atomic) 메소드를 만들 수 있다.**

   가변 객체를 통해 작업을 하는 도중 예외가 발생하면 해당 객체가 불안정한 상태에 빠질 수 있고, 불안정한 상태를 갖는 객체는 또 다른 에러를 유발할 수 있다. 하지만 불변 객체라면 어떠한 예외가 발생하여도 메소드 호출 전의 상태를 유지 할 수 있을 것이다. 그리고 예외가 발생하여도 오류가 발생하지 않은 것 처럼 다음 로직을 처리할 수 있다.

3. **Cache나 Map 또는 Set 등의 요소로 활용하기에 더욱 적합하다.**

   만약 캐시나 Map, Set 등의 원소인 가변 객체가 변경되었다면 이를 갱신하는 등의 부가 작업이 필요할 것이다. 하지만 불변 객체라면 한 번 데이터가 저장된 이후에 다른 작업들을 고려하지 않아도 되므로 사용하는데 용이하게 작용할 것이다.

4. **부수 효과(Side Effect)를 피해 오류가능성을 최소화할 수 있다.**

   부수효과란 변수의 값이나 상태 등의 변화가 발생하는 효과를 의미한다. 만약 객체의 수정자(Setter)를 통해 여러 객체들에서 값을 변경한다면 객체의 상태를 예측하기 어려워진다. 바뀐 상태를 파악하기 위해서는 메소드들을 살펴보아야하고, 이는 유지보수성을 상당히 떨어트린다. 그래서 이러한 부수효과가 없는 순수 함수들을 만드는 것이 상당히 중요한데, 객체가 불변이라면 어떤 효과가 있을까?

   불변 객체는 기본적으로 값의 수정이 불가능하기 때문에 변경 가능성이 적으며, 객체의 생성과 사용이 상당히 제한된다. 그렇기 때문에 메소드들은 자연스럽게 순수 함수들로 구성될 것이고, 다른 메소드가 호출되어도 객체의 상태가 유지되기 때문에 안전하게 객체를 다시 사용할 수 있다. 이러한 불변 객체는 오류를 줄여 유지보수성이 높은 코드를 작성하도록 도와줄 것이다.

5. **다른 사람이 작성한 함수를 예측가능하며 안전하게 사용할 수 있다.**

   일반적으로 개발은 다른 사람들과 협업을 하게 된다. 불변성(Immutability)은 협업 과정에서도 도움을 주는데, 불변성이 보장된 함수라면 다른 사람이 개발한 함수를 위험없이 이용할 수 있다. 마찬가지로 다른 사람도 내가 작성한 메소드를 호출하여도, 값이 변하지 않음을 보장받을 수 있다. 그렇기에 우리는 변경에 대한   불안없이 다른 사람의 코드를 이용할 수 있다. 또한 불필요한 시간을 절약할 수도 있는데, 이에 대한 예제는 아래에서 자세히 살펴보도록 하자.


6. **가비지 컬렉션의 성능을 높일 수 있다.**
    불변성(Immutability)의 많은 이점 중에서 많은 사람들이 놓치는 것이 바로 GC의 성능을 높여준다는 것이다.
    불변의 객체는 한번 생성된 이후에 수정이 불가능한 객체로, Java에서는 final 키워드를 사용하여 불변의 객체를 생성할 수 있다. 이렇게 객체를 생성하기 위해서는 객체를 가지는 또 다른 컨테이너 객체(ImmutableHolder)도 존재한다는 것인데, 당연히 불변의 객체(Object value)가 먼저 생성되어야 컨테이너 객체가 이를 참조할 수 있을 것이다. 즉, 컨테이너는 컨테이너가 참조하는 가장 젊은 객체들보다 더 젊다는 것(늦게 생성되었다는 것)이다. 이를 정리하면 다음과 같다.

  - Object 타입의 value 객체 생성

  - ImmutableHolder 타입의 컨테이너 객체 생성

  - ImmutableHolder가 value 객체를 참조


​	이러한 점은 GC가 수행될 때, 가비지 컬렉터가 컨테이너 객체 하위의 불변 객체들은 Skip할 수 있도	록 도와준다. 왜냐하면 해당 컨테이너 객체(ImmutableHolder)가 살아있다는 것은 하위의 불변 객체	들(value) 역시 처음에 할당된 상태로 참조되고 있음을 의미하기 때문이다.

```java
public class MutableHolder {
    private Object value;
    public Object getValue() { return value; }
    public void setValue(Object o) { value = o; }
}

public class ImmutableHolder {
    private final Object value;
    public ImmutableHolder(Object o) { value = o; }
    public Object getValue() { return value; }
}

@Test
public void createHolder() {
    // 1. Object 타입의 value 객체 생성
    final String value = "MangKyu";
    
    // 2. Immutable 생성 및 값 참조
    final ImmutableHolder holder = new ImmutableHolder(value);
    
}
출처: https://mangkyu.tistory.com/131 [MangKyu's Diary:티스토리]
```

결국 불변의 객체를 활용하면 가비지 컬렉터가 스캔해야 되는 객체의 수가 줄어서 스캔해야 하는 메모리 영역과 빈도수 역시 줄어들 것이고, GC가 수행되어도 지연 시간을 줄일 수 있을 것이다. 그렇기 때문에 필드값을 수정할 수 있는 MutableHolder보다는 필드값을 수정할 수 없는 ImmutableHolder를 사용하는 것이 좋다.
누군가는 위의 코드를 보고 Holder의 값이 바뀌는 경우라면 MutableHolder를 이용하는 것이 더 낫지 않냐고 의구심을 가질 수 있다. 하지만 앞선 포스팅에서 살펴보았듯 GC는 새롭게 생성된 객체는 대부분 금방 죽는다는 Weak Generational Hypothesis 가설에 맞추어 설계되었다. 가비지 컬렉터의 입장에서 생명 주기가 짧은(short lifespan) 객체를 처리하는 것은 그렇게 큰 문제가 아니며, 오히려 MutableHolder의 값이 지속되어 old-to-young 참조가 일어나는 것이 더 큰 성능 저하를 야기할 것이다.



## Java에서 불변 객체를 생성하는 법

- **[final 키워드]**

  Java에서 변수들은 기본적으로 가변적인데, 변수에 final 키워드를 붙이면 참조값을 변경 못하도록 하여 불변성을 확보할 수 있다.

  ```java
  final String name = "Old";
  name = "New"; //컴파일 에러
  ```

  Java에서는 final이 붙은 변수의 값을 변경하려고 하면 컴파일 에러가 발생한다. 

  하지만 final 키워드가 내부의 객체 상태를 변경하지 못하도록 하는 것은 아니다. 하지만 final 키워드가 내부의 객체 상태를 변경하지 못하도록 하는 것은 아니다. 예를 들어 final로 선언된 List에는 새로운 객체가 더해져도 문제가 없다. (상태가 변했음에도) 그렇기 때문에 Java에서는 참조에 의해 값이 변경될 수 있는 점들을 유의해야 하는데, 이를 방지하려면 불변 클래스로 만들어야한다.

  ```java
  final List<String> list = new ArrayList<>();
  list.add("a");
  ```

- 불변 클래스

  Java에서 불변 객체를 생성하기 위해서는 다음과 같은 규칙에 따라서 생성해야한다.

  1. 클래스를 final로 선언
  2. 모든 클래스 변수를 private와 final로 선언
  3. 객체를 생성하기 위한 생성자 또는 정적 팩토리 메소드를 추가하라
  4. 참조에 의해 변경 가능성이 있는 경우 방어적 복사를 이용해 전달

  ```java
  public final class ImmutableClass {
      private final int age;
      private final String name;
      private final List<String> list;
  
      private ImmutableClass(int age, String name) {
          this.age = age;
          this.name = name;
          this.list = new ArrayList<>();
      }
  
      public static ImmutableClass of(int age, String name) {
          return new ImmutableClass(age, name);
      }
      
      public int getAge() {
          return age;
      }
  
      public String getName() {
          return name;
      }
  
      public List<String> getList() {
          return Collections.unmodifiableList(list);
      }
      
  }
  출처: https://mangkyu.tistory.com/131 [MangKyu's Diary:티스토리]
  ```

  위의 코드에서 특히 주목해야 하는 부분은 내부 생성자를 만드는 대신 객체의 생성을 위해 정적 팩토리 메소드를 제공하고 있다는 점과 참조를 전달하여 클라이언트에 의해 수정가능성이 있는 list를 방어적 복사하여 제공하고 있다는 것이다.
  Java에서는 생성자를 선언하지 않으면 기본 생성자가 자동으로 생성되는데, 그러면 다른 클래스에서 해당 객체를 자유롭게 호출할 수 있다. 그렇기 때문에 내부 생성자를 만드는 대신 정적 팩토리 메소드를 통해 객체를 생성하도록 강요하는 것이 좋다.
  또한 배열이나 다른 객체 또는 컬렉션은 참조가 전달되어 수정가능성이 있다. 그렇기 때문에 참조를 통해 변경이 가능한 경우에는 방어적 복사를 통해 값을 반환해야 한다. 마지막으로 클래스의 변수에 가능하다면 final을, final이 불가능하다면 Setter를 최소화하도록 하자.
  
  

Java에 대한 유명한 책인 Effective Java에는 다음과 같은 구문이 적혀있다. 이를 명심하고 개발하도록 하자.

> ```
> 클래스들은 가변적이여야 하는 매우 타당한 이유가 있지 않는 한 반드시 불변으로 만들어야 한다. 
> 만약 클래스를 불변으로 만드는 것이 불가능하다면, 가능한 변경 가능성을 최소화하라.
> - Effective Java -
> ```
>

참조) [Immutable Object(불변 객체) 및 final 사용 이유](https://mangkyu.tistory.com/131)
