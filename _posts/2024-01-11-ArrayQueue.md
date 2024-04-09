---
title : ArrayQueue
categories : [자료구조]
tags : [queue, 자료구조]
---

Queue는 기본적으로 **'선입선출'**인 자료구조이다. 예를 들어 대기열, 놀이공원에서 기구를 기다릴 때 서 있는 대기 줄을 생각하면 된다.

큐는 대표적으로 시간 순으로 어떤 작업 또는 데이터를 처리할 필요가 있을 때 사용된다. 또 알고리즘인 BFS(너비 우선 탐색)에 이용된다고 한다.

Queue는 먼저 들어온 것이 먼저 나간다고 했다. 그럼 배열을 이용해 Queue를 구현했다고 가정하자.

아무런 장치 없이 Queue를 구현한 배열은 추가할때마다 무한히 늘어날 것이다. 그리고 먼저 들어온 값이 먼저 나가기 때문에 앞 쪽 인덱스는 빠져나간 뒤 남아있는 빈 배열만 덩그러니 남아있을 것이다.

![](https://blog.kakaocdn.net/dn/bqtFBm/btqPm90RYfC/k5Rcvg5ayAfkKs0rEqaLc0/img.png) 

그림과 같이 말이다.

이런 문제를 해결하기 위해 우리는 배열을 원형으로 만들어주면 된다.

만약 그림에서 같이 0 ~ n 까지 비어있다고 했을 때, 새로 추가된 값은 0부터 채워나가는 것이다. 

위에 같이 구현할려면 지금 남이있는 배열의 첫 시작과 끝을 나타내는 변수가 필요하다.

그 변수명은 시작을 front, 끝을 rear로 정하겠다.

근데 여기서 더 우리가 생각해야될 부분이 있다.

front가 시작을 의미하고 rear가 끝을 의미한다고 했다. 만약 우리가 데이터를 모두 뺀다고 가정하자.

그럼 value = null, size--, front++ 과 같은 연산이 들어갈 것이다.

그때 마지막 데이터를 제거할 때 문제가 발생할 수 있다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FHm02Q%2FbtqPlcjgIUN%2FmtO2HLKG6Gsn981OgP1DWK%2Fimg.png)

위 그림과 같이 아무 데이터가 없는데 front가 rear을 넘어가 버린다.

이런 문제를 해결하기 위해 front가 가리키는 인덱스는 실제 첫 데이터를 가지고 있는 index보다 한 칸 앞에 있도록 구현해야한다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FA5CLM%2FbtqPaULwhq4%2Fc9N3GZWk5uba5Hqg2ksd40%2Fimg.png)

이런 구조를 보통 **'원형 큐(Circular Queue)'**라고 불린다.

구현한 코드는 다음과 같다.

## Queue Interface

```java
public interface QueueInterface<E> {
  boolean offer(E e); // 첫번째 큐에 추가
  E poll(); // 첫번째 리턴 후  제거
  E peek(); // 첫번째 큐에 있는 데이터 확인
}
```

여기서 주의할 점은 다른 자료구조와 달린 Queue에서는 add를 뜻하는 offer에서 리턴이 boolean으로 되어있다. 이 점을 주의하자.

## ArrayQueue

```java
import interface_form.QueueInterface;

public class ArrayQueue<E> implements QueueInterface<E> {
  private static final int DEFAULT_CAPACITY = 64;

  private Object[] array;
  private int size;
  private int front; // 시작 인덱스
  private int rear; // 마지막 요소의 인덱스

  public ArrayQueue() {
    this.array = new Object[DEFAULT_CAPACITY];
    this.size = 0;
    this.front = 0;
    this.rear = 0;
  }

  public ArrayQueue(int capacity) {
    this.array = new Object[capacity];
    this.size = 0;
    this.front = 0;
    this.rear = 0;
  }

  public void resize(int newCapacity) {
    int arrCapacity = array.length;
    Object[] newArray = new Object[newCapacity];

    for (int i = 1, j = front + 1; i <= size; i++, j++) {
      newArray[i] = array[j % arrCapacity];
    }
    array = null;
    array = newArray;

    front = 0;
    rear = size;
  }

  @Override
  public boolean offer(E item) {
    // if (front + 1 == rear || (rear == array.length - 1 && front == 0)) {
    // resize(array.length * 2);
    // }
    // if (rear == array.length-1) {
    // rear = 0;
    // }

    if ((rear + 1) % array.length == front) { // 용적이 가득찬 경우
      resize(array.length * 2);
    }

    rear = (rear + 1) % array.length;
    // 만약 rear가 끝에 도달했으면 = 0, 아니라면 용적보다 무조건 작으므로 그 값으로 나눴을 때 해당 인덱스가 나온다.

    array[rear] = item;
    size++;

    return true;
  }

  @Override
  public E poll(E value) {
    // if ((rear + 1) % array.length == front) {
    // resize(array.length * 2);
    // }
    // if (front == rear) {
    // return null;
    // }
    // E element = array[(front + 1 % array.length)];
    // array[(front + 1 % array.length)] = null;
    // front = (front + 1) % array.length;
    // size--;
    // return element;

    if (size == 0) {
      return null;
    }

    front = (front + 1) % array.length; // 한번만 계산하자.

    @SuppressWarnings("unchecked")
    E element = (E) array[front];
    array[front] = null;
    size--;

    if (array.length / 4 > size && array.length > DEFAULT_CAPACITY) {
      resize(Math.max(DEFAULT_CAPACITY, array.length / 2));
    }
    return element;
  }

  public E remove() {

    E item = pull();

    if (item == null) {
      throw new NoSuchElementException();
    }

    return item;
  }

  @Override
  public E peek() {
    if (size == 0) {
      return null;
    }
    @SuppressWarnings("unchecked")
    E item = (E) array[(front + 1) % array.length];
    return item;
  }

  public E element() {
    E item = peek();
    if (item == null) {
      throw new NoSuchElementException();
    }
    return item;
  }

  public int size() {
    return size;
  }

  public boolean isEmpty() {
    return size == 0;
  }

  public boolean contains(Object value) {
    int x = (front + 1) % array.length;

    for (int i = 0, idx = x; i < size; idx = (idx + 1) % array.length, i++) {
      if (array[idx].equals(value)) {
        return true;
      }
    }
    return false;
  }

  public void clear() {
    int x = (front + 1) % array.length;

    // for (int i = 0; i < size; i++, x=(x+1)%array.length) {
    // array[x] = null;
    // } 걍 싹 다 null로
    for (int i = 0; i < array.length; i++) {
      array[i] = null;
    }

    front = size = rear = 0;
  }
}
```



## 정리

기본적으로 Queue는 자바로 구현할 경우 LinkedList가 휠씬 편하다고 한다. 

직접 구현한 소스 코드

- [ArrayQueue](https://github.com/sunjong0214/DataStructure/blob/main/Queue/ArrayQueue.java)

- [Queue Interface](https://github.com/sunjong0214/DataStructure/blob/main/Interface_form/QueueInterface.java)



출처)

[자바[JAVA] - ArrayQueue](https://st-lab.tistory.com/183)

[자바[JAVA] - QueueInterface](https://st-lab.tistory.com/181)