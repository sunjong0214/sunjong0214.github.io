---
title : Controller, Service, Repository, Domain
categories : [Spring]
tags : [controller, service, repository, domain]
---



#### 웹 애플리케이션 계층 구조

![image](https://github.com/sunjong0214/algorithm-study/assets/117134728/b40aa4df-cd67-4dbc-bf85-198cb9590f72)

- Controller : MVC에서 컨트롤러의 역할을 하고, 클라이언트에서 요청이 들어오면 그 요청을 Service에게 Mapping 해준다.
- Service : 핵심 비즈니스 로직을 구현한 계층, Repository에 저장된 객체를 이용한다.
- Domain : DB에 저장할 객체, 보통 Getter와 Setter가 있는 곳이다.
- Repository : DB에 직접 접근해 도메인 객체를 DB에 저장하고 관리한다. 보통 Interface를 만들고 구현하는 방식으로 사용한다.



#### 클래스 의존 관계

![image](https://github.com/sunjong0214/algorithm-study/assets/117134728/dcb26f38-8ee2-45ca-ad16-7332bfe18bed)

- 보통 Repository의 경우 DB가 바뀌거나 아직 안 정해지는 다양한 경우를 생각해 DB 교체를 쉽게 하기 위해 Interface로 구현한다.



## Domain

```java
package hello.hellospring.domain;

import java.util.concurrent.atomic.AtomicLong;

public class MemberDomain {
    private Long id; // 실수에서는 동시성 문제를 막기 위해 AtomicLong을 사용
    private String name;

    public AtomicLong getId() {
        return id;
    }

    public void setId(Long id) {
        this.id = id;
    }

    public String getName() {
        return name;
    }

    public void setName(String name) {
        this.name = name;
    }
}
```

- 실무에서는 동시성 문제를 해결하기 위해 Thread Safe한 AtomicLong을 사용한다.
- primitive type인 long이 아니라 Wrapper Class인 Long을 사용한 이유
  - `null을 사용`할 수 있다
  - primitive type은 기본값이 0으로 설정되기 때문에 id가 없을 때를 나타낼 수가 없다

## Repository

#### Interface

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;
import java.util.List;
import java.util.Optional;
import java.util.concurrent.atomic.AtomicLong;

public interface MemberRepository {
     Member save(Member member);
     Optional<Member> findById(Long id);
     Optional<Member> findByName(String name);
     List<Member> findAll();
}
```

#### 구현체

```java
package hello.hellospring.repository;

import hello.hellospring.domain.Member;

import java.util.ArrayList;
import java.util.List;
import java.util.Map;
import java.util.Optional;
import java.util.concurrent.ConcurrentHashMap;

public class MemoryMemberRepository implements MemberRepository{
    private Map<Long, Member> store = new ConcurrentHashMap<>();
    private static Long sequence = 0L;

    // 실무에서는 동시성을 고려해 ConcurrentHashMap과 AtomicLong을 사용
    @Override
    public Member save(Member member) {
        member.setId(++sequence);
        store.put(member.getId(), member);
        return member;
    }

    @Override
     public Optional<Member> findById(Long id) {
        return Optional.ofNullable(store.get(id));
    }

    @Override
    public Optional<Member> findByName(String name) {
        return store.values().stream()
                .filter(member -> member.getName().equals(name))
                .findAny();
    }

    @Override
    public List<Member> findAll() {
        return new ArrayList<>(store.values());
    }
}
```

- 실무에서는 Thread Safe한 ConcurrentHashMap과 AtomicLong을 사용한다
- `Optional` : `NullPointException(NPE)을 방지`할 수 있게 도와주는 클래스. `NULL이 반환될 가능성이 있는 메소드에 사용`해 필요한 경우에만 사용하는 것이 바람직하다.