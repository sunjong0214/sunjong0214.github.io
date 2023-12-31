---
title : abstract와 interface
categories : [Java]
tags : [abstract, interface]
---

## abstract(추상) class란?

추상 클래스란, 추상 메서드를 선언해 놓고 상속을 통해 자식 클래스에서 메서드를 완성하도록 유도하는 클래스이다.

즉, 자식(서브) 클래스에서 반드시 재정의 해야할 필요가 있는 메서드의 경우 부모(수퍼) 클래스에서 추상 메서드로 만드는 것이다.

이러한 특성 때문에, 미완성 설계도라고 불리기도 한다. 추상 클래스는 상속을 위한 클래스이기 때문에 따로 인스턴스를 생성할 수 없다.

```java
abstract class Main {
    public abstract void 메서드();
}
```

위와 같이 class 앞에 abstract를 붙여 추상 클래스라는 것을 표시한다. 또한 메서드도  abstract 예약어를 붙여 추상 메서드라는 것을 선언할 수 있다.

추상 클래스는 추상 메서드를 가지지 않아도 괜찮다. 다만, 추상 메서드를 하나라도 포함하고 있다면 그 클래스는 추상 클래스가 된다.

추상 메서드를 선언했다면, 자식 클래스에서 반드시 구현을 하도록 강제된다.

또 추상 클래스는 인터페이스와 달리 다중 상속이 불가능하다.

```java
abstract class 클래스{
	...
    public abstract void 메서드();
    
}

class 자식클래스 extends 클래스{
	
    @Override
    public void 메서드(){
    	... // 구현
    }
   
}
```

위와 같이 extends로 상속되며, @Override 어노테이션이 동반된다.

다음으로 abstract와 interface의 차이점을 알아보기 위해 interface에 대해 간단히 설명하겠다.

## Interface

인터페이스도 추상 클래스와 비슷하게 다른 클래스를 작성하는데 도움을 주는 목적으로 작성한다.

다만 인터페이스는 추상 클래스보다 추상화 정도가 높아 추상 클래스와 다르게 구현부가 있는 일반 메서드, 일반 변수 멤버 등을 가질 수 없다. 즉, 인터페이스는 구현된 게 아무것도 없는 **기본 설계도**라고 할 수 있다. 인터페이스 또한 인스턴스를 생성할 수 없다.

```java
interface 인터페이스 {
    public static final 상수 = 값;
    public abstract void 메소드();
}
```

인터페이스의 모든 변수는 public static final, 상수이다. 하지만 컴파일 시 자동으로 생성되므로 생략 가능하다.

인터페이스의 모든 메서드는 public abstract, 추상 메서드이다. 마찬가지로 생략 가능하다.

다만, java 8에서부터 디폴드 메서드와 정적 메서드를 사용할 수 있다.

## 추상 메서드와 인터페이스의 차이

|             | 추상 클래스               | 인터페이스                                            |
| ----------- | ------------------------- | ----------------------------------------------------- |
| 다중상속    | 불가능                    | 가능                                                  |
| 추상 메서드 | 0개 이상                  | 전부                                                  |
| 일반 메서드 | 가능                      | 불가능, Java8에서부터는 디폴트, 정적 메서드 구현 가능 |
| 필드        | 일반 변수, 상수 모두 가능 | 상수만 가능                                           |
| 상속 키워드 | extends                   | implements                                            |
| 접근 제어자 | 제한 없음                 | public                                                |
| 공통점      | 1. 인스턴스 생성 불가     | 2. 상속 받은 클래스는 반드시 추상 메서드 구현         |

추상 클래스와 인터페이스는 표와 같이 구분이 가능하다. 

하지만 기능적으로 봤을 때 둘 다 추상 메서드를 사용해 상속 받는 클래스가 추상 메서드를 구현하도록 강제하는 점이 같다. 또, 추상 클래스를 보면 다중 상속이 안된다는 점을 제외하고는 인터페이스의 역할을 충분히 할 수 있는 것으로 보인다.

그렇다면 다중 상속이라는 한 가지 이유만으로 인터페이스가 도입된걸까?

둘의 가장 큰 차이는 부모클래스(인터페이스)와 자식 클래스의 관계이다.

- 추상 클랙스 : 자식 클래스 is kind of 부모 클래스
- 인터페이스 : 자식 클래스 is able to 부모 인터페이스

이 관계를 제대로 이해한다면 추상 클래스와 인터페이스의 차이를 이해할 수 있다.

예시를 통해 살펴보자

## 추상 클래스 사용 시

#### 1. 명확한 계층 구조 추상화

