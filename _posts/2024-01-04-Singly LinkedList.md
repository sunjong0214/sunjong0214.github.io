---
title : Singly LinkedList
categories : [자료구조]
tags : [linked list, 자료구조]
---



# Singly LinkedList 구현

st-lab 님의 블로그를 참고해서 구현한 Singly LinkedList이다.

## Node< E >

```java
public class Node<E> {
  E data;
  Node<E> next;

  public Node(E data) {
    this.data = data;
    this.next = null;
  }
}
```

## Singly LinkedList

```java
import java.lang.reflect.Array;
import java.util.Arrays;
import java.util.Comparator;
import java.util.NoSuchElementException;

import Interface_form.List;

public class SinglyLinkedList<E> implements List<E> {
  private Node<E> head;
  private Node<E> tail;
  private int size;

  public SinglyLinkedList() {
    this.head = null;
    this.tail = null;
    this.size = 0;
  }

  private Node<E> search(int index) {
    if (index < 0 || index >= size) {
      throw new IndexOutOfBoundsException();
    }
    Node<E> x = head;
    for (int i = 0; i < index; i++) {
      x = x.next;
    }
    return x;
  }

  public void addFirst(E value) {
    Node<E> temp = new Node<E>(value);
    temp.next = head;
    head = temp;
    size++;
    if (head.next == null) {
      tail = head;
    }
  }

  @Override
  public boolean add(E value) {
    addLast(value);
    return true;
  }

  public void addLast(E value) {
    Node<E> newNode = new Node<E>(value);

    if (head.next == null) {
      addFirst(value);
      return;
    }
    tail.next = newNode;
    tail = newNode;
    size++;
  }

  @Override
  public void add(int index, E value) {
    if (index < 0 || index > size) {
      throw new IndexOutOfBoundsException();
    }
    if (index == 0) {
      addFirst(value);
      return;
    }
    if (index == size) {
      add(value);
      return;
    }
    Node<E> preNode = search(index - 1);
    Node<E> nexNode = preNode.next;
    Node<E> newNode = new Node<E>(value);
    preNode.next = null;
    newNode.next = nexNode;
    preNode.next = newNode;
    size++;
  }

  public E remove() {
    Node<E> delNode = head;
    Node<E> nexNode = head.next;
    if (delNode == null) {
      throw new NoSuchElementException();
    }
    E elementE = delNode.data;
    head.data = null;
    head.next = null;
    head = nexNode;
    size--;
    if (size == 0) {
      tail = null;
    }
    return elementE;
  }

  @Override
  public E remove(int index) {
    if (index < 0 || index >= size) {
      throw new IndexOutOfBoundsException();
    }
    if (index == 0) {
      return remove();
      ;
    }
    Node<E> delNode = search(index);
    Node<E> preNode = search(index - 1);
    Node<E> nxtNode = delNode.next;
    E elementE = delNode.data;
    preNode.next = null;
    delNode.data = null;
    delNode.next = null;
    preNode.next = nxtNode;
    if (preNode.next == null) {
      tail = preNode;
    }
    size--;
    return elementE;
  }

  @Override
  public boolean remove(Object value) {
    Node<E> preNode = head;
    Node<E> x = head; // deleteNode
    boolean hasValue = false;

    for (; x != null; x = x.next) {
      if (value.equals(x.data)) {
        hasValue = true;
        break;
      }
      preNode = x;
    } // 한칸씩 옮기면서 Node 설정하기

    if (x == null) {
      return false;
    }
    if (x.equals(head)) {
      remove();
      return true;
    } else {
      preNode.next = x.next;
      x.data = null;
      x.next = null;
      if (preNode.next == null) {
        tail = preNode;
      }
      size--;
      return true;
    }
  }

  @Override
  public E get(int index) {
    return search(index).data;
  }

  @Override
  public void set(int index, E value) {
    Node<E> setNode = search(index);
    setNode.data = null;
    setNode.data = value;
  }

  @Override
  public int indexOf(Object value) {
    Node<E> x = head;
    int i = 0;
    for (; x != null; x = x.next) {
      if (x.data.equals(value)) {
        return i;
      }
      i++;
    }
    return -1;
  }

  @Override
  public boolean contains(Object value) {
    return indexOf(value) >= 0;
  }

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
    for (Node<E> x = head; x != null;) {
      Node<E> clear = x;
      x = x.next;
      clear.data = null;
      clear.next = null;
    }
    head = null;
    tail = null;
    size = 0;
  }

  public Object clone() throws CloneNotSupportedException {
    @SuppressWarnings("unchecked")
    SinglyLinkedList<? super E> clone = (SinglyLinkedList<? super E>) super.clone();

    clone.head = null;
    clone.tail = null;
    clone.size = 0;

    for (Node<E> x = head; x != null; x = x.next) {
      clone.addLast(x.data);
    }
    return clone;
  }

  public Object[] toArray() {
    Object[] arr = new Object[size];
    int i = 0;
    for (Node<E> x = head; x != null; x = x.next) {
      arr[i++] = (E) x.data;
    }
    return arr;
  }

  public <T> T[] toArray(T[] a) {
    if (a.length < size) { // 파라미터로 넘어온 a 배열의 크기를 늘려야함
      a = (T[]) Array.newInstance(a.getClass().getComponentType(), size);
    }
    int i = 0;
    T[] result = a;
    for (Node<E> x = head; x != null; x = x.next) {
      result[i++] = x.data;
    }
    return a;
  }

  public void sort() {
    /**
	 *  Comparator를 넘겨주지 않는 경우 해당 객체의 Comparable에 구현된
	 *  정렬 방식을 사용한다.
	 *  만약 구현되어있지 않으면 cannot be cast to class java.lang.Comparable
	 *  에러가 발생한다.
	 *  만약 구현되어있을 경우 null로 파라미터를 넘기면
	 *  Arrays.sort()가 객체의 compareTo 메소드에 정의된 방식대로 정렬한다.
	 */
    sort(null);
  }
  
  @SuppressWarnings({ "unchecked", "rawtypes" })
  public void sort(Comparator<? super E> a) {
    Object[] c = this.toArray(); // toArray로 배열 불러오기
    Arrays.sort(c, (Comparator) a);
    
    int i = 0;
    for (Node<E> x = head; x != null; x = x.next) {
      x.data = (E) c[i++];
    }
  }
}

```



## 소스 코드 링크

[SinglyLinkedList](https://github.com/sunjong0214/DataStructure/tree/main/SiglyLinkedList)



출처) [자바 [JAVA] - Singly LinkedList (단일 연결리스트) 구현하기](https://st-lab.tistory.com/167)