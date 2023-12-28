---
title : List Interface
categories : [자료구조]
tags : [List Interface]
---

배열하고 List 인터페이스에서 지원하는 클래스(ex) ArrayList, LinkedList)의 공통점과 차이점이 무엇인지 간단하게 알아보자.

**[공통점]**

1. 동일한 특성의 데이터들을 묶는다.
2. 반복문(loop)내에 변수를 이용하여 하나의 묶음 데이터들을 모두 접근할 수 있다.

**[차이점 - 배열]**

1. 처음 선언한 배열의 크기(길이)는 변경할 수 없다. 이를 정적 할당(static allocation)이라고 한다.
2. 메모리에 연속적으로 나열되어 할당된다.
3. index에 위치한 하나의 데이터(element)를 삭제하더라도 해당 index에는 빈공간으로 계속 남는다. 

**[차이점 - 리스트]**

1. 리스트의 길이가 가변적이다. 이를 동적 할당(dynamic allocation)이라고 한다.
2. 데이터들이 연속적으로 나열된다. (메모리에 연속적으로 나열되지 않고 각 데이터들은 주소(reference)로 연결되어있다. C에서의 포인터라고 생각하면 된다.)
3. 데이터(element) 사이에 빈 공간을 허용하지 않는다.



장단점도 알아보자.

**[배열의 장단점]**

<장점>

1. 데이터 크기가 정해져있을 경우 메모리 관리가 편하다.
2. 메모리에 연속적으로 나열되어 할당하기 때문에 index를 통한 색인(접근)속도가 빠르다.

<단점>

1. 배열의 크기를 변경할 수 없기 때문에 초기에 너무 큰 크기로 설정해주었을 경우 메모리 낭비가 심해지고, 반대로 너무 작은 크기로 설정해주었을 경우 데이터를 다 못담을 수 있는 경우가 발생 할 수 있다.
2. 데이터를 삽입(add), 삭제(remove)를 할 때 빈 공간을 허용하지 않고자 한다면, 뒤의 데이터들을 모두 밀어내거나 당여주어야 하기 때문에 속도가 느려 삽입, 삭제가 빈번한 경우에는 유용하지 않다.



**[리스트의 장단점]**

<장점>

1. 데이터의 개수에 따라 해당 개수만큼 메모리를 동적 할당해주기 때문에 메모리 관리가 편리해진다.
2. 빈 공간을 허용하지 않기 때문에 데이터 관리에도 편하다.
3. 포인터(주소)로 각 데이터들이 연결되어 있기 때문에 해당 데이터에 연결된 주소만 바꿔주면 되기 때문에 삽입 삭제에 용이하다.(ArrayList는 예외)

<단점>

1. 객체로 데이터를 다루기 때문에 적은양의 데이터만 쓸 경우 배열에 비해 차지하는 메모리가 커진다.

   간단히 예로들어 primitive type인 Int는 4Byte를 차지한다. 반면에 Wraaper class인 Integer는 32bit JVM에선 객체의 헤더(8Byte), 원시 필드(4Byte), 패딩(4Byte)으로 '최소 16Byte를 차지한다. 거기에다가 이러한 객체데이터들을 다시 주소로 연결하기 때문에 16 + α 가 된다.

2. 기본적으로 주소를 기반으로 구성되어있고 메모리에 순차적으로 할당하는 것이 아니기 때문에(물리적 주소가 순차적이지 않다) 색인(검색)능력은 떨어진다.



ArrayList와 LinkList도 이 둘의 장단점이 다른데 다음에 알아보자.

List 인터페이스에 구현된 몇가지 자주 사용하는 메소드들을 살펴보자.



**<List Interface에 선언된 대표적인 메소드>**

