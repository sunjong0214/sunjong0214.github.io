---
title : CPU bound, IO bound
categories : [운영체제]
tags : [cpu bound, io bound]
---

CPU bound, IO bound에 대해 이야기하기 전에 CPU, IO가 무엇인지 짧게 살펴보자.

- CPU(central processing unit) : 프로세스의 명령어를 해석하고 실행하는 장치
- IO(input/output) : 파일을 읽고 쓰기, 네트워크의 어딘가와 데이터를 주고 받는 것, 입출력 장치와 데이터를 주고 받는 것 .... 등등

또 각 bound에 대해 설명하기 위해 알아야할 개념이 있다. 버스트(Burst)이다.

- CPU 버스트 : 프로세스가 CPU에서 한번에 연속적으로 실행되는 시간
- IO 버스트 : 프로세스가 IO 작업을 요청하고 결과를 기다리는 시간

프로세스는 기본적으로 CPU 버스트와 IO 버스트의 연속이다.

## CPU bound 프로세스

CPU bound 프로세스란 CPU burst가 많은 프로세스를 뜻한다.

예로 동영상 편집 프로그램, 머신러닝 프로그램 등이 있다.

## IO bound 프로세스

IO bound 프로세스는 IO burst가 많은 프로세스이다.

예로 (일반적인) 백엔드 API 서버이다.



## 각 프로세스의 적절한 스레드

#### CPU bound의 스레드 수

Goetz의 말에 따르면 CPU bound 프로그램에서 적절한 스레드 갯수는 CPU/코어의 갯수 +1 한 갯수라고 한다. 

그 이유는 컨텍스트 스위칭과 관련이 있다.

기본적으로 컨텍스트 스위칭은 온전한 오버헤드라고 했다. 그렇기 때문에 스레드의 숫자가 늘어나면 늘어날수록 컨텍스트 스위칭이 많이 일어나기 때문에 적절하지 않은 스레드의 수는 오히려 성능을 떨어트리는 효과가 일어난다. 

#### IO bound의 스레드 수

IO bound의 경우 CPU bound와 달리 따로 공식이 있거나 하지는 않다. 여러 상황에 맞춰서 적절한 스레드 수를 찾아야한다.

ChatGPT에 따르면 IO bound의 스레드 수를 정할 때 고려해야 하는 요소들은 다음과 같다고 한다.

- 입출력 대기 시간
- IO 작업의 병렬성
- 시스템 리소스
- 스레드 풀 사용
- 비동기
- 시스템 특성 고려
- 감독 모니터링
- 스레드 간 통신



출처)

[cpu bound, io bound 의미를 설명합니다! 이에 따른 스레드 개수를 정하는 팁도 알려드립니다! (youtube.com)](https://www.youtube.com/watch?v=qnVKEwjG_gM&list=PLcXyemr8ZeoQOtSUjwaer0VMJSMfa-9G-&index=3&ab_channel=쉬운코드)