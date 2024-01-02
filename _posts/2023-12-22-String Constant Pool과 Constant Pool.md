---
title : String Constant Pool과 Constant Pool
categories : [Java]
tags : [String Constant Pool, Constant Pool]
---

String 클래스에 대해 공부를 했다면 String이 String Constant Pool 이라고 불리는 String 상수풀에 저장된다는 것을 알 것이다.

하지만 String Constant Pool과 Constant Pool이 용어가 비슷해 혼용해 헷갈리는 경우가 많은데, 명백히 다른 저장공간이다.

심지어 Constant Pool은 JVM-Metaspace 영역의 Constant Pool과 컴파일된 class파일의 Constant Pool로 나뉘기 때문에 헷갈리지 말고 확실히 정리하자.

![](https://blog.kakaocdn.net/dn/eaJffC/btsgwMqYWOP/lWjjsBoXdDoUMWgrSr5ZCK/img.png)

## String Constant Pool

Java에서 문자열 리터럴을 지정하는 독립된 영역을 'String Constant Pool' 또는 'String Pool'이라고 부릅니다.

 JVM - Perm/Metaspace 영역에 존재하고 일반적으로 GC 대상이 되지는 않습니다. String은 불변객체이기 때문에 문자열의 생성 시 이 String Constant Pool에 저장된 리터럴을 재사용할 수 있습니다.

```java
[참고] String constant pool은 일반적으로 GC가 되지 않지만, 문자열 참조 대상이 없는 경우 선택적으로 GC 대상이 되기도 합니다. 
또한, -XX:+UseStringDeduplication옵션 사용을 통해 중복 문자열에 대해 GC를 수행하도록 트리거를 줄 수 있습니다. 자세한 내용은 여기를 참조해주세요.
```

소스코드에서 리터럴("Hello")로 선언된 문자열이 이 저장공간에 저장되며 동적으로 생성된 문자열은 저장되지 않습니다. 그리고 한번 저장된 문자열은 소스코드에서 동일한 문자열에 대해 재사용하여 메모리를 절약하는 역할을 합니다.

String constant pool에 문자열이 저장되는 조건은 아래와 같습니다.

1. 소스코드에 문자열을 선언한 경우

```java
String str = "Hello world"; // String constant pool에 저장됨
String doNotUse = new String("Hello World"); // 동적 생성, heap 영역에 저장됨
```

소스코드에 리터럴 형태로 작성한 문자열은 컴파일 시 컴파일러에 의해 string constant pool 지정 대상으로 표시됩니다.

코드 클래스 파일의 상수 풀(아래에서 나올 constant pool과 다름)에 'CONSTANT_String' 타입으로 저장되며 런타임시 스캔 대상이 되어 string constant pool로 저장하는 구조입니다.

이렇게 String constant pool에 저장된 문자열은 모두 같은 객체를 재사용하게 됩니다.

```java
String str = "hello"; // String constant pool에 저장
String str2 = "hello"; // String constant pool에서 재사용
String str3 = new ("hello"); // 별도의 Heap 메모리에 저장

System.out.println(str == str2);	// true (같은 객체를 재사용하기 때문에)
System.out.println(str == str3);	// false
System.out.println(str.equals(str3));	// true
```

![](https://blog.kakaocdn.net/dn/cotK5y/btsgCiZkpuA/Go6dv3XeaWJeR8hV71TUg0/img.png)

new 연산자를 사용하여 문자열 생성시 string constant pool을 사용하여 최적화가 가능할 수 있던 문자열을 heap 영역으로 신규 저장하게 됩니다. 

new 연산자를 사용 시 더 이상 기존 값을 참조되지 않는다.

그러므로 성능 및 메모리 최적화를 위하여 문자열 선언 시 new 연산자 사용은 지양하는 것이 좋습니다.

2.String.intern() 메서드를 실행한 경우

```java
new String("Hello World").intern(); // String pool에 강제로 넣음
new String("World").intern(); // String pool에 강제로 넣음
new String("Hello World").intern(); // 이미 String pool에 있기 때문에, 추가되지 않음
```

intern 메서드는 String constant pool에 해당 문자열이 있는지 검증하고 없으면 상수 풀에 저장한 후 레퍼런스를 전달하고, 있으면 기존 상수 풀에 있는 레퍼런스 값을 넘겨준다. 특정 문자열을 강제로 String constant pool에  넣기 때문에 정해진 문자열이 아닌 동적인 문자열에 이 intern() 메서드를 사용하면 상수풀이 사용하는 메모리가 계속해서 커진다. 또, 이 메서드는 비싸고 느리기 때문에 사용이 권장되지 않는다.

그렇기 때문에 어떤 경우에도 **절대 사용하지 않는 것**이 좋습니다. intern 메서드를 사용하는 경우 String constant pool의 크기가 계속해서 커지며, 메모리가 부족해질 수 있습니다. 그리고 이 저장공간은 어지간해서는 GC 대상이 되지 않고 애플리케이션이 종료될 때까지 메모리에 남아있습니다. 즉, 메모리 릭의 발생 가능성을 높이는 메서드이므로 사용하지 않는 것이 좋습니다.

## Constant Pool (class file)

컴파일시 클래스 파일 내부에 존재하는 영역으로 String Constant Pool과는 다른 Constant Pool 영역으로, 클래스 로더에 의해 JVM에 로드될 때 메모리에 로드합니다. 주로 클래스의 구성요서(상수, 문자열, 클래스/인터페이스 참조) 데이터를 저장하고 있습니다.

다음 클래스 파일의 경우 Class Constant Pool이 이렇게 표기됩니다.

```java
class Hello {
    public static void main( String[] args ) {
        for( int i = 0; i < 10; i++ )
            System.out.println( "Hello from Hello.main!" );
  }
}
```

위 클래스를 컴파일한 후, 상수풀을 열어보면 이런 상수들로 저장되어 있습니다.

```java
  #1 = Methodref          #6.#16         // java/lang/Object."<init>":()V
   #2 = Fieldref           #17.#18        // java/lang/System.out:Ljava/io/PrintStream;
   #3 = String             #19            // Hello from Hello.main!
   #4 = Methodref          #20.#21        // java/io/PrintStream.println:(Ljava/lang/String;)V
   #5 = Class              #22            // Hello
   #6 = Class              #23            // java/lang/Object
   #7 = Utf8               <init>
   #8 = Utf8               ()V
   #9 = Utf8               Code
  #10 = Utf8               LineNumberTable
  #11 = Utf8               main
  #12 = Utf8               ([Ljava/lang/String;)V
  #13 = Utf8               StackMapTable
  #14 = Utf8               SourceFile
  #15 = Utf8               Hello.java
  #16 = NameAndType        #7:#8          // "<init>":()V
  #17 = Class              #24            // java/lang/System
  #18 = NameAndType        #25:#26        // out:Ljava/io/PrintStream;
  #19 = Utf8               Hello from Hello.main!
  #20 = Class              #27            // java/io/PrintStream
  #21 = NameAndType        #28:#29        // println:(Ljava/lang/String;)V
  #22 = Utf8               Hello
  #23 = Utf8               java/lang/Object
  #24 = Utf8               java/lang/System
  #25 = Utf8               out
  #26 = Utf8               Ljava/io/PrintStream;
  #27 = Utf8               java/io/PrintStream
  #28 = Utf8               println
  #29 = Utf8               (Ljava/lang/String;)V
```

## Runtime Constant Pool (JVM)

상수 풀, Runtime Constant Pool이라고 부르며 Java 8 이전에는 JVM-Perm 영역에 저장되었고, Java 8 출시 이후부터는 JVM-Metaspace 영역에 저장된다. 앞서 설명한 **Class file constant pool**이 런타임시 이 영역으로 저장됩니다. 참고로 상수 풀은 Method Area, 즉 정적영역에 있는 메모리이다.

주로 클래스와 관련된 메타데이터를 저장하고 클래스 구조, 필드, 메서드와 같은 데이터를 저장합니다. Primarity Type 뿐만 아니라 String 객체, 레퍼런스 타입이 가진 주소 값도 상수 풀로 관리된다.

Runtime 로딩 과정은 [이 문서](https://docs.oracle.com/javase/specs/jvms/se6/html/ConstantPool.doc.html)를 참고하면 된다. 클래스 상수 풀 또한 클래스 로더에 의해 클래스 로딩할 때 Runtime Constant Pool에 저장됩니다.

### Metaspace 영역으로 이관된 이유

Perm 영역에 이 데이터가 저장되던 시점에는 Class, Matespace 로딩 과정에서 메모리 릭이 발생하였고, Perm 영역의 크기를 고정적으로 설정해야 했기 때문에 메모리 부족으로 OOM이 터지는 일이 있었습니다. 조금 더 설명하자면 Metaspace 영역은 JVM의 Native Memeory를 사용하며 JVM이 관리합니다. Perm 영역과의 결정적인 차이는 메모리를 동적으로 관리되며 필요할 경우 OS에게 요청하여 메모리를 추가 할당할 수 있습니다. 이를 통해 OOM을 개선할 수 있었습니다.

## 결론

|             |            String Constant Pool             | Constant Pool inside a Java class file                       |                    Runtime Constant Pool                     |
| ----------- | :-----------------------------------------: | ------------------------------------------------------------ | :----------------------------------------------------------: |
| 역할        |     String 객체의 상수 값을 저장 (캐싱)     | 클래스 파일의 상수 값을 저장                                 | Class Constant Pool에서 읽어온 상수 값, 클래스 메타데이터 저장 |
| 저장되는 값 |     String 상수 값 ex) "Hello", "World"     | 클래스 파일에 포함된 상수 값 ex) 123, 3.14,  true            | 클래스 파일에 포함된 상수값. class constant pool에 저장되어있던 값이 런타임시 이 영역으로 저장 |
| 저장 위치   | Java 7 이전 - Perm, Java 8 이상 - Metaspace | Class file                                                   |         Java 7 이전 - Perm, Java 8 이상 - Metaspace          |
| 저장 트리거 |   String.intern(), String을 리터럴로 생성   | 컴파일시 생성됨                                              | 클래스 파일에 코드 레벨로 선언된 상수풀이 런타임시 로더의 판단에 의해 올라옴. |
| 불변 여부   |               불변(immutable)               | Class 파일 자체는 불변. Runtime 시 동적 로드에 의해 변경될 수 있음. | 불변이 아님. 클래스 파일이 동적으로 로딩되고 초기화 되기 때문에, 로드될 때 마다 변경됨. |

String Constant Pool - Heap 영역, String 리터럴이 저장

Constant Pool은 - Method Area에 있는 Metaspace 영역, 클래스 파일에 포함된 상수 값 등 저장

크게 위와 같이 나눌 수 있다.

출처) 

[[Java\] 많이 헷갈려하는 String constant pool과 Runtime Constant pool, Class file constant pool (tistory.com)](https://deveric.tistory.com/123)

[자바의 String과 Constant Pool (tistory.com)](https://jiwondev.tistory.com/114)

