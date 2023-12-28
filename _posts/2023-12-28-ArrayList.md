---
title : ArrayList
categories : [자료구조]
tags : [ArrayList]
---



자바의 가장 기본적인 자료구조인 ArrayList를 구현해보자.

실제 Java에서 지원하 ArrayList의 경우 구현된 메소드가 30개 이상이라 모두 구현하기는 어렵고, 필수적인 메소드만 구현해보자.

앞서 만든 List 인터페이스를 implements 하여 구체적으로 구현해보자.



ArrayList는 다른 자료구조와 달리 Object[] 배열(객체 배열) 사용한다. 또 주의할 점이 모든 자료구조는 '동적 할등'을 전제로 한다. 마지막으로 리스트 계열 자료구조는 데이터 사이에 빈 공간을 허락하지 않는다.

예를 들어보자.

```java
Object[] a = {"a", "b", "c", "d"};
```

라는 배열이 있고, "c"라는 데이터를 삭제해보자.

그르면 a 배열의 상황은 다음과 같을 것이다.

```java
Object[] a = {"a", "b", null, "d"};
```

이렇게 데이터 사이에 빈 공간이 생길 경우 빈 공간을 없애줘야 한다. 즉, null 뒤에 있는 데이터들을 앞으로 한 칸 씩 끌고와야한다.

```java
Object[] a = {"a", "b", "d", null};
```

이런 식으로 리스트 계열 자료구조는 데이터들이 '연속되어' 있어야 한다.

## ArrayList 구현

### ArrayList 클래스 및 생성자 구성하기

첫째로 ArrayList이름으로 클래스를 생성 후 List 인터페이스를 implements 해주자.

```java
import Interface_form.List;
 
public class ArrayList<E> implements List<E> {
 
	private static final int DEFAULT_CAPACITY = 10;	// 최소(기본) 용적 크기 
	private static final Object[] EMPTY_ARRAY = {};	// 빈 배열
    
	private int size;	// 요소 개수 
 
	Object[] array;	// 요소를 담을 배열 
 
	// 생성자1 (초기 공간 할당 X)
	public ArrayList() {
		this.array = EMPTY_ARRAY;
		this.size = 0;
	}
 
	// 생성자2 (초기 공간 할당 O)
	public ArrayList(int capacity) {
		this.array = new Object[capacity];
		this.size = 0;
	}
}
```

변수에 대한 설명은 다음과 같다.



**DEFAULT_CAPACITY** : 배열이 생성 될 때의 최초 할당 크기(용적)이자 최소 할당 용적 변수로 기본값은 10으로 설정해두었다. (capacity가 용적이라는 의미다)

**EMPTY_ARRAY** : 아무 것도 없는 빈 배열 (용적이 0인 배열)

**size** : 배열에 담긴 요소(원소)의 개수 변수 (용적 크기가 아니다. 절대 헷갈리지 말자)

**array** : 요소들을 담을 배열

DEFAULT_CAPACITY와 EMPTY_ARRAY는 상수로 쓸 것이기 때문에 static final 키워드를 붙여준다.



또 생성자가 2개 있다.

생성자 1의 경우 사용자가 공간 할당을 미리 안하고 객체만 생성하고 싶을 때, 즉 다음과 같은 상태일 때이다.

```java
ArrayList<Integer> list = new ArrayList<>();
```

이 때는 사용자가 공간 할당을 명시하지 않았기 때문에 array 변수를 EMPTY_ARRAY로 초기화 시켜 놓는다.



반면에 사용자가 데이터의 개수를 예측할 수 있어서 미리 공간 할당을 해놓고 싶을 경우, 다음과 같이 생성할 경우

```java
ArrayList<Integer> list = new ArrayList<>(100);
```

array의 공간 할당을 입력 된 수 만큼 (예제에서는 array = new Object[100]) 배열을 만든다. 그리고 마찬가지로 size를 0으로 초기화 시켜 놓는다.

(size는 요소(원소)의 개수를 의미하는 것이다. 헷갈리지 말자)



## 동적할당을 위한 resize 메소드 구현

우리는 데이터의 갯수에 따라 '최적화'된 용적을 갖을 필요가 있다. 

만약 데이터는 적은데 용적이 크면 메모리가 낭비되고, 반대로 용적은 적은데 데이터가 많으면 넘치는 데이터들은 보관할 수 없게 되는 상황을 마주칠 수 있다.

