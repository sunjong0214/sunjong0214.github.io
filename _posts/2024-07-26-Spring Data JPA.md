---
title : Spring Data JPA
category : [JPA]
tags : [jpa, spring, db]
---



Spring Data JPA에 대해 알아보자

## 쿼리 메소드 기능



#### 메소드 이름으로 쿼리 생성

- 스프링 데이터 JPA는 메소드 이름을 분석해서 JPQL을 생성해서 실행해준다.

  | Keyword                | Sample                                                       | JPQL snippet                                                 |
  | :--------------------- | :----------------------------------------------------------- | :----------------------------------------------------------- |
  | `Distinct`             | `findDistinctByLastnameAndFirstname`                         | `select distinct … where x.lastname = ?1 and x.firstname = ?2` |
  | `And`                  | `findByLastnameAndFirstname`                                 | `… where x.lastname = ?1 and x.firstname = ?2`               |
  | `Or`                   | `findByLastnameOrFirstname`                                  | `… where x.lastname = ?1 or x.firstname = ?2`                |
  | `Is`, `Equals`         | `findByFirstname`,`findByFirstnameIs`,`findByFirstnameEquals` | `… where x.firstname = ?1`                                   |
  | `Between`              | `findByStartDateBetween`                                     | `… where x.startDate between ?1 and ?2`                      |
  | `LessThan`             | `findByAgeLessThan`                                          | `… where x.age < ?1`                                         |
  | `LessThanEqual`        | `findByAgeLessThanEqual`                                     | `… where x.age <= ?1`                                        |
  | `GreaterThan`          | `findByAgeGreaterThan`                                       | `… where x.age > ?1`                                         |
  | `GreaterThanEqual`     | `findByAgeGreaterThanEqual`                                  | `… where x.age >= ?1`                                        |
  | `After`                | `findByStartDateAfter`                                       | `… where x.startDate > ?1`                                   |
  | `Before`               | `findByStartDateBefore`                                      | `… where x.startDate < ?1`                                   |
  | `IsNull`, `Null`       | `findByAge(Is)Null`                                          | `… where x.age is null`                                      |
  | `IsNotNull`, `NotNull` | `findByAge(Is)NotNull`                                       | `… where x.age is not null`                                  |
  | `Like`                 | `findByFirstnameLike`                                        | `… where x.firstname like ?1`                                |
  | `NotLike`              | `findByFirstnameNotLike`                                     | `… where x.firstname not like ?1`                            |
  | `StartingWith`         | `findByFirstnameStartingWith`                                | `… where x.firstname like ?1` (parameter bound with appended `%`) |
  | `EndingWith`           | `findByFirstnameEndingWith`                                  | `… where x.firstname like ?1` (parameter bound with prepended `%`) |
  | `Containing`           | `findByFirstnameContaining`                                  | `… where x.firstname like ?1` (parameter bound wrapped in `%`) |
  | `OrderBy`              | `findByAgeOrderByLastnameDesc`                               | `… where x.age = ?1 order by x.lastname desc`                |
  | `Not`                  | `findByLastnameNot`                                          | `… where x.lastname <> ?1`                                   |
  | `In`                   | `findByAgeIn(Collection<Age> ages)`                          | `… where x.age in ?1`                                        |
  | `NotIn`                | `findByAgeNotIn(Collection<Age> ages)`                       | `… where x.age not in ?1`                                    |
  | `True`                 | `findByActiveTrue()`                                         | `… where x.active = true`                                    |
  | `False`                | `findByActiveFalse()`                                        | `… where x.active = false`                                   |
  | `IgnoreCase`           | `findByFirstnameIgnoreCase`                                  | `… where UPPER(x.firstname) = UPPER(?1)`                     |

