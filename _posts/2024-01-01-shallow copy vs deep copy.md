---
title : Shallow copy VS Deep copy
categories : [CS]
tags : [memory]
---

## Value and Reference type

모든 데이터 타입은 값(value) 또는 참조(reference) 타입을 가진다.

- 값 타입 (Value type) : 각각의 고유의 메모리를 소유한다.
- 참조 타입 (Reference type) : 생성된 인스턴스들은 주소값을 공유한다.

두 가지 타입 모두 copy 메소드가 존재하는데 deep copy를 지원하는지 아니면 shallow copy를 지원하는지 알아보자

## Deep copy (깊은 복사)

- 데이터 자체를 통째로 복사한다.
- 복사된 두 객체는 완전히 독립적인 메모리를 차지한다.
- Value type의 객체들은 깊은 복사를 하게 된다.

아래 예시를 통해 알아보자.

```java
String[] arr1 = {"abc", "def"};
String[] arr2 = arr1;
```

다음과 같이 String 배열인 arr1을 선언하고, arr1 배열을 또 다른 String 배열인 arr2에 할당했다.

이때 Deep copy가 일어난다.

```java
arr1[0] = "ghi";
---------------------결과
arr1 = "ghi", "def"
arr2 = "abc", "def"    
```

위와 같이 arr1의 0번째 인덱스 값을 변경해도 arr2에는 영향이 가지 않는다.

이렇듯 **깊은 복사가 일어날 경우 두 객체는 완전히 독립적**이다. 모든 value type에 적용된다.

## Shallow copy (얕은 복사)

앝은 복사는 값을 복사할 때, 인스턴스가 메모리에 새로 생성되지 않는다. 값 자체를 복사하는 것이 아닌 주소값만 복사하여 같은 메모리를 가리키는 것이기 때문이다.

새로운 인스턴스를 생성하는 것이 아니기 때문에 Deep copy보다 상대적으로 빠르다. reference type의 경우 Shallow copy가 일어난다.

예시를 보자.

```java
class Address {
    String address = "String";
    
    public Address (String address) {
        this.address = address;
    }
}
```

```java
class main {
    Address add = new Address("Seoul");
    Address add2 = add;
    
    System.out.println(add.address); //Seoul
    System.out.println(add2.address); //Seoul
    
    add2.address = "JeJu";
    
    System.out.println(add.address); //JeJu
    System.out.println(add2.address); //JeJu
}
```

위 예제와 같이 add를 add2에 할당해줬다. 이럴 경우 얕은 복사가 일어난다.

같이 주소값을 가지고 있기 때문에 add의 값을 바꾼다면, add2의 값도 바뀌게 된다. 반대도 마찬가지다.

## Deep copy의 여러가지 구현 방법

주로 사용하는 3가지 방법에 대해 알아보자.

1. 복사 생성자 또는 복사 팩터리를 이용하여 복사

2. 직접 객체를 생성하여 복사

3. Cloneable을 구현해 clone() 재정의 (final 인스턴스 또는 배열이 아닌 경우 사용을 권하지 않음)

따라서 cloneable을 구현하여 clone()을 재정의하기보단 복사 생성자나 복사 팩터리를 이용하여 Deep copy하는 것이 좋다.

#### 1. 복사 생성자 or 복사 팩토

   ```java
   public class Student {
   
       // 복사 생성자
       public Student (Student student) {
           this.name = student.name;
           this.age = student.age;
       }
       // 복사 팩토리
       public static Student newInstance(Student student) {
           Student std = new Student();
           std.name = student.name;
           std.age = student.age;
           return std;
       }
   }
   ```

#### 2. 직접 객체를 생성하여 복사

```java
void deepCopy() {
    Student std = new Student("철수", 20);
    Student stdCopy = new Student();
    stdCopy.setName(std.getName());
    stdCopy.setAge(std.getAge());
    
    std.chageName("영희");
   	std.chageAge(22);
}
```

#### 3. Cloneable을 구현하여 clone() 재정의

(final 인스턴스 또는 배열이 아닌 경우 추천 X)

```java
public class Student implements Cloneable {
    String name;
    int age;
    
    @Override
    protected Student clone() throws CloneNotSupportedException {
        return (Student) super.clone();
    }
    
    void deepCopy() throws CloneNotSupportedException {
        Student std = new Student("민수" 22);
        Student stdCopy = std.clone();
        
        std.chageName("문규");
        std.chageAge(24);
    }
}
```



참조) 

[Shallow copy VS Deep copy](https://velog.io/@ellyheetov/Shallow-Copy-VS-Deep-Copy)

[[Java] Shallow copy vs Deep copy](https://jackjeong.tistory.com/entry/Java-Shallow-copy%EC%96%95%EC%9D%80-%EB%B3%B5%EC%82%AC-vs-Deep-copy%EA%B9%8A%EC%9D%80-%EB%B3%B5%EC%82%AC)