그렇기 때문에 size(요소의 개수)가 용적(capacity)에 얼마만큼 차있는지 확인하고, 적절한 크기에 맞게 배열의 용적을 변경해야한다. 

또 용적은 외부에서 마음대로 접근하면 데이터의 손상을 야기할 수 있기 때문에 private로 접근을 제한하자.

```java
private void resize() {
	int array_capacity = array.length;
 
	// if array's capacity is 0
	if (Arrays.equals(array, EMPTY_ARRAY)) { // array와 EMPTY_ARRAY를 비교해 같은 경우. 즉, 생성자1로 생성한 경우
		array = new Object[DEFAULT_CAPACITY];
		return;
	}
 
	// 용량이 꽉 찰 경우  
	if (size == array_capacity) {
		int new_capacity = array_capacity * 2;
 
		// copy
		array = Arrays.copyOf(array, new_capacity);
		return;
	}
	// 용적의 절반 미만으로 요소가 차지하고 있을 경우 
	if (size < (array_capacity / 2)) {
		int new_capacity = array_capacity / 2;
 
		// copy
		array = Arrays.copyOf(array, Math.max(new_capacity, DEFAULT_CAPACITY));
		return;
	}
}
```

현재 array의 용적(= array 길이)과 데이터 개수(size)를 비교한다.



**조건문 1 :  if (Arrays.equals(array, EMPTY_ARRAY))** 

앞서 생성자에서 사용자가 용적을 별도로 설정하지 않은 경우 EMPTY_ARRAY로 초기화 되어있어 용적이 0인 상태다. 이 경우를 고려하여 이제 새로 array의 용적을 할당하기 위해 최소 용적으로 설정해두었던 DEFAULT_CAPACITY의 크기만큼 배열을 생성해주고 메소드를 종료한다.

또한 주소가 아닌 값을 비교해야 하기 때문에 Arrays.equals() 메소드를 사용하도록 하자.

**equals를 쓰는 이유**

내가 직접 구현할 때 들었던 의문이다. 왜 array_capacity == 0으로 조건을 설정하면 안될까?

그 이유는 아래에서 구현하는 clear() 메소드를 실행했을 때 문제가 발생하기 때문이다. clear() 메소드는 배열을 초기화하는 메소드인데, 이때 == 연산자를 쓰면 이미 늘어나있는 배열의 길이 때문에 resize가 원하는 만큼 되질 않는다. 그렇기 때문에 equals을 사용해 배열의 요소가 비었는지 확인하는 것이 좀 더 적절하다고 볼 수 있다.

**조건문 2 :  if (size == array_capacity)** 

데이터가 꽉 찰 경우에는 데이터(요소)를 더 받아오기 위해서 용적을 늘려야 한다. 즉, 데이터의 개수가 용적과 같을 경우는 꽉 차있는 경우를 말한다. 이 때는 새롭게 용적을 늘릴 필요가 있으므로 새로운 용적을 **현재 용적의 2배**로 설정하도록 한다. 

 

또한 기존에 담겨있던 요소들을 새롭게 복사해와야한다. 이 때 편리하게 쓸 수 있는 것이 Arrays.copyOf() 메소드다. Arrays.copyOf() 는 첫 번째 파라미터로 '복사할 배열'을 넣어주고, 두 번째 파라미터는 용적의 크기를 넣어주면 된다.

만약 복사할 배열보다 용적의 크기가 클 경우 먼저 배열을 복사한 뒤, 나머지 빈 공간은 null로 채워지기 때문에 편리하다.

 

**조건문 3 :  if (size < (array_capacity / 2 ))** 

데이터가 용적에 절반 미만으로 차지 않을 경우다. 이 경우 데이터는 적은데 빈 공간이 메모리를 차지하고 있어 불필요한 공간을 낭비할 뿐이다. 이럴 때에는 사이즈를 적적하게 줄여주는 것이 좋지 않겠는가?

데이터의 개수가 용적의 절반 미만이라면 용적도 절반으로 줄여주도록 하기 위해 새로운 용적(new_capacity)을 현재 용적의 절반으로 둔 뒤, Arrays.coptyOf() 메소드를 통해 새로운 용적의 배열을 생성해주도록 하자.

 

만약 복사할 배열보다 용적의 크기가 작을경우 새로운 용적까지만 복사하고 반환하기 때문에 예외발생에 대해 안전하다.



## add 메소드 구현

이제부터 ArrayList에 데이터를 추가할 수 있도록 해보자. 리스트 인터페이스에 있는 add() 메소드는 반드시 구현해야하는 추상메소드이기 때문에 Override(재정의)를 한다고 보면 된다.

