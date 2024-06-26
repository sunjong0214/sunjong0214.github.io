---
title : Context Switching
categories : [운영체제]
tags : [thread, process, context switching]
---

## Context Switching

컨텍스트 스위칭(Context Switching)이란 CPU나 코어에서 실행 중이던 프로세스/스레드가 다른 프로세스/스레드로 교체되는 것이다.

여기서 컨텍스트는 프로세스/스레드의 상태를 의미한다.(CPU/메모리 안에서의)

컨텍스트 스위칭은 여러 프로세스/스레드를 동시에 실행 시키기 위해 필요하다. 이러한 컨텍스트 스위칭은 언제 발생하는지 알아보자.

- 주어진 time slice(quantum)을 다 사용한 경우

- IO(입출력) 작업을 해야되는 경우
- 다른 리소스를 기다려야 하는 경우
- ....등등

컨텍스트 스위칭은 과연 **누구**에 의해 실행될까?

각종 리소스를 관리/감독하는 **OS 커널(kernel)**에 의해 실행된다.

## Context Switching의 과정

컨텍스트 스위칭은 다른 프로세스끼리의 스위칭인지 혹은 같은 프로세스의 스레드들끼리 스위칭인지에 따라 나뉜다.

위 둘의 공통점은 다음과 같다.

- 커널 모드에서 실행
- CPU의 레지스터 상태를 교체

차이점도 알아보자.

프로세스 컨텍스트 스위칭은 가상(virtual) 메모리 주소 관련 처리를 추가로 진행해야 한다는 점이다.

같은 프로세스의 스레드들끼리는 다음과 같이 컨텍스트 스위칭이 진행된다.

#### 같은 프로세스의 스레드

프로세스 P에 T1과 T2라는 스레드가 있다고 가정하자.

![스크린샷 2024-01-19 153414](https://github.com/sunjong0214/algorithm-study/assets/117134728/b1db12b5-c981-40ae-9e09-9040eeb8f5d6)

이런 식으로 컨텍스트 스위칭이 발생할 때 실행하던 스레드의 상태를 저장하고 불러올 스레드의 상태를 로딩한다.



#### 다른 프로세스의 스레드

![스크린샷 2024-01-19 153515](https://github.com/sunjong0214/algorithm-study/assets/117134728/303b9b58-f2da-4a04-90f2-661693444bd6)

다음과 같이 컨텍스트 스위칭이 일어나기 전 가상 메모리 주소 관련 추가 처리를 해줘야한다. 



위와 같은 이유로 프로세스 컨텍스트 스위칭은 메모리 관련 처리가 추가로 들어가기 때문에 스레드 컨텍스트 스위칭에 비해 느리다. 

## 정리

컨텍스트 스위칭은 간접적으로 캐시 오염이라는 영향을 줄 수 있다. (이건 다음에 알아보자.)

사용자 관점에서 컨텍스트 스위칭은 **순수한(pure) 오버헤드(overhead)**다.

즉, 가져다 주는 이익은 전혀 없고 성능에 악영향만 끼친다는 것이다.

그렇다보니 컨텍스트 스위칭이 최소로 일어나게끔 하는 것을 지향하자.



출처)

[컨텍스트 스위칭 뽀개기! 의미와 종류와 왜 스레드 컨텍스트 스위칭이 더 빠르다고 하는지까지..! 이 모든 것을 시원~~하게 설명합니다!! (youtube.com)](https://www.youtube.com/watch?v=Xh9Nt7y07FE&t=684s&ab_channel=쉬운코드)