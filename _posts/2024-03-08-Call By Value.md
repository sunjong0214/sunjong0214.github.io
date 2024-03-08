---
title : Java에서는 항상 Call By Value다.
categories : [Java]
tags : [call by value, call by reference]
---

Call By Value와 Call By Reference는 함수의 호출 방법을 뜻한다.

## Call By Value

call by value는 값에 의한 호출을 말한다. call by value는 인자로 받은 값을 복사하여 처리한다.

- 장점 : 값을 복사하기 때문에 원본에 영향이 가지 않음, 안전함
- 단점 : 메모리 사용 증가

## Call By Reference

call by reference는 참조에 의한 호출을 말한다. call by reference는 인자로 받은 값의 주소를 직접 참조한다.

- 장점 : 복사하지 않고 직접 참조해 빠름
- 단점 : 원본에 영향이 감



|                      **call by value**                      |                    **call by reference**                     |
| :---------------------------------------------------------: | :----------------------------------------------------------: |
| 값에 의한 호출방식은 인자로 받은 값을 복사하여 처리를 한다. | 참조에 의한 호출방식은 인자로 받은 값의 주소를 참조하여 직접 값에 영향을 준다. |
|                 원래 값이 수정되지 않는다.                  |                    원래의 값이 수정된다.                     |
|                  변수의 복사본이 전달된다.                  |                    변수 자체가 전달된다.                     |
|          실제 인수가 다른 메모리 위치에 생성된다.           |           실제 인수가 같은 메모리 위치에 생성된다.           |



하지만 CS 이론에서 Call By Reference는 트렌드에 뒤쳐진 기술로 선호도가 굉장히 낮아졌다. 최신 언어에서는 더 이상 거의 사용되지 않는다.

특히 우리가 자주 사용하는 Java에서는 Call By Value만을 사용한다.



## Java에서의 Call By Value

- #### Primitive Type

```java
class Main {
    public static void main (String[] args) {
        int x = 10;
        add(x);
        System.out.println(x);
    }
    void add(int num) {
        num++;
    }
}
```

```java
// 출력
10
```

위의 예제의 경우 자바에서는 call by value를 사용하기 때문에 함수에 인자로 보낸 값은 아무리 add함수 내에서 여러 연산들을 진행해도 원본 값은 바뀌지 않는다.

자바의 primitive type은 메모리의 Stack 영역에 할당된다.

위 예제의 x는 다음과 같이 저장된다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FeA8zZF%2FbtsEkT9NHdl%2FLu75PIxhoHSUn8p0xq4DqK%2Fimg.png)

그 다음 add 메소드를 호출한다. 자바는 call by value를 사용하기 때문에 add에서 num 변수는 x 변수를 바탕으로 값을 복사하여 전달한다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fb3IXmq%2FbtsEjFxk3La%2FDea5ilYhtKXa7RwlXkIEDK%2Fimg.png)

만약 call by refernce 였다면 원본인 x의 값이 변하겠지만 자바는 call by value이기 때문에 원본은 그대로인 것을 확인할 수 있다.

- Reference Type

```java
class Main {
	public static void main(String[] args) {
		String[] strings = new String[2];
        strings[0] = "Hello";
        add(strings);
        
        System.out.println(strings[1]);
	}
    
    void add (String[] arrays) {
		arrays[1] = "Java";
    }
}
```

```java
// 출력
Java
```

자바는 call by value라고 했는데 왜 인자로 보낸 배열의 값이 바뀐 것일까?

그건 reference type의 경우 해당 참조값을 복사하여 전달하기 때문에 그렇다.

자바에서 참조 타입은 Heap 영역에 할당된다. 그리고 그 참조 타입을 가르키는 변수는 Stack 영역에 할당된다. 다음과 같다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcazcYx%2FbtsEmK496sE%2FlEKBq2cqWJN80ETcXXdb1K%2Fimg.png)

그 다음 arrays 변수는 strings에서 strings가 가지고 있는 그 참조값을 복사하여 값이 할당된다.

따라서 add 함수에서 arrays의 값을 변경하면 strings에게도 영향이 가는 것이다.

이 부분은 자바 call by reference로 동작하는 것이 아닌가 착각할 수 있다. 하지만 이는 같은 주소값을 가지는 변수가 주소값을 통해 접근해 주소값이 가리키는 내용을 변경하는 것이기 때문에 call by value라고 할 수 있다.

즉, 객체 자체를 복사하는 것이 아닌 객체의 주소만을 복사한다는 것이다. (call by reference는 객체를 복사함)

## 정리

자바 창시자인 제임스 고슬링도 자바에서는 "Call By Value"만을 참고했다고 한다. 누군가 자바에서도 Call By Reference가 있다고 하면 아니라고 말해주자.



출처)

[자바는 Call By Value(Pass By Value) 방식으로만 동작한다 - MangKyu's Diary (tistory.com)](https://mangkyu.tistory.com/322)

[자바가 언제나 Call By Value인 이유 (Call By Reference X)](https://loosie.tistory.com/486)

[Call by value와 Call by reference의 차이](https://yoojin99.github.io/cs/Call-by-Value-Call-by-Reference/)