add 메소드에는 크게 3가지로 분류할 수 있다.

- 가장 마지막 부분에 추가 (기본값) - addLast(E value)
- 가장 앞부분에 추가 - addFirst(E value)
- 특정 위치에 추가 - add(int index, E value)

물론 현재 자바에 내장되어있는 ArrayList에서는 addLast() 역할을 add() 메소드가하고, 특정 위치에 추가는 add(int index, E value) 로 오버로딩된 메소드가 하며, addFirst()는 없다. 하지만 편의상 3가지로 나누자.



#### 1. **기본삽입 : add(E value) & addLast(E value) 메소드**

먼저 add()의 기본 값은 addLast() 라고 했다. 가장 마지막 부분에만 추가하면 되다보니 이 부분 구현은 어렵지 않다. (자바 내부에서 add메소드는 boolean을 리턴하기 때문에 똑같이 구현했다.)

```java
@Override
public boolean add(E value) {
	addLast(value);
	return true;
}
 
public void addLast(E value) {
 
	// 꽉차있는 상태라면 용적 재할당
	if (size == array.length) {
		resize();
	}
	array[size] = value;	// 마지막 위치에 요소 추가
	size++;	// 사이즈 1 증가
}
```

add를 호출하면 자동으로 파라미터로 넘어온 value는 addLast로 보내진다. 그리고 데이터를 넣기 전 현재 용적이 꽉차있는지 검사한다.

만약 꽉차있다면 우리가 만들었던 resize() 메소드를 호출해서 array가 더 큰 용적을 갖도록 만들어 준다. 그리고 마지막 위치(size)에 value를 추가해주고 size를 1 증가시켜준다. 그림으로 보자.

![](https://blog.kakaocdn.net/dn/8nrqZ/btqLXThGutt/3kERf53Zvade8nysx9stU1/img.png)

size가 요소의 개수이고, index는 0부터 시작하다보니 array[size] = value; 를 해주면 된다. 이 부분은 많이들 헷갈려 잘못된 인덱스에 참조하는 경우가 많으니 주의하자.



#### 2. **중간삽입 : add(int index, E value)**

중간에 추가하는 것은 더 복잡하다. 우선 index가 범위를 벗어나지 않았는지 확인해야하고, 중간에 삽입할 경우 기존에 있던 index의 요소와 그 뒤에 있는 모든 데이터들을 한 칸씩 뒤로 밀어야된다. 그림을 통해 살펴보자.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FbJ6K1y%2FbtqL1pmCYgQ%2FKy9A4mgkqgnPMLy7thl1kK%2Fimg.png)

 즉, add에서 index로 들어오는 파라미터에 대해 만약 값이 3이 들어왔다면 원래 있던 배열에서 3을 포함한 뒤에있는 모든 요소들을 모두 한 칸씩 뒤로 옮긴 뒤에 3의 위치에 새로운 데이터를 삽입을 해주는 것이다.

addLast는 단순히 데이터를 추가하는 것이였다면 중간 삽입은 데이터를 미루는 코드가 추가된 것이다.

다음과 같다.

```java
@Override
public void add(int index, E value) {
 
	if (index > size || index < 0) {	// 영역을 벗어날 경우 예외 발생 
		throw new IndexOutOfBoundsException();
	}
	if (index == size) {	// index가 마지막 위치라면 addLast 메소드로 요소추가
		addLast(value);
	} 
	else {
			
		if(size == array.length) {	// 꽉차있다면 용적 재할당
			resize();
		}
			
		// index 기준 후자에 있는 모든 요소들 한 칸씩 뒤로 밀기
		for (int i = size; i > index; i--) {
			array[i] = array[i - 1];
		}
 
		array[index] = value;	// index 위치에 요소 할당
		size++;
	}
}
```

먼저 index로 들어오는 값이 size를 벗어나는지(빈공간을 허용하지 않으므로), 또는 음수가 들어오는지를 확인한다. 만약에 범위를 벗어나거나 인덱스가 음수가 들어오면 예외를 발생시키도록 한다.

그리고 사용자가 넘겨준 index가 size와 같다는 것은 결국 가장 마지막에 추가하는 것과 같은 의미이므로 이미 만들어두었던 addLast() 메소드로 가도록 한다.

그 외의 경우가 이제 중간에 삽입되는 경우다.

