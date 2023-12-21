---
categories : [Java]
tags : [String, StringBuffer, StringBuilder, Thread Safe]
---

자바의 문자열을 다루는 자료형으로는 대표적으로 3가지가 있다.

**String, StringBuffer, StringBuilder** 이다.

대부분 문자열에 대해 이야기할 때 String만을 취급하지만, 사용 목적에 따라 더 적합한 문자열 자료형이 있다.

위 3가지 자료형의 차이점에 대해 알아보고, 어떤 상황에 어떤 자료형이 좋은지 알아보자.

![](https://3553248446-files.gitbook.io/~/files/v0/b/gitbook-legacy-files/o/assets%2F-M5HOStxvx-Jr0fqZhyW%2F-MH65JVMk6LY89kolMcn%2F-MH66mfDushIpg_wA7pT%2F%E1%84%80%E1%85%B3%E1%84%85%E1%85%B5%E1%86%B71.png?alt=media&token=d42f44a3-aef8-4432-9951-6b5be0c606d7)

위 표를 참고하면 각 자료형의 차이점을 알 수 있다.

## String

- String은 Heap 메모리가 아닌 String 상수풀에 저장된다.
- String 상수풀은 Java8 이전에는 Permgen 영역에 할당되어 Method area에 위치하였다.
- Method area에는 영구적으로 보관되는 상수형 데이터(Static variable)들이 저정되었다.
- Java 8 이후에는 Permgen 영역이 제거되고 Metaspace영역으로 변경되면서 Perm 영역에 있던 정보들 중 static 변수나 String 상수풀은 Heap영역으로 이동하여 GC가 관리할 수 있도록 개선되었다.

아래는 String 클래스의 구현 코드이다.

```java
/**
 * Strings are objects which represent immutable arrays of characters.
 *
 * @author OTI
 * @version initial
 *
 * @see StringBuffer
 */
public final class String implements Serializable, Comparable<String>, CharSequence
{
	...
	private final char[] value;
	private final int count;
	private int hashCode;

	...

  /**
	 * Creates a string that is a copy of another string
	 *
	 * @param string
	 *          the String to copy
	 */
	public String(String string) {
		value = string.value;
		count = string.count;
		hashCode = string.hashCode;
	}
}
```

String은 final 즉 상수로 선언 되어있는 것을 알 수 있다. 이렇듯 String 인스턴스의 경우에는 immutable(불변)하다고 볼 수 있다.

여기서 자바에서 String 클래스를 써본 적 있는 사람이라면 위의 immutable하다는 것이 이해가 잘 안 될 것이다. 분명 코딩을 하면서 문자열을 수정하고, 조합한 기억이 있다고 말이다.

그것은 사실 해당 문자열을 수정하거나, 조합한 것이 아니다.

String을 수정하고 조합한다는 것은 정확히 말하면 **새로운 String 인스턴스를 생성**한다는 의미인 것이다.

```
[ 자바 언어에서 String을 불변으로 설정한 이유 ]

String 객체를 불변하게 설계한 이유는 캐싱, 보안, 동기화, 성능측면 이점을 얻기 위해서이다.

1. 캐싱 : String을 불변하게 함으로써 String pool에 각 리터럴 문자열의 하나만 저장하며 다시 사용하거나 캐싱에 이용가능하며 이로 인해 힙 공간을 절약할 수 있다는 장점이 있다. 

2. 보안 : 예를 들어 데이터베이스 사용자 이름, 암호는 데이터베이스 연결을 수신하기 위해 문자열로 전달되는데, 만일 번지수의 문자열 값이 변경이 가능하다면 해커가 참조 값을 변경하여 애플리케이션에 보안 문제를 일으킬 수 있다.

3. 동기화 : 불변함으로써 동시에 실행되는 여러 스레드에서 안정적이게 공유가 가능하다.
```

아래는 두 문자열을 조합하였을 경우 호출되는 String 생성자이다. 참고하자.

```java
/*
 * Creates a string that is s1 + s2.
 */
private String(String s1, String s2) {
	...
	value = new char[concatlen]; //new 연산으로 새로운 인스턴스 생성
	count = concatlen;

	System.arraycopy(s1.value, 0, value, 0, s1len);
	System.arraycopy(s2.value, 0, value, s1len, s2len);
}
```

## StringBuffer / StringBuilder

- 가변적이다. 즉, 상수가 아니다.
- Heap 메모리에 생성.
- StringBuffer = Thread Safe / StringBuilder != Thread Safe
- Thread Safe를 고려하지 않아 StringBuilder가 성능이 제일 우

## String Optimization(최적화)

Java 1.5 이상부터는 String 인스턴스에 문자열을 '+' 할 경우 컴파일러가 StringBuilder로 변환해주어 연산하는 것을 확인 할 수 있다.

즉,  아래와 같다.

```java
String a = "hello" + "world";
/* 는 아래와 같다. */
String a = new StringBuilder("hello").append("world").toString(); 
// StringBuilder를 통해 "hello" 문자열을 생성하고 "world"를 추가하고 toString()을 통해 String 객체로 변환하여 반환 
```

그럼 그냥 StringBuilder을 쓸 필요없이 String으로만 문자열을 다루면 되는거 아닌가? 라는 생각이 들 수 있다.

하지만 문자열을 합치는 일이 많아지면 메모리 효율이 떨어진다. 아래의 예시를 보자.

```java
String a = "";

for(int i = 0; i < 10000; i++) {
    a = a + i;
}

/* 위의 문자열 + 연산 식은 결국 아래와 같다. */
/* 즉, 매번 new StringBuilder() 객체 메모리를 생성하고 다시 변수에 대입하는 멍청한 짓거리를 하고 있는 것이다. */

String a = "";

for(int i = 0; i < 10000; i++) {
    a = new StringBuilder(b).append(i).toString();
}
```

위 예시처럼 매번 new 연산을 통해 인스턴스화 한다면 메모리 효율에 좋지 못하다.

그렇기 때문에 문자열의 연산이 많은 경우 처음부터 StringBuilder 클래스를 지정하는 것이 좋다.

```java
StringBuilder a = new StringBuilder();

for(int i = 0; i < 10000; i++) {
    a.append(i);
}

final String b = a.toString();
```

### 문자열 자료형 선택 결론

성능이 좋은 StringBuffer / StringBuilder를 사용하는 것이 좋다고 맹신할 수 있지만, 그건 상황에 따라 다르다.

StringBuffer / StringBuilder의 경우 buffer의 크기를 초기에 설정하는 작업이 필요한다. 이런 동작이 무거운 편에 속하고 문자열을 수정할 경우 buffer의 크기를 재설정 해야되는 경우도 있으므로 처리할 문자열이 많을수록 성능에 좋지 않다.

반면 String 클래스는 크기가 고정되므로, 단순 조회나 문자열을 크게 변경할 일이 없는 경우에는 String이 더 성능에 좋을 수 있다.

따라서, 보통의 경우 문자열에 대한 추가 작업이 많으면 StringBuffer / StringBuilder, 작업이 거의 없다면 String을 사용한다고 생각하자.

## Thread safe

표나 StringBuffer / StringBuilder 에 대한 특징을 보면 계속해서 나오는 단어가 있다.

Thread safe이다. 그렇다면 Thread safe는 무엇일까?

Multi-Thread 상황에서 함수, 변수, 객체에 동시 접근이 발생해도 프로그램 실행에 문제가 되지 않는것을 의미한다.

두 클래스는 문법이나 배열구성도 모두 같지만, **동기화(Synchronization)**에서의 지원의 유무가 다르다. 

java에서 synchronized 키워드는 여러개의 스레드가 한 개의 자원에 접근할려고 할 때, 현재 데이터를 사용하고 있는 스레드를 제외하고 나머지 스레드들이 데이터에 접근할 수 없도록 막는 역할을 수행한다.

예제를 보며 확인해보자.

아래의 예제는 StringBuilder와 StringBuffer 객체를 선언하고, 두개의 멀티 쓰레드를 돌려 StringBuilder와 StringBuffer 객체에 각각 1 요소를 1만번 추가하는(append) 로직을 수행한 코드이다.

두개의 쓰레드가 있고 한개의 쓰레드에서 배열요소를 1만번 추가하니 문자열 배열의 길이는 20000이 된다고 유추할 수 있다.

```java
import java.util.*;

public class Main extends Thread{
  public static void main(String[] args) {
    StringBuffer stringBuffer = new StringBuffer();
    StringBuilder stringBuilder = new StringBuilder();

    new Thread(() -> {
        for(int i=0; i<10000; i++) {
            stringBuffer.append(1);
            stringBuilder.append(1);
        }
    }).start();

    new Thread(() -> {
        for(int i=0; i<10000; i++) {
            stringBuffer.append(1);
            stringBuilder.append(1);
        }
    }).start();

    new Thread(() -> {
        try {
            Thread.sleep(2000);

            System.out.println("StringBuffer.length: "+ stringBuffer.length()); // thread safe 함
            System.out.println("StringBuilder.length: "+ stringBuilder.length()); // thread unsafe 함
        } catch (InterruptedException e) {
            e.printStackTrace();
        }
    }).start();
  }
}
```

```java
StringBuffer.length: 20000
StringBuilder.length: 19628
```

StringBuilder의 값이 더 작은 것을 볼 수 있는데, 이는 쓰레드들이 동시에 StringBuilder 클래스에 접근하여 동시에 append() 를 수행하다 몇번 씹혀서 제대로 수행이 안되어 일어난 결과라고 보면 된다.

StringBuilder는 Thread safe 하지 않아서 각기 쓰레드가 객체에 접근해서 변경을 하면 기다려주지 않기 때문에 이러한 현상이 발생한 것이다.

이와 달리 StringBuffer는 멀티 쓰레드(multi thread)환경에서, 한 쓰레드가 append() 를 수행하고 있을경우 다른 쓰레드가 append() 를 수행을 동시에 하지못하도록 잠시 대기를 시켜주고 순차적으로 실행하게 한다. 이처럼 동시에 접근해 다른 값을 변경하지 못하도록 하므로 Thread Safe로서 정상적으로 2만개의 배열요소가 추가된 것이다.

그래서 web이나 소켓환경과 같이 비동기로 동작하는 경우가 많을 때는 StringBuffer를 사용하는 것이 안전하다는 것을 알 수가 있다.

그렇기 때문에 현업에서는 왠만하면 안정적인 StringBuffer로 통일해서 코딩한다. (비록, StringBuilder 가 더 빠르지만 속도 차이는 미미하다.)



참조) 

[String, StringBuffer, StringBuilder - Incheol's TECH BLOG (gitbook.io)](https://incheol-jung.gitbook.io/docs/q-and-a/java/string-stringbuffer-stringbuilder)

[☕ 자바 String / StringBuffer / StringBuilder 차이점 & 성능 비교 (tistory.com)](https://inpa.tistory.com/entry/JAVA-☕-String-StringBuffer-StringBuilder-차이점-성능-비교)

