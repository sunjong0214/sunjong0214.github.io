---
title : JDBC와 Connection Pool
categories : [Java]
tags : [DB, JDBC, Connection Pool]
---

JDBC란 자바에서 데이터베이스에 접근할 수 있도록 하는 자바 API이다. 데이터베이스에서 자료를 쿼리하거나 업데이트하는 방법을 제공한다.

![](https://velog.velcdn.com/images%2Fmungmnb777%2Fpost%2Fe95ae870-41b2-4800-8b12-94588a6cd237%2Fimage.png) 

최근에는 spring JDBC 등이 등장해 JDBC API를 직접 사용하는 일은 별로 없다. 하지만 spring JDBC와 같은 기술도 내부적으론 JDBC를 사용하기 때문에 어떤 식으로 구동하는지 알아두자.

## JDBC 동작 흐름

사용자가 자바 어플리케이션에서 JDBC API를 이용해 DB에 접근하고자 한다. 

하지만 JDBC API는 구현체가 아닌 인터페이스 이므로 실제 메서드를 사용하기 위해서는 구현체가 필요하다.

그 구현체를 DriverManger라는 클래스를 통해 생성할 수 있다. 이때 생성한 Driver를 통해 데이터베이스에 접근할 수 있는 것이다.

이때 만약 더 이상 DB를 사용하지 않을 시 반드시 close() 메서드를 통해 연결을 끊어줘야한다.

## JDBC API 사용 흐름

그렇담 JDBC가 어떤 흐름을 가지고 DB에 접근하는지 알아보자.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FwTyMC%2FbtrR5yww0DA%2Fjwst72s4xtTKhLTtzfJ0mK%2Fimg.png)

- 사용하고자 하는 DBMS의 드라이버를 로딩한다. (DriverManager를 통해 로딩)

- Connection 객체 생성 : DriverManager를 통해 데이터베이스와 연결되는 세션인 Connection 객체를 생성한다. (3-way-handshaking을 사용해 작업)
- Statement 객체 생성 : Statement 객체는 작성된 SQL 쿼리문을 실행하기 위한 객체로 정적 SQL 쿼리 문자열을 입력으로 가진다.
- Query 실행 : 생성된 Statement 객체를 이용하여 입력한 SQL 쿼리를 실행한다.
- ResultSet 객체로부터 데이터 조회 : 실행된 SQL 쿼리문에 대한 데이터 
- close : JDBC API를 통해 사용된 객체들은 사용한 순서의 역순으로 close한다. 



## 커넥션 풀(Connection Pool)

JDBC API를 사용하여 데이터베이스와 연결하기 위해 객체를 생성하는 것은 비용이 많이 드는 작업 중 하나다.

Connection Pool이란, 비용이 많이 드는 Connection을 미리 정해진 개수만큼 생성해 Pool에 보관하고, 재사용하여 데이터를 교환하는 방식이다.

이런 방식은 Connection 시 발생하는 반복적인 3-way-handshaking 과정을 제거할 수 있어 휠씬 빠르게 통신 가능하다.

#### Hikari CP (Connection Pool) 동작 원리

Spring 에서는 기본적으로 Hikari CP라는 JDBC Connection Pool을 사용한다. 이 동작 원리에 대해 간단히 알아보자.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2Fbav8cF%2FbtqTADjCVH1%2FRQbZ8DfapcUHyglakg6WK0%2Fimg.png)

위 그림과 같이 작동하는데, Hikari CP의 경우, 이전에 사용했던 Connection이 존재하는지 확인하고, 이를 우선적으로 반환하는 특징을 갖고 있다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FCxo9j%2FbtqTqJeQp50%2FNveuqvKEGK7K2IoeiDy491%2Fimg.png)

만약 사용가능한 Connection이 존재하지 않는다면, HandOffQueue를 Polling 하면서 다른 Thread가 Connection을 반환하기를 기다린다(Queue에 사용이 끝난 Connection이 들어오기까지). 그러다 만약 지정한 TimeOut 시간을 초과하는 순간 예외(Exception)을 던진다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2F3PcJG%2FbtqTrTn0gs6%2FztMrPKK8hxRW1a3NqJGdKk%2Fimg.png)

최종적으로 사용한 Connection을 반납하면 Connection Pool이 사용내역을 기록하고, HandOffQueue에 반납된 Connection을 삽입한다. 이때 만약 Polling하던 Thread가 있다면 그 Connection을 획득하고 작업을 이어간다.

이와 같이 Connection 객체 생성의 비싼 비용을 줄일 수 있다.



#### 적절한 Connection Pool 크기

그렇다면 Connection Pool의 크기는 어느정도가 적절할까? Connection Pool의 크기는 클수록 성능이 좋을까? 답은 NO이다.

Connection Pool을 사용하는 주체는 Thread이다. 그렇기 때문에 Thread Pool의 크기보다 Connection Pool의 크기가 커지면, 실제 트랜잭션을 처리할 Thread가 부족해 Connection은 실질적으로 메모리 공간만 차지하게 된다.

결국 Connection Pool은 Thread Pool과 관련이 있고, 무작정 Connection Pool과 Thread Pool을 늘리게 되면 Thread의 증가는 곧 성능 저하를 일으키게 된다. [관련 링크 ](https://sunjong0214.github.io/posts/Context-Switching/)

데이터베이스 입장에서 Connection은 Thread와 어느정도 일치한다고 볼 수 있다.

그럼 적절한 크기는 얼마일까?

그 크기는 Hikari CP의 공식 문서에 따르면 `connections = ((core_count * 2) + effective_spindle_count)`라고 명시되어 있다.

위 공식은 절대적인 것은 아니고 실제 프로그램의 다양한 상황들을 고려해서 크기를 적절하게 측정해야 된다.





출처) 

[내가 만든 서비스는 얼마나 많은 사용자가 이용할 수 있을까? - 3편(DB Connection Pool) (tistory.com)](https://hyuntaeknote.tistory.com/12)

[[Java] JDBC란 무엇인가? - Java Database Connectivity (tistory.com)](https://ittrue.tistory.com/250)