당연하게도 size가 현재 배열의 용적과 같다는 것은 이미 꽉차서 더이상 들어올 공간이 없다는 뜻이므로 resize()메소드를 호출해줌으로써 용적량을 늘리도록 한다. 그리고 그림에서 보듯이 index와 그 후방에 있는 데이터들을 한 칸씩 뒤로 밀어야 하므로 반복문을 통해 뒤로 밀어주도록 한 뒤 array[index] 에는 새로운 요소로 교체해주도록 한다.

한 칸씩 뒤로 미는 반복문에서 왜 index가 아닌 size에서부터 시작하는지 궁금할 수 있다. 그 이유는를 알아보자. index로 시작할 경우 반복문 안에 array[i] = array[i - 1]; -> array[i] = array[i+1];이 될 것이다. 근데 +1로 처리할 경우 i+1의 값이 배열의 크기(size)보다 커지는 경우가 발생하며 따로 조건문으로 처리하지 않는다면, IndexOutOfBoundsExecption이 발생한다. 그렇기 때문에 최대값에서 인덱스를 줄여가며 뒤로 밀어주는 것이 조건문을 한번 더 거치지 않아도 되서 효율적이다.



#### 3. addFirst(E value)

이 부분은 간단하다. 생각해보면 기존 데이터가 있다면 어처피 모든 데이터들을 뒤로 밀어야하는 것이다. 즉, 앞서 만들었던 중간 삽입에서 index를 0으로 보내면 된다.

```java
public void addFirst(E value) {
    add(0, value);
}
```



이렇게 기본적인 요소 추가 메소드들을 만들었다. 그리고 만들다보면 size와 index참조 부분을 헷갈려서 배열에서 잘못된 참조를 하는 경우가 많다. 항상 이 부분을 주의자.



#### **[add 메소드 묶음]**

```java
@Override
public boolean add(E value) {
	addLa4st(value);
	return true;
}
 
public void addLast(E value) {
	if (size == array.length) {
		resize();
	}
	array[size] = value;
	size++;
}
 
@Override
public void add(int index, E value) {
	if (index > size || index < 0) {
		throw new IndexOutOfBoundsException();
	}
	if (index == size) {
		addLast(value);
	} 
	else {
			
		if(size == array.length) {
			resize();
		}
			
		for (int i = size; i > index; i--) {
			array[i] = array[i - 1];
		}
		array[index] = value;
		size++;
	}
}
 
public void addFirst(E value) {
	add(0, value);
}
```



## get, set, indexOf, contains 메소드 구현

#### 1. **get(int index) 메소드**

get()은 index로 들어오는 값을 인덱스삼아 해당 위치에 있는 요소로 반환하는 메소드다. 배열의 위치를 찾아가는 것이기 때문에 **반드시 잘못된 위치 참조에 대한 예외처리를 해주어야 한다.**

```java
@SuppressWarnings("unchecked")
@Override
public E get(int index) {
	if(index >= size || index < 0) {	// 범위 벗어나면 예외 발생
		throw new IndexOutOfBoundsException();
	}
	// Object 타입에서 E타입으로 캐스팅 후 반환
	return (E) array[index];
}
```

여기서 @SuppressWarnings("unchecked") 에 대해 잠깐 언급하고 가겠다.

@SuppressWarnings("unchecked")을 붙이지 않으면 type safe(타입 안정성)에 대해 경고를 보낸다. 반환되는 것을 보면 E 타입으로 캐스팅을 하고 있고 그 대상이 되는 것은 Object[] 배열의 Object 데이터다. 즉, Object -> E 타입으로 변환을 하는 것인데 이 과정에서 변환할 수 없는 타입을 가능성이 있다는 경고로 메소드 옆에 경고표시가 뜨는데, 우리가 add하여 받아들이는 데이터 타입은 유일하게 E 타입만 존재한다. 

그렇기 때문에 형 안정성이 보장된다. 한마디로 ClassCastException이 뜨지 않으니 이 경고들을 무시하겠다는 것이 @SuppressWarnings("unchecked") 이다. 물론 절대 남발하면 안되고, 형 변환시 예외 가능성이 없을 확실한 경우에 최소한의 범위에서 써주는 것이 좋다. 그렇지 않으면 중요한 경고 메세지를 놓칠 수도 있기 때문이다.



#### 2. set(int index, E value) 메소드

set 메소드는 기존에 index에 위치한 데이터를 새로운 데이터(value)으로 교체하는 것이다. add 메소드는 데이터 '추가'인 반면에 set은 '교체'라는 점을 기억해두도록 하자.