- 출처) [JPA Query Methods :: Spring Data JPA](https://docs.spring.io/spring-data/jpa/reference/jpa/query-methods.html)

#### @Query, 리포지토리 메소드에 쿼리 정의하기

- 메서드 위에 직접 JPQL 쿼리를 작성할 수 있다.

  ```java
  @Query("select m from Member m where m.username= :username and m.age = :age") 
  List<Member> findUser(@Param("username") String username, @Param("age") int 
  age);
  ```

- 애플리케이션 실행 시점에 문법 오류를 발견할 수 있음 (매우 큰 장점이다)

- 메소드 이름 생성 기능 사용 시 메소드 이름이 너무 길어지면 @Query 기능을 사용



#### 파라미터 바인딩

- @Query에 파라미터 바인딩이 가능하다.

- 위치 기반과 이름 기반이 있는데 위치 기반은 사용 X

  ```java
  @Query("select m from Member m where m.username = :name")
  Member findMembers(@Param("name") String username);
  //컬렉션도 가능 (in 절로 들어감)
  @Query("select m from Member m where m.username in :names") 
  List<Member> findByNames(@Param("names") List<String> names);
  ```



#### 반환 타입

- 스프링 데이터 JPA는 유연한 반환 타입 지원 - 컬렉션, 단건, 단건 Optional 등
- 조회 결과가 많거나 적으면?
  - 컬렉션
    - 결과 없을 시 -> 빈 컬렉션 반환
  - 단건 조회
    - 결과 없음 -> null 반환
    - 결과가 2건 이상 -> NonUniqueResultException 예외 발생
- 참고 : 원래 조회 결과가 없으면 NoResultException이 발생하는데 이럼 개발자가 처리할 부분이 많아짐. 그래서 스프링 데이터 JPA가 이 예외를 무시하고 대신 null을 반환해줌



#### 페이징과 정렬

- 스프링이 지원해주는 Pageable 인터페이스를 사용하면 쉽게 페이징과 정렬이 가능

  ```java
  Page<Member> findByUsername(String name, Pageable pageable);  //count 쿼리 사용 
  Slice<Member> findByUsername(String name, Pageable pageable); //count 쿼리 사용 안함 
  List<Member> findByUsername(String name, Pageable pageable);  //count 쿼리 사용 안함 
  List<Member> findByUsername(String name, Sort sort);
  ```



#### left join 최적화

- 하이버네이트 6부터는 의미없는 left join을 최적화해준다.

- 예시)

  ```java
  @Query(value = "select m from Member m left join m.team t")
  Page<Member> findByAge(int age, Pageable pageable);
  //결과
  select
          m1_0.member_id, 
          m1_0.age, 
          m1_0.team_id, 
          m1_0.username
  from
          member m1_0
  ```

- 보면 left join을 하지 않는다. 왜일까?

- 잘보면 left join한 m.team이 select나 where 절에서 사용되고 있지 않다.

- 이 JPQL은 사실상 'select m from Member m'과 같다.

- 그렇기 때문에 JPA가 최적화해서 join 없이 SQL을 만들어준다.

- 만약 한번에 조회하고 싶으면 fetch join을 사용하면 된다.



#### 벌크성 수정 쿼리

- 스프링 데이터 JPA도 벌크성 수정 쿼리를 지원한다.

  ```java
  @Modifying
  @Query("update Member m set m.age = m.age + 1 where m.age >= :age") 
  int bulkAgePlus(@Param("age") int age)
  ```

- @Modifying 어노테이션을 사용해야된다. 사용하지 않을시 QueryExecutionRequestException 예외가 발생한다.

- 만약 벌크성 수정 쿼리 후 자동으로 영속성 컨텍스트를 초기화하고 싶다면 @Modifying(clearAutomatically = true) 속성을 추가하면 된다.

- 벌크 연산은 영속성 컨텍스트를 무시하고 DB에 직접 실행되기 때문에 DB와 영속성 컨텍스트 1차 캐시의 값이 달라지는 상황이 발생할 수 있다.

- 권장하는 방안

  - 영속성 컨텍스트에 엔티티가 없는 상태에서 벌크 연산을 먼저 실행한다.
  - 부득이하게 영속성 컨텍스트에 엔티티가 있으면 벌크 연산 직후 영속성 컨텍스트를 초기화 한다.



#### @EntityGraph

- 연관된 엔티티를 SQL 한번에 조회하는 방법

- 패치 조인을 @Query를 사용해 직접 작성하지 않아도 된다.

- 사실상 페치 조인(FETCH JOIN)의 간편 버전, LEFT OUTER JOIN 사용한다.

  ```java
  //공통 메서드 오버라이드
  @Override
  @EntityGraph(attributePaths = {"team"}) 
  List<Member> findAll();
  //JPQL + 엔티티 그래프
  @EntityGraph(attributePaths = {"team"})
  @Query("select m from Member m")
  List<Member> findMemberEntityGraph();
  //메서드 이름으로 쿼리에서 특히 편리하다.
  @EntityGraph(attributePaths = {"team"}) 
  List<Member> findByUsername(String username)
  ```



## 확장 기능



#### 사용자 정의 구현 클래스

- 스프링 데이터 JPA가 제공하는 메소드가 아니라 직접 매소드를 구현하고 싶을 때
- 주로 Querydsl랑 사용
- 규칙 : 인터페이스이름 + Impl -> 스프링 데이터 JPA가 인식해서 스프링 빈에 등록



#### Auditing

