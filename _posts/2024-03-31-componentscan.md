---
title : 컨테이너에 Bean을 등록하는 2가지 방법
categories : [Spring]
tags : [DI, componentscan, autowired]
---



전 포스팅에서 우리는 직접 MemberService에 생성자를 만들어 DI를 통해 MemberRepository를 주입했었다.

하지만 스프링에서는 사용자가 직접 DI를 구현할 필요없이 개발자의 편의를 위해 스프링 컨테이너가 관리해준다.

뿐만 아니라 스프링 컨테이너에 객체를 등록하면 해당 객체를 관리해주는 기능을 제공한다.

이때 컨테이너에 객체를 등록하는 것을 `Bean`에 등록한다고 한다.

`Bean`이란 Ioc 컨테이너 즉, `스프링 컨테이너가 관리하는 자바 객체`라는 뜻 이다.

스프링의 IoC 컨테이너는 내부적으로 처음 어플리케이션이 실행되면 클래스들을 자동 생성하고 Bean에 등록해준다.

스프링 컨테이너에 Bean을 등록하는 방식에는 2가지 방식이 있다.

- 컴포넌트 스캔을 이용해 자동 스프링 빈에 등록하고 자동 의존관계 설정
- 자바 코드를 통해 직접 스프링 빈에 등록하기

자세한 것은 예제를 통해 알아보자.

## 컴포넌트 스캔을 이용

지금까지 진행한 우리 어플리케이션은 Service와 Repository만 있고 Controller가 없었다.

스프링에서는 Controller를 작성할 때 @Controller 어노테이션을 붙여 관리한다.

Controller에서는 Service 객체가 필요하므로 Service 객체를 생성해주자.

```java
package hello.hellospring.controller;

import hello.hellospring.service.MemberService;
import org.springframework.stereotype.Controller;

@Controller
public class MemberController {
    private final MemberService memberService = new MemberService();
}
```

지금까지는 new로 memberService 객체를 생성해줬다.

하지만 스프링에서는 직접 new로 객체를 생성하는 것보다 스프링 컨테이너에 Bean으로 등록해 관리하도록 하는 작업이 필요하다.

하지만 우리가 작성한 MemberService를 살펴보면 온전한 자바 클래스 파일이고 그렇기 때문에 스프링 컨테이너에서 이 MemberService를 관리할 수가 없는 상태다.

이때 필요한 것이 어노테이션이다.

```java
@Service // 스프링 컨테이너에게 이 파일을 Bean에 등록해주세요 라는 뜻
public class MemberService {
 	   .
 	   .
 	   .
```

이 @Service 어노테이션을 붙이면 스프링 컨테이너가 이 자바 클래스를 Bean에 등록하고 관리해준다.

그 다음으로 MemberController와 MemberService를 연결 해주는 작업이 필요하다.

```java
@Controller
public class MemberController {
    private final MemberService memberService;

    public MemberController(MemberService memberService) {
        this.memberService = memberService;
    }
}
```

위와 같이 생성자를 설정한 뒤, Controller와 Service를 이어주는 어노테이션이 필요한데 그게 바로 `@Autowired`이다.

```java
@Autowired
public MemberController(MemberService memberService) {
    this.memberService = memberService;
}
```

`@Autowired`를 붙여주면 스프링 컨테이너에서 객체를 생성할 때 Bean에서 MemberService를 찾아서 연결해준다.

이로써 MemberController와 MemberService가 서로 연결되었다.

MemberService에서도 똑같은 작업을 해주었다.

```java
 	@Autowired // MemoryMemberRepository와 연결
    public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }

   @Repository // 스프링에게 Bean에 등록하라고 알려줌
   public class MemoryMemberRepository implements MemberRepository{
       				.
                    .
                    .
   }
```



우리가 지금까지 각 클래스에 붙인 어노테이션을 살펴보면 각 객체의 역할에 맞는 어노테이션을 붙여줬다.

이건 이 Controller, Service, Repository이 굉장히 정형화된 패턴이기 때문에 스프링에서도 각 역할에 맞는 어노테이션이 존재하는 것이다.



사실 이 @Controller, @Service, @Repository 어노테이션이 스프링 컨테이너에게 Bean에 등록하라고 직접 알려주는 것이 아니다.

위 셋 어노테이션에 내부 구조를 살펴보면 공통적으로 `@Component(= @ComponentScan)` 라는 어노테이션이 있는데 이것이 `컨테이너에게 이 객체를 Bean에 등록하라고 알려주는 역할`을 하는 것이다. 



스프링에서는 컨테이너에 Bean을 등록할 때, 싱글톤으로 등록한다. 즉, 유일하게 하나만 등록하고 그 Bean을 공유한다. 

예를 들어 helloController에서 memberService를 사용하고 또 memberController에서도 memberService를 사용한다고 가정하자. 

그럼 이 memberService는 Bean에 2개의 인스턴스가 등록된 것일까?

정답은 아니다. 각 controller에서 다른 memberService가 아니라 하나의 memberService를 가지고 공유하는 것이다. 이 점을 유의하자.



## Bean에 직접 등록

```java
package hello.hellospring;

import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;
import hello.hellospring.service.MemberService;
import org.springframework.context.annotation.Bean;
import org.springframework.context.annotation.Configuration;

@Configuration
public class SpringConfig {
    @Bean
    public MemberService memberService() {
        return new MemberService(memberRepository());
    }

    @Bean
    public MemberRepository memberRepository() {
        return new MemoryMemberRepository();
    }
}
```

컨테이너에 객체를 등록하기 위한 Config 파일이 필요하다.

@Configuration 어노테이션을 붙여 스프링 컨테이너에게 Config 파일에 대해 알려준다.

그리고 Bean에 등록할 객체들을 @Bean 어노테이션을 붙인 뒤 위와 같이 작성하면 된다.

그 다음으로 컴포넌트 스캔이 있는 MemberService에 @Service, @Autowired와 MemoryMemberRepository에 @Repository, @Autowired를 지워주면 된다.

하지만 Controller의 경우에는 이런 직접 Bean에 등록하지 않고 Component Scan 방식을 이용해야하한다.



## 정리

Bean에 등록하는 2가지 방법에 대해 알아보았다.

실무에서는 2가지 방식을 혼합해서 쓰기 때문에 두 방식 다 알고 있어야한다.

보통 컴포넌트 스캔 방식은 Controller, Service, Repository와 같은 정형화된 코드에 사용하고, 정형화 되지 않거나, 상황에 따라 구현 클래스를 변경해야 한다면 그때는 직접 Bean에 등록하는 방식을 사용한다.