결과적으로 index에 위치한 데이터를 교체하는 것이기 때문에 get이랑 메소드가 유사하다. 다만 get은 해당 인덱스의 값을 반환하는 것이였다면 set은 데이터만 교체해주면 된다.

```java
@Override
public void set(int index, E value) {
	if (index >= size || index < 0) {	// 범위를 벗어날 경우 예외 발생
		throw new IndexOutOfBoundsException();
	} 
	else {
		// 해당 위치의 요소를 교체
		array[index] = value;
	}
}
```

마찬가지로 잘못된 인덱스를 참조하고 있진 않은지 반드시 검사가 필요하다. 그냥 index로 참조하는 모든 메소드들은 반드시 검사 필요.



#### 3. indexOf(Object value) 메소드

indexOf 메소드는 사용자가 찾고자 하는 요소(value)의 **'위치'**를 반환하는 메소드다.

그러면 이러한 질문이 들어올 수 있다. "찾고자 하는 요소가 중복된다면 어떻게 반환해야하나요?" 그 답은 **가장 먼저 마주치는 요소의 인덱스를 반환**한다는 것이다.(실제 자바에서 똑같다.)

찾고자 하는 요소가 없다면 -1 을 반환한다.

그리고 중요한 점은 **객체끼리 비교할 때는 동등연산자(==)가 아니라 반드시 .equals()로 비교**해야한다. 객체끼리 비교할 때 동등연산자를 쓰면 값을 비교하는 것이 아닌 주소를 비교하는 것이기 때문에 잘못된 결과가 나온다.

```java
@Override
public int indexOf(Object value) {
	int i = 0;
    
	// value와 같은 객체(요소 값)일 경우 i(위치) 반환
	for (i = 0; i < size; i++) {
		if (array[i].equals(value)) {
			return i;
		}
	}
	// 일치하는 것이 없을경우 -1을 반환
	return -1;
}
```



#### 3-1. LastindexOf(Object value) 메소드

indexOf 메소드는 index가 0부터 시작했다면 거꾸로 탐색하는 과정도 있으면 좋다. 예를들어 사용자가 찾고자 하는 인덱스가 뒤 쪽으로 예상 가능할 때 굳이 앞에서부터 찾아 줄 필요가 없다. 또한 이후에 구현 할 Stack에서도 이용 가능하다.

```java
@Override
public int lastindexOf(Object value) {
    for(int i=size-1; i >= 0; i--) {
        if(array[i].equals(value)) {
            return i;
        }
    }
    return -1;
}
```



#### 4. contains(Object value) 메소드

indexOf 메소드가 사용자가 찾고자 하는 요소(value)의 '위치(index)'를 반환하는 메소드였다면, contains는 사용자가 찾고자 하는 요소(value)가 존재 하는지 안 하는지를 반환하는 메소드다.

찾고자 하는 요소가 존재하면 true를, 존재하지 않는다면 false를 반환한다.

음? 그러면 indexOf와 기능이 비슷하니깐 이를 쓸 수 있을 것 같은데? 라는 생각이 들었다면 매우 정답이다.

어차피 해당 요소가 존재하는지를 '검사'한다는 기능은 같기 때문에 indexOf 메소드를 이용하여 만약 음수가 아닌 수가 반환되었다면 요소가 존재한다는 뜻이고, 음수(-1)이 나왔다면 요소가 존재하지 않는다는 뜻이다. 즉, 아래와 같이 contains 메소드를 만들 수 있다.

```java
@Override
public boolean contains(Object value) {
    
    // 0 이상이면 요소가 존재한다는 뜻
    if(indexOf(value) >= 0) {
        return true;
    }
    else {
        return false;
    }
}
```

이런 식으로 서로의 메소드를 연결해서 사용한다면 좀 더 직관적이고 이해가 쉽다. 연습하자.

#### get, set, indexOf, contains 메소드 묶어보기

```java
@SuppressWarnings("unchecked")
@Override
public E get(int index) {
	if(index >= size || index < 0) {
		throw new IndexOutOfBoundsException();
	}
	return (E) array[index];
}
 
 
@Override
public void set(int index, E value) {
	if (index >= size || index < 0) {
		throw new IndexOutOfBoundsException();
	} else {
		array[index] = value;
	}
}
	
	
@Override
public int indexOf(Object value) {
	int i = 0;
	for (i = 0; i < size; i++) {
		if (array[i].equals(value)) {
			return i;
		}
	}
	return -1;
}
 
 
public int lastIndexOf(Object value) {
	for(int i = size - 1; i >= 0; i--) {
		if(array[i].equals(value)) {
			return i;
		}
	}
	return -1;
}
 
	
@Override
public boolean contains(Object value) {
	if(indexOf(value) >= 0) {
		return true;
	}
	else {
		return false;
	}
}
```