- 엔티티 생성, 변경할 때 등록일, 수정일 등을 추적할 때 사용

  ```java
  @EntityListeners(AuditingEntityListener.class) 
  @MappedSuperclass
  @Getter
  public class BaseEntity { 
      @CreatedDate
      @Column(updatable = false) 
  	private LocalDateTime createdDate;
      @LastModifiedDate
  	private LocalDateTime lastModifiedDate; 
  }
  ```

- 스프링 부트 설정 클래스에 @EnableJpaAuditing 적용 해야됨

- 사용하고 싶은 엔티티에 상속받아서 사용하면 됨



#### 페이징과 정렬

- 컨트롤러 계층에서 Pageable 인터페이스를 파라미터로 받아서 사용 가능

- 설정

  ```java
  spring.data.web.pageable.default-page-size=20 /# 기본 페이지 사이즈/ 
  spring.data.web.pageable.max-page-size=2000 /# 최대 페이지 사이즈/
  ```

- 클라이언트에서 쿼리 파라미터로 사이즈나 정렬 조건 등을 받을 수 있음 -> 넘어온 조건들에 알맞게 PageRequest 객체 만들어줌

- Page는 stream()과 같은 map을 지원해 내부 데이터 변경 가능



## 스프링 데이터 JPA가 만들어준 구현체

- 스프링 데이터 JPA 인터페이스를 사용하면 스프링 데이터 JPA가 SimpleJpaRepository라는 구현체를  만들어준다.
- 이 구현체를 잘 살펴보면 JPA의 예외를 스프링 예외로 변환하기 위해 @Repository가 달려있는 것을 확인할 수 있다. (@Repository에는 스프링 예외 변환기가 포함되어있다.)
- 또 @Transactional이 걸려있어 만약 서비스 단에서 트랜잭션을 시작 안하고 사용해도 그냥 JPA와 달리 예외가 터지지 않는다. 단, 이렇게 할시 Repostiory 계층을 벗어나면 트랜잭션이 끝나기 때문에 주의하자



#### save() 메서드 주의 사항

- SimpleJpaRepository의 save()를 사용 시 주의할 점은 새로 만든 엔티티라면 persist로 저장하고 그게 아니면 merge를 호출한다는 점이다.
- 새로운 엔티티를 확인하는 기준은 객체 == null, 기본 타입 == 0 으로 확인한다.
- 이 merge는 기존의 값을 무시하고 덮어쓰기 때문에 주의해서 사용해야된다. 또한 merge의 경우 save하는 엔티티가 DB에 있다고 가정하고 동작하기 때문에 select 쿼리가 나간다. 
- 이런 문제 때문에저장할 목적이라면 persist를 써야한다.
- merge의 경우 영속상태를 한번 벗어난 엔티티를 다시 영속상태로 만들 때 사용한다. (영속 -> 비영속 -> merge -> 영속)

#### save() 시 @GeneratedValue를 사용 못하는 경우

- 실무를 하다보면 여러 이유에 의해 @GeneratedValue 사용 못하는 경우가 있다.

- 그럼 내가 직접 pk 값을 지정해야되는데 위에서 살펴봤듯이 save는 null인 경우에만 persist를 호출한다.

- 그럼 우리가 직접 pk 값을 지정할 때는 어떻게 해야될까? 만약 pk값을 String으로 받는 경우에 어떻게 새로운 엔티티라는 것을 JPA에게 알려줄 수 있을까?

  ```java
  Member member = new Member("newMember");
  repository.save(member);
  ```

- 위와 같이 새로운 멤버임에도 persist가 아니라 merge가 호출된다. 이런 문제를 해결해주는 방법이 존재한다.

  ```java
  public interface Persistable<ID> {
  	ID getId(); 
  	boolean isNew();
  }
  ```

- 이 Persistable 인터페이스를 상속 받고 메소드를 직접 정의하면 된다.

  ```java
  @Entity
  @EntityListeners(AuditingEntityListener.class)
  @NoArgsConstructor(access = AccessLevel.PROTECTED) 
  public class Item implements Persistable<String> {
      @Id
      private String id; 
      
      @CreatedDate
      private LocalDateTime createdDate; 
      
      public Item(String id) {
      	this.id = id;
      }
      
      @Override
      public String getId() { 
      	return id;
      }
      
      @Override
      public boolean isNew() { 
     		return createdDate == null;
      } 
  }
  ```

- isNew() 메소드에 내가 직접 새로운 엔티티를 식별할 조건을 넣어주면 된다.

- 보통 우리가 기존의 Auditing을 사용한 경우 createDate로 식별하는 방법을 자주 사용한다. (새로운 엔티티면 DB에 insert가 안되어있기 때문에 createDate == null이다)