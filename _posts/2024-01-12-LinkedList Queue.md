---
title : LinkedList Queue
categoies : [자료구조]
tags : [Queue]
---



이번에는 배열 대신 LinkedList를 사용해 Queue를 구현해보겠습니다.

## Node

```java
class Node<E> {
  E value;
  Node<E> next;
  
  public Node<E> (E value) {
    this.value = value;
    this.next = null;
  }
}
```

## LinkedListQueue

```java
import java.util.NoSuchElementException;

import Interface_form.*;

public class LinkedListQueue<E> implements QueueInterface<E> {

  Node<E> head;
  Node<E> tail;
  int size;

  public LinkedListQueue() {
    this.head = null;
    this.tail = null;
    this.size = 0;
  }

  @Override
  public boolean offer(E item) {
    Node<E> addNode = new Node<E>(item);
    if (size == 0) {
      head = tail = addNode;
    } else {
      tail.next = addNode;
    }
    addNode = tail;
    size++;

    return true;
  }

  @Override
  public E poll() {
    Node<E> delNode = head;
    if (size == 0) {
      // throw new NoSuchElementException();
      return null; // poll의 경우 아무 요소가 없을 때 null을 반환한다.
    }
    E element = delNode.data;
    head = head.next;
    delNode.data = null;
    delNode.next = null;
    size--;
    if (head == null) {
      tail = null;
    }
    return element;
  }

  public E remove() {
    E element = poll();
    if (element = null) {
      throw new NoSuchElementException();
    }
    return element;
  }

  @Override
  public E peek() {
    if (size == 0) {
      return null;
    }
    E element = head.data;
    return element;
  }

  public E element() {
    E element = peek();
    if (element == null) {
      throw new NoSuchElementException();
    }
    return element;
  }
}
```

size 등 다른 메소드는 전에 구현했던 ArrayQueue랑 비슷해 구현하지 않았습니다.

## 정리

#### 내가 구현한 소스코드

[Queue](https://github.com/sunjong0214/DataStructure/tree/main/Queue)



출처)

[자바 [JAVA] - 연결리스트를 이용한 Queue (큐) 구현하기 (tistory.com)](https://st-lab.tistory.com/184#recentComments)