## remove 메소드 구현

remove 메소드는 사람들이 가장 어려워하고, 가장 많이 에러가 나는 부분이다. 차근차근 공부해보자.

remove 메소드의 경우 크게 2가지로 나눌 수 있다.

- **특정 index의 요소를 삭제 - remove(int index)**
- **특정 요소를 삭제 - remove(Object value)**

물론 자바에 내장되어있는 ArrayList도 remove() 요소는 없다. remove(int index)와 remove(Object value) 메소드만 존재한다. 하지만 이후에 다룰 Stack 이나 LinkedList, Queue 등 다양한 자료구조들은 remove() 메소드가 존재하니 공부해두자.

만약 remove() 기능을 넣어 가장 앞 또는 뒤의 요소를 제거하는 기능을 넣고 싶다면 밑에서 다룰 remove(int index)을 호출하여 파라미터로 0 또는 size-1을 넘겨주면 된다.



#### 1. remove(int index) 메소드

remove(int index) 는 '특정 위치에 있는 요소를 제거'하는 것이다.

앞서 **add(int index, E value)를 했던 방식을 거꾸로 하면 된다는 의미**다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FwCmYA%2FbtqL39iXzlO%2FU50Vsl00hJ2DE7p2Bc6dKK%2Fimg.png)

**코드**

```java
@SuppressWarnings("unchecked")
@Override
public E remove(int index) {
 
	if (index >= size || index < 0) {
		throw new IndexOutOfBoundsException();
	}
 
	E element = (E) array[index];	// 삭제될 요소를 반환하기 위해 임시로 담아둠, 원본 데이터 타입으로 반환하기 위해 E 타입으로 캐스팅
	array[index] = null;
    
	// 삭제한 요소의 뒤에 있는 모든 요소들을 한 칸씩 당겨옴
	for (int i = index; i < size - 1; i++) {
		array[i] = array[i + 1];
		array[i + 1] = null;
	}
	size--; // 요소를 하나 삭제했으니 size를 줄여준다.
	resize(); // 삭제되면서 데이터가 일정 이상 비워진 경우 용적을 줄이기 위해 추가
	return element;
}
```

주의할 점은 항상 마지막 원소의 인덱스는 size보다 1 작다. 그렇기 때문에 범위 체크와, 이후의 배열 요소들을 한 칸씩 당겨올 때 시작점과 끝 점을 잘 생각해야한다.

또 명시적으로 요소를 null로 처리해주어야 GC에 의해 처리가 되기 때문에 최대한 null로 처리하자. (물론 명시적으로 처리 안 해도 크게 무제는 없지만 GC가 쓰지 않는 데이터를 참조될 가능성 있는 데이터로 볼 수 있다. 메모리 및 프로그램 성능에 영향이 있을 수 있다.)



#### 2. remove(Object value) 메소드

remove(Object value) 메소드는 사용자가 원하는 특정 요소(value)를 찾아서 삭제하는 것이다. indexOf와 마찬가지로 중복 요소일 경우 처음 발견한 요소를 삭제한다.

이 메소드에서 필요한 동작은 1) value와 같은 요소가 존재하는지, 존재한다면 어디에 존재하는지 2) 그 index의 데이터를 지우고 나머지 뒤의 요소를 하나씩 당기는 작업. 이렇게 두가지 동작이 필요하다.

그렇다면 우리는 위에서 작성한 메소드를 재사용한다면 쉽게 작성할 수 있다.

1)indexOf,() 2)remove(int index) 이 두개의 메소드를 사용해서 작성하자.

```java
@Override
public boolean remove (Object value) {
    
    // 삭제하고자 하는 요소의 인덱스 찾기
    int index = indexOf(value);
    
    // -1이라면 array에 요소가 없다는 의미이므로 false 반환
    if(index == -1) {
        return false;
    }
    
    // index 위치에 있는 요소를 삭제
    remove(index);
    return true;
}
```



#### remove 메소드 묶어보기

