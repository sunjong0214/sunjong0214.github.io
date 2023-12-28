---
title : 상속(Inheriterce)
categories : [Java]
tags : [inheriterce, extends, implements]
---



## 상속이란

상속이란 한 마디로 자식이 부모의 특징을 물려 받는 것이다.

예를 들어, 사람이라는 큰 카테고리가 있다면 그 아래 학생, 직장인 등으로 나눌 수 있을 것이다.

이때, 자바에서는 사람 = 부모 클래스, 그 아래 학생, 직장인 = 자식 클래스라고 정의한다. 

위에서 상속이란 자식이 부모의 특징을 물려 받는다고 했는데 그건 어떤 의미일까?

그럼 먼저 사람의 특징에 대해 알아보자.

```java
class Person () {
    public void walk(); // 걷기
    public void think(); // 생각
}
```

그 아래에 있는 자식 클래스들 또한 각각의 특징을 가지고 있다.

```java
class Student () extends Person{
    public void study(); // 학생은 공부
}
class Worker () extends Person{
    public void work(); // 직장인은 일
}
```

이 때 Student 클래스 안에 walk와 think 메소드가 없다고 해서 이 학생이 걷지 못하고, 생각을 못하는 것인가? 아니다 우리가 Student 클래스가 Person 클래스를 extends(상속, Student의 부모 클래스는 Person이다) 한다고 붙여줬기 때문에 Person의 특징들을 Student도 가지고 있는 것으로 여길 수 있다.

이 때 알아둘 점이 있다.

```java
public static void main(String[] args) {
    Student sd = new Student();
    sd.walk();
    sd.think();
    sd.study();
}
```

위와 같이 Student를 객체로 선언하면 부모 클래스의 메소드들 까지 사용이 가능하다. 그럼 walk 메소드나 think 메소드가 Student 클래스 안에서 실행 되는 것으로 볼 수 있는 것 아닌가?

답은 '아니다'.

위와 같이 코드를 짜서 실행 시 코드의 흐름을 알아보자.

```
sd.walk 메소드 실행 -> Student 클래스에서 walk 메소드를 찾는다 -> 없다!! -> 
Student의 부모 클래스인 Person 클래스에서 walk 메소드를 찾는다. -> 찾았다 -> 그 부모 클래스에서 실행!!
```

이렇듯 상속 받았다고 해서 자식클래스에서 부모 클래스의 메소드를 실행하는 것이 아닌 부모 클래스로 직접 찾아가서 실행시킨다는 점을 알아두자.

##### 상속의 다양한 타입들

<img src="https://www.tutorialspoint.com/java/images/types_of_inheritance.jpg" width="100%" height="100%"/>

### 상속의 형태

상위 객체(부모)의 특징(메소드, 변수 등)을 '새롭게 구현'하는가, '그대로 사용'하는가에 따라서 상속의 형태가 갈리게 된다.

1. extends
   - 부모에서 선언/ 정의를 모두하며, 자식은 오버라이딩 할 필요 없이 부모의 메소드/변수를 그대로 사용 가능.
   
   - 부모의 특징을 그대로 가져옴.
2. implements
   - 부모 객체는 선언만 하며, 정의는 반드시 자식이 오버라이딩 해서 사용.
   
   - 부모의 특징을 도구로 사용해 새로운 특징을 만들어 사용.
3. abstract
   - extends 와 implements 의 혼합.
   
   - extends 하되, 몇 개는 추상 메소드로 구현

## extends

extends는 상속의 대표적인 형태입니다. 부모의 클래스를 그대로 사용할 수 있으며, 오버라이딩(재정의)할 필요없이 부모에 구현되어있는 것을 그대로 사용 가능하다.

```java
class Vehicle {
  protected int speed = 3;
  
  public int getSpeed(){
    return speed;
  }
  public void setSpeed(int speed){
    this.speed = speed;
  }
}

class Car extends Vehicle{
  public void printspd(){
    System.out.println(speed);
  }
}

public class ExtendsSample {
  public static main (String[] args){
    Car A = new Car();
    System.out.println(A.getSpeed());
    A.printspd();
  }
}
```

car 객체를 생성하고 그 객체에서 getSpeed 메소드를 불러왔다. getSpeed는 Car 클래스에 없으므로 그의 부모 클래스인 Vehicle로 찾아 들어간다. getSpeed 메소드는 speed 변수를 리턴한다. 이렇듯 extends 시 메소드나 변수 할 거 없이 모두 사용이 가능하다.

하나 주의할 점이 있다. 기본적으로 자바는 **다중 상속**을 지원하지 않는다.

다중 상속이란, 부모 클래스가 두개 이상 존재할 수 있다는 것인다. c++이나 다른 언어들과 달리 자바에서는 지원하지 않는다.

즉, public class Son extends Father, Mother {...} 이 것이 지원하지 않는다는 것이다.

그래서 대신 implements을 사용하면 된다.

## implements

```java
interface TestInterface{
  public static int num = 8;
  public void fun1();
  public void fun2();
}

class InterfaceExam implements TestInterface{
  @Override
  public void fun1(){
    System.out.println(num);
  }
  
  @Override
  public void fun2() {
    
  }
}

public class InterfaceSample{
  public static void main(String args[]){
    InterfaceExam exam = new InterfaceExam();
    exam.fun1();
  }
}
```

implements의 가장 큰 특징은 위와 같이 **부모의 메소드를 반드시 오버라이딩(재정의) 해야한다**는 것이다.

또한 implements는 다중 상속이 가능하다. public class Son implements Father, mother {...} 과 같이 말이다.

이러한 특징 때문에 자바에서 상속은 사전 상의 상속의 의미보다는 계약 및 분류의 의미가 더 강하다고 말할 수 있다.(인용한 부분이라 계약과 분류의 의미는 잘 모르겠다...)

## 정리

1. extends는 일반 클래스와 abstract 클래스 상속에 사용되고, implement는 interface 상속에 사용된다.
2. class가 class를 상속받을 땐 extends를 사용하고, interface가 interface를 상속 받을 땐 extends를 사용한다.
3. class가 interface를 사용할 땐 implements를 써야하고
4. interface가 class를 사용할 땐 implements를 쓸수 없다.
5. extends는 클래스 한 개만 상속 받을 수 있다.
6. extends 자신 클래스는 부모 클래스의 기능을 사용한다.
7. implements는 여러개 사용 가능하다.
8. implements는 설계 목적으로 구현 가능하다.
9. implements한 클래스는 implements의 내용을 다 사용해야 한다. 아니면 오류 발생.

extends = 클래스 확장

implemetns = 인터페이스 구현

과 같이 생각하자.

extends와 implemets 에 대해서는 정리가 끝났지만 abstract에 대해서는 다음 포스팅에서 다루도록 하겠다.



[참조]

[Java - Inheritance]([Java - Inheritance (tutorialspoint.com)](https://www.tutorialspoint.com/java/java_inheritance.htm))

[자바 extends, implements 차이]([자바 extends, implements 차이 (velog.io)](https://velog.io/@hkoo9329/자바-extends-implements-차이))

[[자바]extends와 implements의 차이점](https://zbomoon.tistory.com/13)