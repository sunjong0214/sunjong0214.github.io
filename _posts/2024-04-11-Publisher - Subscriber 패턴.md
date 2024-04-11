---
title : WebFlux에서의 Publisher와 Subscriber
categories : [Spring]
tags : [webflux, publisher, subscriber]
---



WebFlux에 대해 공부하다 Publisher와 Subscriber에 대해 좀 더 자세히 공부하고 싶어 정리한 글이다.



#### Publisher - Subscriber 패턴

> spring mvc로 동기식 개발을 할 때는 메서드 호출만 하면 결과값이 return 됐었다.
> 그러나, reactive하게 동작하는 WebFlux에서는 메서드만 호출해서는 아무 일이 일어나지 않는다.([Reactive선언문](https://zorba91.tistory.com/290))



![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FcXQrI4%2FbtqBtMTDnAn%2FKS6tHYZA7Qz7J5qSYz2O6K%2Fimg.png)

> Publisher - Subscriber 패턴은 비동기 메시징 페러다임이다. 발행 - 구독 모델에서는 발신자의 메시지는 특별한 수신자가 정해져 있지 않다. 대신 발행된 메시지는 정해진 범주에 따라, 각 범주에 대한 구독을 신청한 수신자에게 절단된다. 수신자는 발행자에 대한 지식이 없어도 원하는 메시지만을 수신할 수 있다. 이러한 발행자와 구독자의 디커플링은 더 다이나믹한 네트워크 토폴로지와 높은 확장성을 허용한다.



Publisher - Subscriber 패턴은 옵저버 패턴과 매우 유사하다. 특히 객체가 옵저버에게 객체의 상태를 알려준다는 부분이 비슷하다. (subject = publisher, observer = subscriber)

하지만 Publisher - Subscriber 패턴은 메시지 송신자(publisher)는 정해진 수신자와 직접적으로 메시지로 통신하지 않는다. 그 둘 사이에 브로커라고 불리는 제 3의 구성요소가 있다.

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdn%2FTHaNB%2FbtqBtGF6tbR%2F6nSNAnpnb7Mw2LqF6aibV0%2Fimg.png)



#### Event bus

모든 들어오는 메시지를 필터링하고 다시 메시지들을 배포한다.

즉, Publisher - Subscriber 패턴은 다른 시스템 컴포넌트 간에 서로 알지 못해도 소통할 수 있도록 해준다.

위에서 나온 브로커는 모든 메시지들을 크게 2가지 방식으로 메시지 필터링을 한다. topic-based system과 content-based system이다. 자세한건 따로 찾아보자.



## 요약

| 구성               | 옵저버 패턴                                                  | 구독 - 발행 패턴                                       |
| ------------------ | ------------------------------------------------------------ | ------------------------------------------------------ |
| 의존성             | 옵저버들이 객체들을 알고있고, 객체들은 옵저버들의 기록을 가지고 있음 | 서로 몰라도 되고, 메시지 큐들이나 브로커를 통해서 소통 |
| 비교했을 때 결합성 | 높음                                                         | 낮음                                                   |
| 구현방식           | 대부분 동기                                                  | 비동기                                                 |
| 확장성             | 하나의 애플리케이션에서 구현                                 | 크로스 애플리케이션 구현                               |



이런 차이들이 있지만 결국에는 옵저버 패턴에서 변화한 것이 구독 - 발행 패턴이라고 보면 된다.



#### WebFlux에서 유의사항

- subscribe하지 않으면 아무 일도 일어나지 않음

결국 subscribe함을써 publisher와 subscriber는 연결된다. 즉, 공급을 아무리해도 수요가 없으면 아무 일도 일어나지 않는다는 것과 비슷하다.

그렇다고 꼭 메서드를 subscribe해서 결과값을 return 해줄 필요는 없다. 스프링 내부적으로 controller에서 return된 publisher를 subscribe 해주므로 예시처럼 publisher를 return 해주면 된다.

```java
@GetMapping("/")
public Mono<String> hello() {
    return Mono.just("hello");
}
```



출처)

[[WebFlux\] publisher(발행) - subscriber(구독) 패턴에 대해서 :: Carry On Progamming (tistory.com)](https://zorba91.tistory.com/291)