```java
@SuppressWarnings("unchecked")
@Override
public E remove(int index) {
 
	if (index >= size || index < 0) {
		throw new IndexOutOfBoundsException();
	}
 
	E element = (E) array[index];
	array[index] = null;
	for (int i = index; i < size - 1; i++) {
		array[i] = array[i + 1];
		array[i + 1] = null;
	}
	size--;
	resize();
	return element;
}
 
 
@Override
public boolean remove(Object value) {
	int index = indexOf(value);
	if (index == -1) {
		return false;
	}
 
	remove(index);
	return true;
}
```



## size, isEmpty, clear 메소드 구현

#### 1. size() 메소드

ArrayList가 동적으로 할당되며 여러번 메소드가 실행되다보면 리스트에 담긴 요소의 개수가 몇 개인지 기억하기 힘들고, ArrayList에서 size 변수는 private로 캡슐화 되어있기 때문에 size 변수의 값을 반환해주는 size() 메소드를 만들 필요가 있다. (캡슐화의 이유는 사용자가 size 변수의 값을 고의적으로 데이터 조작할 수 있기 때문이다.)

```java
@Override
public int size() {
    return size;
}
```



#### 2. isEmpty() 메소드

isEmpty() 메소드는 현재 ArrayList에 요소가 비어있는지를 알려준다.

비었을 경우 true, 아니면 false를 반환한다. 즉, size == 0이면 true, size != 0 이면 false 라는 것이다. 굳이 배열을 순회할 필요가 없다.

```java
@Override
public boolean isEmpty() {
    return size == 0; // 조건문을 반환할 경우 true, false를 반환
}
```



#### 3. clear() 메소드

clear의 경우 모든 요소를 비워버리는 메소드다. 즉, 모든 요소를 초기화 해준다. 요소 뿐만 아니라 size 도 0으로 초기화 해주고, 배열의 용적 또한 현재 용적의 절반으로 줄일 수 있다.

배열의 용적을 초기값이 아닌 절반만 줄이는 이유는 무엇일까?

물론 초기값으로 초기화해도 되지만 결국 전 배열에서 사용한 데이터 용적과 근접하는 데이터가 들어올 확률이 높기 때문이다. 

```java
@Override
public void clear() {
    
    // 모든 공간을 null로 처리
    for (int i=0; i<size; i++) {
        array[i] = null;
    }
    size = 0;
    resize();
}
```



#### size, isEmpty, clear 메소드 묶어보기

```java
@Override
public int size() {
	return size;
}
 
@Override
public boolean isEmpty() {
	return size == 0;
}
 
@Override
public void clear() {
	for (int i = 0; i < size; i++) {
		array[i] = null;
	}
	size = 0;
	resize();
}
```



## clone, toArray 메소드 구현

clone은 기존에 있던 객체를 파괴하지 않고 요소들이 동일한 객체를 새로 하나 만드는 것이다. 그리고 toArray는 리스트를 출력할 때 for-each문을 쓰기 위해 만들어보았다.

중요한 부분은 아니지만 한번 살펴보자.



#### 1. clone() 메소

사용자가 사용하던 ArrayList를 새로 하나 복제하는 메소드다. 단순히 = 연산자로 객체를 복사하게 되면 '주소'를 복사하는 것이기 때문에 복사한 객체에서 데이터를 조작할 경우 원복 객체까지 영향을 미친다. 즉 얕은 복사(shallow copy)가 된다는 것이다.

```java
@Override
public Object clone() throws CloneNotSupportedException {
 
	// 새로운 객체 생성
	ArrayList<?> cloneList = (ArrayList<?>)super.clone();
 
	// 새로운 객체의 배열도 생성해주어야 함 (객체는 얕은복사가 되기 때문)
	cloneList.array = new Object[size];
 
	// 배열의 값을 복사함
	System.arraycopy(array, 0, cloneList.array, 0, size);
 
	return cloneList;
}
```

clone()의 경우 Object에 있는 메소드이지만 접근제어자가 protected로 되어있어 우리가 만든 것처럼 사용자 클래스의 경우 Cloneable 인터페이스를 추가로 implements 해줘야한다.

즉, public class ArrayList< E > implements List< E >에 Cloneable도 추가해야한다.

그래도 설명을 조금 하자면, super.clone() 자체가 생성자 비슷한 역할이고 shallow copy를 통해 사실상 new ArrayList() 를 호출하는 격이라 제대로 완벽하게 복제하려면 clone한 리스트의 array 또한 새로 생성해서 해당 배열에 copy를 해주어야 한다.



#### 2. toArray() 메소드

