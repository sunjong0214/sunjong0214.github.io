

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









참조) [ArrayList](https://st-lab.tistory.com/161)