![](https://blog.kakaocdn.net/dn/2pOt3/btrV6bZe0PE/LfVAWC4fBwDtCGbWuBrGW1/img.png)

예를 들어 그림과 같은 상속관계 클래스가 있다고 치자.

- 사람 is kind of 포유류 
- 포유류 is kind of 생물체
- 조류 is kind of 생물체
- .....

이와 같이 추상 클래스를 상속받는 자식 클래스는 is kind of 관계가 성립한다.

이처럼 클래스끼리의 명확한 계층 구조가 필요할 때 추상 클래스를 사용할 수 있다.

```java
추상 클래스는 이를 상속받을 각 객체들의 공통점을 찾아 추상화시켜 놓은 것으로, 
상속 관계를 타고 올라갔을 때 같은 부모 클래스를 상속하며 부모 클래스가 가진 기능들을 구현해야 할 경우 사용한다.
```

#### 2. 중복 멤버 통합

자식 클래스는 부모 클래스의 일반 멤버 변수도 상속받아 사용할 수 있다.

반면 추상 클래스에 int age를 선언해 놓고 이를 상속 받는다면, 중복되는 멤버 변수를 중복으로 선언하지 않아도 된다.

이처럼 자식 클래스 is kind of 부모 클래스의 관계가 성립하기 때문에 일반 멤버 변수의 통합도 가능하다.

```java
추상 클래스를 이용하면 중복 멤버 변수의 통합이 가능하다. 
인터페이스는 일반 멤버 변수를 선언하지 못하고 상수만 선언할 수 있기 때문에 이러한 중복 멤버 변수의 통합이 불가능하다. 
static final 상수를 선언한다면 각 클래스에서 값의 변경이 불가능할 것이다.
```

## 인터페이스 사용 시

#### 1. 자유로운 관계 묶음

위 그림같이 박쥐와 독수리는 날 수 있고, 사람과 펭귄은 수영할 수 있다 치자.

이때 박쥐와 독수리의 기능으로 fly() 메소드를 추가하고 싶으면 부모 클래스인 포유류나 조류에 메서드를 선언할 수 없다. 사람이나 펭귄은 날 수 없는데 메서드의 구현이 강제받기 때문이다.

어쩔 수 없이 박쥐와 독수리에 따로 fly() 메서드를 구현해야한다. 공통된 기능이 있음에도 추상화 할 수 없는 것이다.

하지만 인터페이스를 사용한다면 공통도니 기능을 뽑아낼 수 있다.

![](https://blog.kakaocdn.net/dn/bbVc8J/btrV5BjRqyt/h0pxWjESUQUusK6bchpek1/img.png)

- 박쥐 is albe to 날기
- 독수리 is albe to 날기
- 사람 is albe to 수영
- 펭귄 is albe to 수영

위와 같이 is albe to 관계에서는 인터페이스를 사용 가능하다. 인터페이스는 추상 클래스보다 상대적으로 자유로운 관계 묶음이 가능하다. 서로 관련성이 없는 클래스이더라도 같은 동작을 한다면 하나의 인터페이스로 묶을 수 있다. 심지어 다중상속도 가능하기 때문에 다양한 인터페이스를 implements 해서 기능을 구현 가능하다.

```java
인터페이스는 상속 관계를 타고 올라갔을 때 다른 조상 클래스를 상속하더라도, 같은 기능이 필요할 경우 사용한다.
클래스와 별도로 구현 객체가 같은 동작을 한다는 것을 보장하기 위해 사용한다.
```

특히, is albe to 관계에 있을 때 인터페이스를 사용할 수 있다. 이러한 이유 때문인지 인터페이스명은 ~able로 끝나는 경우가 많다. 

어떤 기능 또는 행위를 하는데 필요한 메서드를 제공한다는 의미를 강조하기 위해서다 ex) Comparable, Serializable...등

#### 2. 다중 상속이 필요한 경우 

![](https://blog.kakaocdn.net/dn/bgcSEx/btrV3y2x10t/MkfZ1cyeVDpf26FJpFIj2k/img.png)

위 그림과 같이 speak()을 추가한다고 한다고 가정해보자. 그리고 나중에 만의 하나라도 말할 수 있는 생물이 나타난다고 생각한다면 이 또한 재사용이 필요한 부분이다.

그렇기 때문에 다중 상속을 이용해 재사용성을 높일 수 있고, 다중 상속이 가능하기 때문에 이러한 구현이 가능하다.

## 정리

정리하자면 

추상 클래스는 is kind of 관계로 관련성이 높은 명확한 계층 구조의 추상화에 사용된다.

인터페이스는 is able to 관계로 관련성이 낮더라도 기능에 따른 추상화가 필요하거나 다중 상속(구현)이 필요할 때 사용된다.



실제 프로젝트에서 인터페이스와 추상 클래스가 어떤 식으로 쓰이는지 한번 알아보자

우리가 인터페이스 'a'에는 상속 시 구현해야될 추상 메소드가 100가지 있다고 가정하자.

근데 우리가 'a' 인터페이스를 상속 받아 만들 클래스 'b'는 'a' 인터페이스에서 50개의 추상 메서드만 상속받아 사용해도 원하는 로직을 구현할 수 있다.

그럼에도 'a' 인터페이스를 직접 상속 받은 'b' 클래스는 나머지 50개의 추상 메서드를 규칙에 맞춰 구현해야만 한다.

```java
'a'에 100개의 추상 메서드가 있다.
    
'b'라는 클래스는 'a'로부터 상속(implements) 받아 원하는 기능을 구현해야 된다. 
    
'a'인터페이스에 있는 추상 메서드 중 구현에 필요한 추상 메서드는 50개이다.
    
그럼에도 'a' 인터페이스에 있는 100개의 추상 메서드를 'b' 클래스에 모두 구현.
```

이런 비효율성을 줄이기 위해 사용될 수 있는 것이 추상 클래스(abstract)이다.

위에서 'b' 클래스를 만들기 위해 했던 것들을 'c' 추상 클래스를 이용해서 좀 더 효율적으로 바꿔보자.

```java
'a' 인터페이스를 상속(implements) 받는 추상 클래스 'c'를 만들고, 'a' 인터페이스의 추상 메서드를 모두 'c' 클래스에 추상 메서드로 구현.

그 후 우리가 실질적으로 구현에 사용할 'b' 클래스는 'c' 추상 클래스를 상속(extends).

그 후 'b' 클래스에서 구현하는 필요한 추상 메서드 50개만 'c' 추상 클래스에서 상속(extends)받아 구현.
```

이런 식으로 추상 메서드와 인터페이스를 같이 사용한다면 더 효율적으로 상속을 사용 가능하다.



추가로 참고해서 수정해야겠다....

[언제 추상클래스와 인터페이스를 사용해야할까?](https://mangkyu.tistory.com/321)



출처) [[JAVA] 추상클래스와 인터페이스의 차이](https://code-lab1.tistory.com/287)