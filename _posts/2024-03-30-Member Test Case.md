---
title : 테스트 케이스 연습
categories : [Spring]
tags : [test case]
---





이전에 만들었던 MemoryMemberRepository의 테스트 케이스를 작성해보자



## Test Case

- Test Case를 작성할 때는 test 폴더 밑에 같은 테스트를 진행할 패키지의 이름과 `같은 이름의 패키지`를 하나 만들어준다
- `@Test` 어노테이션을 붙여 테스트 코드라는 것을 명시해준다.

#### 작성 코드

``` java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import org.junit.jupiter.api.Test;

import java.util.List;

import static org.assertj.core.api.Assertions.*;

class MemoryMemberRepositoryTest {
    MemoryMemberRepository repository = new MemoryMemberRepository();
    @Test
    public void save() {
        Member member = new Member();
        member.setName("aaa");

        repository.save(member);

        Member result = repository.findById(member.getId()).get();

        assertThat(member).isEqualTo(result);
    }

    @Test
    public void findByName() {
        Member member1 = new Member();
        member1.setName("aaa");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("bbb");
        repository.save(member2);

        Member result = repository.findByName("aaa").get();

        assertThat(member1).isEqualTo(result);
    }

    @Test
    public void findAll() {
        Member member1 = new Member();
        member1.setName("aaa");
        repository.save(member1);

        Member member2 = new Member();
        member2.setName("bbb");
        repository.save(member2);

        List<Member> result = repository.findAll();

        assertThat(result.size()).isEqualTo(2);
    }
}
```

- 결과를 확일할 때는 assertj의 `Assertions`를 사용해 확인한다.
  - `assertThat().isEqualTo()`를 사용하면 원하는 객체나 값이 뒤에 작성한 객체나 값과 `같은지 확인`한다.



하지만 위의 클래스 단위로 테스트를 진행했을 때 문제가 발생한다.

클래스 단위 테스트는 테스트를 진행하는 메소드의 순서가 `무작위로 실행`된다.

그러므로 findAll이 먼저 실행되어 findByName 테스트가 진행될 때 findAll 테스트 진행할 때 생성한 객체가 남아있어 문제가 발생할 수 있다.

그래서 Test 하나를 진행하고 난 뒤 데이터를 클리어 해줘야 한다.



#### MemoryMemberRepository에 clearStore() 메소드 추가

```java
 public void clearStore() {
        store.clear();
     // store을 클리어 해준다
 }
```

#### MemoryMemberRepository에 afterEach() 메소드 추가

```java
@AfterEach
public void afterEach() {
    repository.clearStore();
}
```

- `@AfterEach` 어노테이션은 단위 테스트가 실행된 후 자동 실행해준다.



#### *TDD(테스트 주도 개발)란

- 테스트 클래스를 먼저 작성한 후에 구현 클래스를 작성하는 방법

틀을 먼저 만들고 그 다음 실제 작동하는지 확인하는 것이다.