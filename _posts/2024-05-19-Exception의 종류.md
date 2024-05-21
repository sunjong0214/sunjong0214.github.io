---
title : Check Exception과 Uncheck Exception
categories : [JAVA]
tags : [java, exception, error]
---

## Exception의 종류

![](https://baekjungho.github.io/resource/wiki/spring-exception-hierarchy/exception-hierarchy.jpeg)

- `에러(Error)`

  - java.lang.Error 클래스의 하위 클래스들

  - Error는 메모리가 부족하다는 등과 같이 시스템이 비정상적인 상황인 경우에 발생

  - 주로 JVM에서 발생해 애플리케이션 코드에서 잡아서는 안되며, 잡아서 대응할 수도 없다.

- `예외(Exception)` : 에러와 달리 애플리케이션에서 예외가 발생할 경우 사용, 체크와 언체크 예외로 나뉨

  - `체크 예외(Check Exception)`

    - RuntimeException 클래스를 상속받지 않은 예외 클래스들

    - `복구 가능성이 있는 예외`, 예외를 처리하는 코드를 함께 작성  : catch 문으로 잡거나 throws를 통해 메소드 밖으로 던져야 함
    - 대표적으로 IOException, SQLException 등이 있다.
    - 체크 예외를 처리하지 않으면 `컴파일 에러가 발생` (예외 처리를 강제)

    - 모든 체크 예외를 처리해줘야 하므로 번거롭다
    - 실제로 애플리케이션 개발에서 발생하는 예외들은 복구 불가능한 경우가 많음
    - 위와 같은 이유 때문에 실제 개발에서는 대부분 언체크 예외를 사용

  - `언체크 예외(Uncheck Exception)`

    - RuntimeException 클래스를 상속받는 예외 클래스들
    - `복구 가능성이 없는 예외`들로 `컴파일 에러가 발생하지 않음` (예외 처리를 강제하지 않음)
    - 대표적으로 NullPointerException, IllegalArgumentException  등이 있다
    - 언체크 예외는 `생략이 가능`해 상당히 편리하지만 개발자의 실수로 `예외를 누락할 수 있다는 단점`이 있다



우리가 자주 사용하는 스프링 프레임워크의 `트랜젝션(@Transactional) `안에서 에러 발생 시 `체크 예외는 롤백이 되지 않고,` `언체크 예외는 롤백`이 되기 때문에 이 차이에 대해 잘 알아둬야한다.



## 예외 처리 방법

- `예외 복구`

  - 예외 상황을 파악하고 `문제를 해결`해서 정상 상태로 돌려놓는 것
  - 대표적으로 다른 API 호출에 실패하였을 경우, 3회 정도 retry 하여 복구되로록 하는 것

- `예외 처리 회피`

  - 예외 처리를 직접 처리하지 않고, 자신을 `호출한 곳으로 던져버리기`

- `예외 전환`

  - `적절한 예외로 변환하여 던지기`

  - 2가지 목적으로 사용

    - 의미 있고 추상화된 예외로 바꾸는 경우
    - 런타임 예외로 포장하여 불필요한 처리를 줄여주는 경우

  - 내부에서 발생한 예외를 그대로 던졌을 때 적절한 의미를 알 수 없다면, `의미 있고 추상화된 예외로 바꾸는 것이 좋다.`

  - 예를 들어 새로운 사용자 등록 시 동일한 아이디가 존재하면 SQLException이 발생한다. 

    이 에러를 그대로 사용하면 서비스 계층에서 어떤 예외가 발생했는지 파악이 힘들다.

    그럴 때 커스텀 예외를 사용해 DuplicatedUserIdException과 같은 예외로 던져 확실히 어떤 예외인지 의미를 전달할 수 있다.

  - 또한 체크 예외에 의해 불필요한 예외 처리가 많아진다면 이를 런타임 예외로 포장(언체크 예외로)하여 불필요한 처리를 막을 수 있다.



## 올바른 예외 처리 방법

- `조치가 없는 try/catch -> 상황에 맞는 조치 진행`

  - try/catch로 예외를 잡고 아무 조치도 하지 않는 것은 조심해야 한다.

    예외가 발생해도 다음 라인을 실행하겠다는 의도가 있는 것이 아니라면 반드시 피해야 된다.

- `무분별한 throws Exception -> 언체크 예외로 전환`

  - throws Exception을 남발하는 것은 좋지 않다.
  - 어떤 문제가 발생했는지 예외 문구를 보고 파악할 수 있어야 한다.
  - 만약 SQLException과 같이 복구가 불가능한 예외라면 throws를 던지기 보단 언체크/런타임 예외로 전환해주는 것이 좋다.

- 예외 전환을 하거나 새로운 예외를 만들 때 `꼭 기존 예외를 포함`

  - 만약 SQLException이 발생해 예외 전환 시 기존 예외를 포함 시켜서 던져야한다.

    ```java
     public class MyRuntimeException extends RuntimeException{
        public MyRuntimeException(Throwable cause) {
          super(cause);
        }
      }
    ```

    이와 같이 생성자 중 `Throwble을 파라미터로 받는 생성자`를 만들어 기존 예외를 넘겨줘야한다.

  - 기존 예외를 생략 시, `어떤 예외가 발생했는지 알 수 없기 때문에` 꼭 기존 예외를 포함시켜서 던져야한다.

출처)

[Exception Hierarchy - FLex (baekjungho.github.io)](https://baekjungho.github.io/wiki/spring/spring-exception-hierarchy/)

[[Java\] 체크 예외(Check Exception)와 언체크 예외/런타임 예외 (Uncheck Exception, Runtime Exception)의 차이와 올바른 예외 처리 방법 - MangKyu's Diary (tistory.com)](https://mangkyu.tistory.com/152)