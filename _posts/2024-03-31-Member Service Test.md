---
title : MemberService 테스트 코드
categories : [Spring]
tags : [service, test]
---



## Test

```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import org.junit.jupiter.api.Assertions;
import org.junit.jupiter.api.Test;

import java.util.List;

import static org.assertj.core.api.Assertions.*;

class MemberServiceTest {

    MemberService memberService = new MemberService();

    @Test
    void join() {
        //given
        Member member = new Member();
        member.setName("user1");

        //when
        Long saveId = memberService.join(member);

        //then
        assertThat(member.getId()).isEqualTo(saveId);
    }
    @Test
    public void 중복_회원_예외() {
        //given
        Member member1 = new Member();
        member1.setName("user1");

        Member member2 = new Member();
        member2.setName("user1");


        //when
        memberService.join(member1);
        IllegalStateException e = Assertions.assertThrows(IllegalStateException.class, () -> memberService.join(member2));

        assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");

        //then
    }

    @Test
    void findMembers() {
    }

    @Test
    void findOne() {
    }
}
```

앞서 작성했던 MemberService의 join 메소드에 대한 테스트 코드이다.

join의 경우 member 객체를 생성하는 역할도 있지만 중복 회원에 대한 검사도 같이 진행하게 된다.

그렇기때문에 이 join의 테스트를 진행할 때 잘 작동하는지와 예외가 잘 작동하는지도 검사해야된다.

join에서 예외가 잘 작동하는지 검사하는 확인하기 위한 코드는 아래와 같다.

```java
// 예외가 같은지
IllegalStateException e = Assertions.assertThrows(IllegalStateException.class, () -> memberService.join(member2));

// 예외 시 보내는 메시지가 같은지
assertThat(e.getMessage()).isEqualTo("이미 존재하는 회원입니다.");
```

예외가 같은지 확인 시 사용하는 메소드는 junit.Assertions의 `assertThrows`로 확인이 가능하다.



하지만 위 코드에서는 한 가지 문제가 더 있다.



#### 코드 개선



위 코드는 join에 값이 계속 누적되고 있으므로 같은 이름을 가진 member 객체를 생성할 경우 다른 테스트임에도 중복된 값으로 나온다.

그러기 위해선 전 포스팅에서 나온 afterEach를 적용시켜줘야한다.

```java

class MemberServiceTest {

    MemberService memberService = new MemberService();
    MemoryMemberRepository memberRepository = new MemoryMemberRepository();
    
    @AfterEach
    public void afterEach() {
        memberRepository.clearStore();
    }
```

근데 이런 식으로 필드에 MemoryMemberRepository를 생성하면 memberService 안에 있는 MemoryMemberRepository와 MemberServiceTest에 있는 MemoryMemberRepository 다른 객체이다.

이렇듯 memberService안에 있는 객체 대신 다른 객체를 사용한다는 점이 문제다.

이 점을 개선하기 위해 memberService에 생성자를 새로 만들어주자.

```java
public MemberService(MemberRepository memberRepository) {
        this.memberRepository = memberRepository;
    }
```

그 후 테스트를 진행하기 전에 memberService 객체를 생성하기 위해 `@BeforeEach` 에노테이션을 사용해주자.

```java
@BeforeEach
    public void beforeEach() {
        memberRepository = new MemoryMemberRepository();
        memberService = new MemberService(memberRepository);
    }
```

beforeEach는 각 테스트를 실행하기 전에 실행되며 memberService 객체를 만들어준다.

이렇듯 MemberService 객체를 만들 때 직접 MemberRepository를 만든 것이 아니라 외부에서 만들어 넘겨주는 것을 `Dependency Injection(DI)`라고 부른다.