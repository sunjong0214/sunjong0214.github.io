---
title : MemberService 구현
categories : [Spring]
tags : [service, member]
---



Member에 사용될 비즈니스 로직을 가진 Service 클래스를 만들어보자.



## MemberService

```java
package hello.hellospring.service;

import hello.hellospring.domain.Member;
import hello.hellospring.repository.MemberRepository;
import hello.hellospring.repository.MemoryMemberRepository;

import java.util.List;
import java.util.Optional;

public class MemberService {
    private final MemberRepository memberRepository = new MemoryMemberRepository();

    /**
     * 회원가입
     * @param member
     * @return
     */
    public Long join(Member member) {
        validateDuplicateMember(member);
        memberRepository.save(member);
        return member.getId();
    }

    /**
     * 중복 회원 조회
     * @param member
     */
    private void validateDuplicateMember(Member member) {
        memberRepository.findByName(member.getName())
                .ifPresent(m -> {
                    throw new IllegalStateException("이미 존재하는 회원입니다.");
                });
    }

    /**
     * 전체 회원 조회
     * @return
     */
    public List<Member> findMembers() {
        return memberRepository.findAll();
    }

    /**
     * 회원 아이디로 조회
     * @param memberId
     * @return
     */
    public Optional<Member> findOne(Long memberId) {
        return memberRepository.findById(memberId);
    }
}
```

- validateDuplicateMember에 있는 Optional 메소드 중 하나인 `ifPresent는 NULL이 아니면 안의 코드가 작동`되는 방식이다. ( NULL이 아니면 과 같은 의미이다)

- Optional은 `직접 반환하는 방식을 권장하지 않는다.` 위 vaildateDuplicateMember와 같은 방식을 권장한다.