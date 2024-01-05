---
title : Doubly LinkedList
categories : [자료구조]
tags : [Linked List, 자료구조]
---



# Doubly LinkedList 구현

st-lab 님의 블로그를 참고해서 구현한 Doubly LinkedList이다.

## Node< E >

```java
public class Node<E> {
  E data;
  Node<E> next;
  Node<E> prev;

  Node<E> (E data) {
    this.data = data;
    this.next = null;
    this.prev = null;
  }
}
```

## Singly LinkedList

```java
import java.lang.reflect.Array;
import java.util.Arrays;
import java.util.Comparator;
import java.util.NoSuchElementException;

public class DoublyLinkedList<E> implements Interface_form.List<E> {

  private Node<E> head;
  private Node<E> tail;
  private int size;

  public DoublyLinkedList() {
    this.head = null;
    this.tail = null;
    this.size = 0;
  }

  public Node<E> search(int index) {
    if (index < 0 || index >= size) {
      throw new IndexOutOfBoundsException();
    }
    // if ((size / 2) < index) { // 뒤에서부터 탐색
    //   for (Node<E> x = tail; x != null; x = x.prev) {
    //     int i = size - 1;
    //     if (i-- == index) {
    //       return x;
    //     }
    //   }
    // } else {
    //   for (Node<E> x = head; x != null; x = x.next) {
    //     int i = 0;
    //     if (i++ == index) {
    //       return x;
    //     }
    //   }
    // }
    // 너무 Node를 이용한 조건문 사용에 매몰된 것 같다. 그리고 size도 인덱스보다 1 많으니 조건문 설정할 때 index + 1을 해줘야 정확한 비교 가능
    if (size / 2 < index + 1) {
      Node<E> x = tail;
      for (int i = size-1; i != index; i--) {
        x = x.prev;
      }
    } else {
      Node<E> x = head;
      for (int i = 0; i != index; i++) {
        x = x.next;
      }
    }
    return x;
  }

  public void addFirst(E value) {
    Node<E> addNode = new Node<E>(value);
    // if (head == null) {// 노드에 아무것도 없을 때
    //   head = addNode;
    //   tail = addNode;
    //   return;
    // }
    // Node<E> nextNode = head;
    // addNode.next = nextNode;
    // nextNode.prev = addNode;
    // head = addNode; 
    // 더 효율적으로 짜보자
    addNode.next = head;
    if (head != null) {
      head.prev = addNode;
    }
    head = addNode;
    size++;
    if (head == null) {
      tail = addNode;
    }
  }

  public boolean add(E value) {
    addLast(value);
    return true;
  }

  public void addLast(E value) {
    Node<E> newNode = new Node<E>(value);
    if (tail == null) {
      addFirst(value);
      return;
    }
    newNode.prev = tail;
    tail.next = newNode;
    tail = newNode;
    size++;
  }

  public void add(int index, E value) {
    if (index < 0 || index >= size) {
      throw new IndexOutOfBoundsException();
    }
    if (index == 0) {
      addFirst(value);
      return;
    }
    if (index == size - 1) {
      addLast(value);
      return;
    }
    Node<E> newNode = new Node<E>(value);
    Node<E> preNode = search(index);
    Node<E> nxtNode = preNode.next;

    preNode.next = newNode;
    nxtNode.prev = newNode;
    newNode.prev = preNode;
    newNode.next = nxtNode;
    size++;
  }

  public E remove() {
    if (head = null) {
      throw new NoSuchElementException();
    }
    Node<E> rmNode = head;
    Node<E> nxtNode;
    if (rmNode.next != null) {
      nxtNode = rmNode.next;
      nxtNode = null; 
    }
    E element = rmNode.data;
    rmNode.data = null;
    rmNode.next = null;
    // rmNode.prev = null; // head를 가져왔기 때문에 이미 null이다.
    head = nxtNode;
    size--;
    // node가 하나만 있었을 경우 설정 또 깜빡함
    if (size == 0) {
      tail = null;
    }
    return element;
  }

  public E value(int index) {
    if (index < 0 || index >= size) {
      throw new IndexOutOfBoundsException();
    }
    if (index == 0) {
      return remove();
    }
    Node<E> delNode = search(index);
    Node<E> nxtNode = delNode.next;
    Node<E> preNode = delNode.prev;

    E element = delNode.data;
    delNode.data = null;
    delNode.next = null;
    delNode.prev = null;
    preNode.next = nxtNode;

    if (nxtNode != null) {
      nxtNode.prev = preNode;
    } else {
      tail = preNode;
    }
    size--;
    return element;
  }

  public boolean remove(Object value) {
    if (head.data == value) {
      remove();
      return true;
    }
    Node<E> x = head;
    while (x.data != value) {
      x = x.next;
    }
    if (x == null) {
      return false;
    }
    Node<E> preNode = x.prev;
    Node<E> nxtNode = x.next;
    x.data = null;
    x.next = null;
    x.prev = null;
    preNode.next = nxtNode;
    if (nxtNode != null) {
      nxtNode.prev = preNode;
    } else {
      tail = preNode;
    }
    size--;
    return true;
  }

  public E get(int index) {
    return search(index).data;
  }

  public void set(int index, E value) {
    search(index).data = value;
  }

  public int indexOf(Object value) {
    int index = 0;
    for (Node<E> x = head; x != null; x = x.next) {
      if (x.equals(value)) {
        return index;
      }
      index++;
    }
    return -1;
  }

  public int LastindexOf(Object value) {
    int index = size-1;
    for (Node<E> x = tail; x != null; x = x.prev) {
      if (x.equals(value)) {
        return index;
      }
      index--;
    }
    return -1;
  }

  public boolean contains(Object item) {
    return indexOf(item) >= 0;
  }

  public int size() {
    return size;
  }

  public boolean isEmpty() {
    return size == 0;
  }

  public void clear() {
    for (Node<E> x = head; x != null; x = x.next) {
      Node<E> tempNode = x.next;
      x.data = null;
      x.prev = null;
      x.next = null;
      x = tempNode;
    }
    head = tail = null;
    size = 0;
  }

  public Object clone() throws CloneNotSupportedException {

    DoublyLinkedList<? super E> clone = (DoublyLinkedList<? super E>) super.clone();

    clone.head = null;
    clone.tail = null;
    clone.size = 0;

    for(Node<E> x = head; x != null; x = x.next) {
      clone.addLast(x.data);
    }
    return clone;
  }

  public Object[] toArray() {
    Object[] arr = new Object[size];
    int i = 0;
    for(Node<E> x = head; x != null; x = x.next) {
      arr[i++] = x.data;
    }
    return arr;
  }

  public <T> T[] toArray(T[] a) {
    if (a.length < size) {
      a = (T[]) Array.newInstance(a.getClass().getComponentType(), size);
      // a 사이즈르 재정의 하기 위해 a의 타입을 받고 사이즈 까지 받아 새로운 array 객체를 만든다.
    }
    int i = 0;
    Object[] result = a;
    for(Node<E> x = head; x != null; x = x.next) {
      result[i++] = x.data;
    }
    return result;
  }

  public void sort() {
    sort(null);
  }

  public void sort(Comparator<? super E> c) {
    Object[] a = this.toArray();
    Arrays.sort(a, (Comparator) c);
    int i = 0;
    for(Node<E> x = head; x != null; x = x.next) {
      x.data = (E) a[i++];
    }
  }
}
```



## 소스 코드 링크

[DoublyLinkedList](https://github.com/sunjong0214/DataStructure/tree/main/DoublyLinkedList)



출처) [자바 [JAVA] - Doubly LinkedList 구현하기](https://st-lab.tistory.com/169)