toArray()에는 크게 두 가지가 있다. 1) ArrayList의 리스트를 객체배열(Object[])로 반환해주는 Object[] toArray() 메소드가 있고, 2) ArrayList를 이미 생성  된 다른 배열에 복사해주고자 할 때 쓰는 T[] ArrayList(T[] a) 메소드가 있다.

```java
ArrayList<Integer> list = new ArrayList<>();
 
// get list to array (using toArray())
Object[] array1 = list.toArray();
 
// get list to array (using toArray(T[] a)
Integer[] array2 = new Integer[10];
array2 = list.toArray(array2);
```

1번의 장점은 ArrayList에 있는 요소의 수만큼 정확하게 배열의 크기가 할당되어 반환된다는 점이고,

2번의 장점은 객체 클래스로 상속관계에 있는 타입이거나 Wrapper(Integer -> int) 같이 데이터 타입을 유연하게 캐스팅할 여지가 있다는 것이다. 또한 리스트에 원소 5개가 있고, array2에 10개의 원소가 있다면 array2에 0~4 index에 리스트에 있던 원소가 복사되고, 그 외의 원소는 기존 array2배열에 초기화 되어있던 원소가 그대로 남는다.

구현은 다음과 같다.

```java
public Object[] toArray() {
	return Arrays.copyOf(array, size);
}
	
 
@SuppressWarnings("unchecked")
public <T> T[] toArray(T[] a) {
	if (a.length < size) {
		// copyOf(원본 배열, 복사할 길이, Class<? extends T[]> 타입)
		return (T[]) Arrays.copyOf(array, size, a.getClass());
	}
	// 원본배열, 원본배열 시작위치, 복사할 배열, 복사할배열 시작위치, 복사할 요소 수 
	System.arraycopy(array, 0, a, 0, size);
	return a;
}
```

Object[] toArray() 메소드의 경우 원본배열(array)를 요소 개수(size)만큼 복사하여 Object[] 배열을 반환해주는 메소드다. 이 것을 그대로 써서 반환해주면 되기 때문에 크게 어렵지 않다.

 

두 번째 T[] toArray(T[] a) 메소드를 보자.

이 부분은 제네릭 메소드로, 우리가 만들었던 ArrayList의 E타입하고는 다른 제네릭이다. 예로들어 E Type이 Integer라고 가정하고, T타입은 Object 라고 해보자.

Object는 Integer보다 상위 타입으로, Object 안에 Integer 타입의 데이터를 담을 수도 있다. 이 외에도 사용자가 만든 부모, 자식 클래스 같이 상속관계에 있는 클래스들에서 하위 타입이 상위 타입으로 데이터를 받고 싶을 때 쓸 수 있도록 하기 위함이다.

즉, 상위타입으로 들어오는 객체에 대해서도 데이터를 담을 수 있도록 별도의 제네릭메소드를 구성하는 것이다.

그리고, 들어오는 배열(a)가 현재 array의 요소 개수(size)보다 작으면 size에 맞게 a의 공간을 재할당 하면서 array에 있던 모든 요소를 복사한다.

쉽게 이해해보면 ArrayList의 array의 요소가 4개 {1, 2, 3, 4} 있다고 치자. 그리고 Object[] copy = new Object[1]라는 배열을 하나 만들었는데, 공간이 한 개 밖에 없다.

그러면 array의 요소 1개만 복사하는 것이 아니라 copy 배열의 사이즈가 1에서 4로 증가하여 copy배열에 {1, 2, 3, 4}가 모두 담기게 되는 것이다.

또한 앞서 상위타입에 대해서도 담을 수 있도록 하기 위해 copyOf메소드에서 Class라는 파라미터를 마지막에 넣어준다.(a.getClass()) 그런다음 Object[] 배열로 리턴 된 것을 T[] 타입으로 캐스팅하여 반환해주면 된다.

반대로 파라미터로 들어오는 배열의 크기가 현재 ArrayList에 있는 array보다 크다면 앞 부분부터 array에 있던 요소만 복사해서 a배열에 넣고 반환해주면 된다.



## 정리

ArrayList는 보통 index를 통해 요소에 접근하는 작업이 매우 빠르고, 반대로 중간 삽입, 삭제의 경우 비효율적이다

![](https://blog.kakaocdn.net/dn/c47wrr/btqNG0s9sD1/GE9KaZbmsXUbPKVzOkon20/img.png)

앞으로 구현할 LinkedList와의 차이점이다.

서로 장단점이 뚜렷해 상황에 맞게 각 자료구조를 사용하면 된다.



참조) [ArrayList](https://st-lab.tistory.com/161)