![](https://blog.kakaocdn.net/dn/bD25oy/btqKjcJw8ME/MhhaKCzEFHOQR3wqXruzYK/img.png)

실제로는 더 많은 추상메소드들이 있는데, 자세한 내용은 아래 Java API 에서 잘 설명해주고 있으니 한 번 확인해보는 것도 앞으로 구현하는데 도움이 될 것이다.

https://docs.oracle.com/en/java/javase/13/docs/api/java.base/java/util/ArrayList.html



## List Interface 구현

st-lab님의 블로그의 코드를 참고하여 만들어보자. 아래는 st-lab님의 코드이다.

```java
package Interface_form;
 
/**
 * 
 * 자바 List Interface입니다. <br>
 * List는 ArrayList, SinglyLinkedList, DoublyLinked에 의해 각각 구현됩니다.
 * 
 * @author st_lab
 * @param <E> the type of elements in this list
 *
 * @version 1.0
 * 
 */
 
public interface List<E> {
 
	/**
	 * 리스트에 요소를 추가합니다.
	 * 
	 * @param value 리스트에 추가할 요소
	 * @return 리스트에서 중복을 허용하지 않을 경우에 리스트에 이미 중복되는 
	 *         요소가 있을 경우 {@code false}를 반환하고, 중복되는 원소가
	 *         없을경우 {@code true}를 반환
	 */
	boolean add(E value);
 
	/**
	 * 리스트에 요소를 특정 위치에 추가합니다. 
	 * 특정 위치 및 이후의 요소들은 한 칸씩 뒤로 밀립니다.
	 * 
	 * @param index 리스트에 요소를 추가할 특정 위치 변수
	 * @param value 리스트에 추가할 요소
	 */
	void add(int index, E value);
 
	/**
	 * 리스트의 index 위치에 있는 요소를 삭제합니다.
	 * 
	 * @param index 리스트에서 삭제 할 위치 변수
	 * @return 삭제된 요소를 반환
	 */
	E remove(int index);
 
	/**
	 * 리스트에서 특정 요소를 삭제합니다. 동일한 요소가 
	 * 여러 개일 경우 가장 처음 발견한 요소만 삭제됩니다.
	 * 
	 * @param value 리스트에서 삭제할 요소
	 * @return 리스트에 삭제할 요소가 없거나 삭제에 실패할 
	 *         경우 {@code false}를 반환하고 삭제에 성공할 경우 {@code true}를 반환 
	 */
	boolean remove(Object value);
 
	/**
	 * 리스트에 있는 특정 위치의 요소를 반환합니다.
	 * 
	 * @param index 리스트에 접근할 위치 변수 
	 * @return 리스트의 index 위치에 있는 요소 반환 
	 */
	E get(int index);
 
	/**
	 * 리스트에서 특정 위치에 있는 요소를 새 요소로 대체합니다.
	 * 
	 * @param index 리스트에 접근할 위치 변수 
	 * @param value 새로 대체할 요소 변수 
	 */
	void set(int index, E value);
 
	/**
	 * 리스트에 특정 요소가 리스트에 있는지 여부를 확인합니다.
	 * 
	 * @param value 리스트에서 찾을 특정 요소 변수 
	 * @return 리스트에 특정 요소가 존재할 경우 {@code true}, 존재하지 않을 경우 {@code false}를 반환  
	 */
	boolean contains(Object value);
 
	/**
	 * 리스트에 특정 요소가 몇 번째 위치에 있는지를 반환합니다.
	 * 
	 * @param value 리스트에서 위치를 찾을 요소 변수  
	 * @return 리스트에서 처음으로 요소와 일치하는 위치를 반환.
	 *         만약 일치하는 요소가 없을경우 -1 을 반환 
	 */
	int indexOf(Object value);
 
	/**
	 * 리스트에 있는 요소의 개수를 반환합니다.
	 * 
	 * @return 리스트에 있는 요소 개수를 반환  
	 */
	int size();
 
	/**
	 * 리스트에 요소가 비어있는지를 반환합니다.
	 * @return 리스트에 요소가 없을경우 {@code true}, 요소가 있을경우 {@code false}를 반환 
	 */
	boolean isEmpty();
 
	/**
	 * 리스트에 있는 요소를 모두 삭제합니다.
	 */
	public void clear();
 
}
```

자료구조를 구현할 때 규칙을 몇개 정하자.

1. 제네릭(Generic)은 한 개일 경우 E, 두 개일 경우 E와 T로 표현할 것이다. 보통 많이 쓰이는 제네릭 이름은 T, K, V, E가 많기 때문에 필자도 대표적인 E와 T로 쓰고자 한다.

 

2. 최대한 Java API 문서에 나와있는 제공되는 메소드들과 유사하게 만들 것이다. 기준은 아래 링크에서 보면 된다. [docs.oracle.com/en/java/javase/11/docs/api/index.html](https://docs.oracle.com/en/java/javase/11/docs/api/index.html)

 

3. 기본적으로 여러분들이 재정의(Override), 예외처리(throw), 객체(Object), 인터페이스(nterface), 제네릭(Generic) 이렇게 5개에 대한 기본 지식은 있다는 전제하에 설명 할 것이다. 그렇지 않고 하나하나 설명하다보면 길이 너무 길어져 핵심을 못짚게 되어 결정했다.

## 정리

List Interface와 배열의 차이와 장단점 등을 알아보았다.

st-lab님의 블로그를 참고하여 작성한 글이다. st-lab님의 블로그에 들어가면 여러 자료구조에 대해 잘 설명된 글이 있으니 그것을 따라가며 자료구조에 대해 공부해보자.

무작정 코드를 따라치기보다는 먼저 혼자 힘으로 자료구조에 있는 메소드 등을 구현해 보고 그 다음 모르는 부분을 참고하는 방식으로 따라해보자.

- 내가 만든 List Interface

  [List Interface](https://github.com/sunjong0214/DataStructure/blob/main/Interface_form/List.java)



참조) [List Interface](https://st-lab.tistory.com/146)