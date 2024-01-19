---
title : Thread와 Thread Pool
categoies : [운영체제]
tags : [Thread, Thread Pool]
---

Thread란 프로세스가 실행될 때 요청들을 처리하는 것, 코드의 실행 흐름을 의미한다.

우리가 Thread Pool을 사용해야하는 이유에 대해 알아보자.

만약 어떠한 요청이 들어올 때마다 새로운 Thread를 만들어야 된다면 어떤 문제가 발생할까?

Thread 생성에 소요되는 시간 때문에 요청 처리가 더 오래 걸릴 것이다. Thread의 경우 커널 레벨에서 생성되기 때문에 생각보다 요청 처리에 더 많은 시간이 걸린다.

또 처리 속도보다 더 빠르게 요청이 늘어나면 어떤 문제가 발생할까?

Thread가 계속 생성되면서 Thread 수가 늘어나고, CPU 오버헤드 증가로 CPU time이 낭비되고 그러다 어느 순간 서버 전체가 응답 불가능 상태에 빠질 것이다.

이런 문제를 해결하기 위해 Thread Pool이 필요하다.

## Thread Pool

Thread Pool이란 미리 Thread들을 요청들을 처리할 Thread들을 미리 준비해두고 요청이 들어왔을 때 그 요청들을 Thread Pool 내부에 있는 작업 Queue에 넣은 뒤 작업을 처리 안 하고 있는 Thread들에 할당해 각 Thread가 처리하게 한다. 처리를 완료한 뒤에는 Thread가 없어지는 것이 아니라 다시 Thread Pool에 돌아와 요청이 들어올 때 까지 대기하게 된다.

즉, 미리 Thread를 여러개 만들어놓고 재사용 함으로써 Thread 생성시간을 절약해고,Thread가 무제한 생성되는 것을 방지해주는 효과가 있다.

Thread Pool은 결국 thread per request 모델, task를 subtask로 나뉘어 동시에 처리하는 작업이나, 순서 상관없이 동시 실행이 가능한 task를 처리할 때 사용된다.

그렇다면 우리가 미리 만들어놓을 Thread의 갯수는 어떤 기준으로 정해야될까?

그 기준은 CPU의 코어 개수와 task의 성향에 따라 다르다.

- CPU-bound task라면 : CPU 코어 개수만큼 혹은 그보다 몇 개 더
- I/O-bound task라면 : 코어 개수보다 1.5배~ 그 이상



또 Thread Pool을 사용할 때 주의할 점이 있다.

Thread Pool에서 실행될 task 개수 (요청 개수)에 제한이 없다면 **Thread Pool의 내부 Queue가 사이즈 제한이 있는지 확인**해야한다.

이 말은 즉, 만약 Thread의 작업 처리 속도보다 더 빠른 속도로 요청이 들어온다면 어떻게 될까?

일단 빈 Thread가 나올 때까지 Queue에 쌓이게 된다. 이때 계속해서 작업 처리 속도보다 빠른 속도로 요청이 계속 들어온다면, Queue에 무한정 쌓이게 되며, 잠재적으로 메모리를 고갈시킬 수 있는 위험요소가 될 수 있다.

그래서 Queue의 사이즈에 제한을 두어 만약 Queue가 다 쌓이게 된다면, 이 후 들어오는 요청들을 버리게 되더라도 전체 시스템에 문제가 발생하는 것은 막아야한다.

## Java에서의 예

실제 Java에서 예를 통해 살펴보자.

Java에서는 Executors 클래스에서 Thread Pool을 static 메서드들로 제공해준다.

```java
ExecutorService threadPool = Executors.newFixedThreadPool(10);
// 10은 Thread 갯수
```

위와 같이 선언하고 사용할 수 있다.

여기서 newFixedThreadPool을 직접 살펴보자.

```java
public static ExecutorService newFixedThreadPool(int nThreads) {
        return new ThreadPoolExecutor(nThreads, nThreads,
                                      0L, TimeUnit.MILLISECONDS,
                                      new LinkedBlockingQueue<Runnable>());
}
```

여기서 우리가 살펴볼 부분은 Thread의 내부 Queue를 살펴보기 위해

```java
new LinkedBlockingQueue<Runnable>()
```

Queue를 생성해주는 생성자는 다음과 같다.

```java
public LinkedBlockingQueue() {
        this(Integer.MAX_VALUE); // 생성자 호출
    }

    public LinkedBlockingQueue(int capacity) {
        if (capacity <= 0) throw new IllegalArgumentException();
        this.capacity = capacity;
        last = head = new Node<E>(null);
    }
```

이렇듯 LinkedBlockingQueue는 Integer의 최대값인 약 20억을 사이즈로 가진 Queue가 만들어진다는 것을 알 수 있다. 즉, 제한이 없는 Queue가 생성된다. 이런 부분을 유의하며 사용해야한다.





출처)

[스레드 풀(thread pool)은 왜 쓰는 걸까요? 어떻게 쓰는게 잘 쓰는 걸까요? 지금 이 영상으로 스레드 풀! 깔끔하게 정리하시죠! (youtube.com)](https://www.youtube.com/watch?v=B4Of4UgLfWc&ab_channel=쉬운코드)