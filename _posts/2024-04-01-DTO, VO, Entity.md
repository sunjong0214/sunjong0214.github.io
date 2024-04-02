---
title : DTO, VO, Entity
categories : [Project]
tags : [dto, vo, entity, db]
---



프로젝트를 진행하면서 DTO와 VO, Entity에 대해 제대로 이해하지 못한 상태에서 DB에 있는 테이블을 토대로 클래스를 만들려다다보니 어려움이 있었다.

여러 블로그를 참고해 어느정도 정리가 된 느낌이라 바로 블로그에 작성하려고 한다.



회원의 데이터를 저장하는 VO 클래스를 만들다보니 DB 칼럼과 클래스 변수들이 1대1로 매칭되는 것이 맞는건지 의문이 생겼다.

분명 회원 번호나 가입일 등과 같은 칼럼들은 DB의 auto increment나 date의 now() 함수를 사용해서 자동으로 할당이 되는 것인데 이 부분이 회원 클래스에 따로 저장해놓는 것이 맞는지 잘 모르겠다.

그래서 DTO, VO, Entity가 무엇을 뜻하는지 좀 더 명확하게 개념을 정리할 필요가 있다고 느꼈다.



## DTO (Data Transfer Object)

- `계층간 데이터 교환을 위한 객체`이다.
- 데이터 교환에만 쓰이므로 `로직을 갖지 않고, getter/setter 메소드만 갖는다.`



만약 회원의 이름, 생년월일의 정보만 필요한 경우가 있다고 가정하자.

DTO를 따로 객체로 만들어놓지 않는다면 회원의 모든 정보를 가지고 있는 객체를 가지고 여러 계층을 옮겨다녀야한다.

또 회원가입시 회원 정보 뿐만 아니라 약관 정보도 들어오고, 화면을 처리하기 위한 추가 정보들도 들어오고 또는 Member에 필요한 정보들이 화면이 아니라 데이터베이스에 더 있을 수도 있다.

이러한 이유 때문에 필요한 정보만 가지고 여러 계층을 돌아다니는 DTO 객체가 필요한 것이다.

#### 예제

Member 객체

```java
@Getter
public class Member {

  private int no;
  private String email;
  private String name;
  private String password;
  private String photo;
  private Date createdDate;

}
```

회원 가입 시 데이터를 저장할 MemberForm 객체

```java
@Getter
@Setter
public class MemberForm {
    
    private String email;
    private String name;
    private String password;

}
```

아래의 MemberForm이 DTO 객체가 된다.

## VO (Value Object)

VO는 [이 링크](https://sunjong0214.github.io/posts/VO)에서 자세히 다뤘다. 해당 링크를 참고하자.

## Entity

- 실제 `DB의 테이블과 매핑되는 객체`이다. id를 통해 각각의 Entity를 구분한다.
- VO와 마찬가지로 로직을 가질 수 있다.

#### 예제

```java
@Entity
public class Member {
   @Id
   @GeneratedValue(strategy = GenerationType.IDENTITY)
   privae Long id;
  
   @Column(nullable = false)
   private String name;
  
   @Column(nullable = false)
   private String email;
  
   @Builder
   public User(String name, String email) {
      this.name = name;
      this.email = email;
   }
  
   public User update(String name, String email) {
      this.name = name;
      this.email = email;
      return this;
   }
}
```



## 정리

- DTO는 계층간 데이터 이동을 위해 사용되는 객체
- VO는 불변한 데이터를 갖는 객체
- Entity는 이를 DB에 매핑하는 객체



![](https://user-images.githubusercontent.com/33862991/116250387-a46ec800-a7a8-11eb-8368-8e2df28225fa.png)



#### Entity와 DTO를 분리하는 이유

- DB 계층과 View 계층 사이의 역할을 분리하기 위해
- DTO의 목적은 계층간의 데이터를 주고 받기 위한 객체이므로 일회성으로 사용되는 경우가 많다. 그렇기 때문에 실제 DB와 매핑되는 Entity와 분리해야한다.

출처)

[DTO와 VO 그리고 Entity의 차이](https://youngjinmo.github.io/2021/04/dto-vo-entity/)

[ DTO, VO, Entity 란? (tistory.com)](https://wjddntjr555w.tistory.com/50)