---
title : cursor based pagination에 대해
category : [Spring, 프로젝트]
tags : [paging, cursor, db, index] 
---



기존의 offset based pagination에서 cursor based pagination으로 바꾸며 배운 내용을 정리한 내용입니다

## 문제 상황
- offset을 기준으로 페이징을 할 시 큰 숫자의 페이지 숫자 요청 시 성능 문제가 발생한다
- 그 이유는 offset의 특징 때문인데 이에 대해 알아보자

![s](https://github.com/user-attachments/assets/66e89c8e-4178-4b27-b3e5-0b4fbaf06c01)

- 위 그림과 같이 10,001째 페이지를 요청하면 그 전의 row들은 쓰이지 않고 버려지는데도 읽어야하는 상황이 발생한다 (읽고 -> 버림)
- 그렇기 때문에 페이지 번호가 커지면 읽어야될 row가 많아지고 점점 속도가 느려지는 것 입니다
- 이런 문제를 해결하기 위해 No-offset 방식을 사용하면 됩니다
- 근데 주의할 점은 만약 화면에 페이지 번호가 필요하다면 어쩔 수 없이 offset을 사용해야합니다



## cursor based pagination(No-offset 방식)

- 만약 무한 스크롤 같이 페이지 번호가 필요없다면 no offset 방식을 사용하는게 성능에 많은 이점을 가져다 줍니다
- cursor를 기반으로 페이징 하는 방식은 쿼리 문에 offset을 사용하는게 아닌 where 문에 cursor가 되는 pk 값을 기준으로 데이터를 가져오는 방식입니다
- 이 방식은 where 문에 넣을 조건을 기준으로 order by가 되어있어야 합니다
- 예제)

```java
public List<salaries> getSalaries(int id, int pageSize) {
        return getJpaQueryFactory().selectFrom(salaries)
                .where(salaries.empNo.lt(id))
                .orderBy(salaries.empNo.desc())
                .limit(pageSize)
                .fetch();
}
```

- 위 예제는 클라이언트로부터 cursor가 되는 id 값을 받고 그 값보다 작은 데이터들을 pageSize만큼 가져오는 코드입니다
- 클라이언트로부터 그 전 페이지 마지막 데이터의 id를 보내는 식으로 동작한다
- 이런 식으로 하면 어느 페이지를 불러오든 첫 페이지를 가져오는 것과 같은 성능으로 동작한다
- 이런 방식은 where 전 안에 clustered Key를 사용하기 때문에 가능합니다



## Non-clustered Key / clustered Key

|                   | 대상                                                         | 제한                     |
| ----------------- | ------------------------------------------------------------ | ------------------------ |
| Clustered Key     | 1) PK <br />2) PK가 없을땐 유니크키<br />3) 1~2 둘다 없을 경우 6byte의 Hidden Key를 생성 (rowid) | 테이블당 1개만 존재 가능 |
| Non Clustered Key | 일반적인 인덱스                                              | 여러개 생성 가능         |

![image](https://github.com/user-attachments/assets/49d8765a-5e6a-4a40-9d38-462c6f25f6bc)

- 위 그림처럼 non clustered key들은 실제 데이터를 접근하기 위해 두번의 과정을 거친다

- Non clustered key를 통해 clustered key 탐색 -> clustered key로 실제 데이터 접근
- 하지만 clustered key는 바로 실제 데이터에 접근하기 때문에 성능에 많은 이점이 있습니다

## 정리

- 만약 페이지 번호가 필요하지 않다면 cursor based pagination을 적용하면 성능에 많은 이점이 있을 것 같습니다
- 하지만 정렬을 id 값이 아니라 생성일 등으로 해야한다면 어떤 식으로 쿼리 해야할지 고민 해봐야겠습니다



출처) 

[1. 페이징 성능 개선하기 - No Offset 사용하기 (tistory.com)](https://jojoldu.tistory.com/528)
