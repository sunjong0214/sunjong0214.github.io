---
title : JPA BatchSize에 대해
category : [JPA]
tags : [spring, jpa, batchsize]
---



프로젝트 중 트러블 슈팅 정리 내용과 BatchSize를 사용 시 주의할 점에 대해 정리한 글



## 문제 상황

게시글 불러오는데 게시글 숫자만큼 사진을 가져오는 쿼리가 나감 (N+1 문제)

- 게시글 로딩이 3초가량 걸림

![image](https://github.com/user-attachments/assets/eca24d39-153f-44be-8143-45f6948ff2d8)



## 해결

```java
 @OneToMany(mappedBy = "board", cascade = CascadeType.ALL)
 @BatchSize(size = 20) // or application.properties에 default-batch-size 설정
 private List<BoardPhoto> photos = new ArrayList<>();
```

BatchSize를 적용해  연관된 사진을 in 절로 한번에 불러오는 batch size를 도입함

- 게시글 로딩 3s -> 1s로 성능 향상

![image](https://github.com/user-attachments/assets/bc7a393b-c1f3-4c4a-84d4-7de00c5c8093)



## Batch Size 사용 시 주의할 점

보통의 RDB들은 select * from x from in (? ...) 과 같은 preparedstatement는 미리 문법을 파싱해서 최대한 캐싱을 해둔다.

만약 1개, 2개, 3개, 100개를 찾아야할 때 다음처럼 각각 100개의 preparedstatement 쿼리를 만들어야한다.

- select * from x where in (?)

- select * from x where in (?, ?)

- select * from x where in (?, ?, ?)

- select * from x where in (?, ?, ? ...)

이렇게 되면 DB 입장에서는 너무 많은 쿼리를 캐싱해야 하기 때문에 성능이 떨어지게 된다.

그래서 하이버네이트는 이 문제를 해결하기 위해 내부에서 최적화를 한다.

설정한 batch_size가 100일 때

- 100 = 설정값
- 50 = 100/2
- 25 = 50/2
- 12 = 25/2

- 1 ~ 10은 자주 사용하니 모두 캐싱

이런 식으로 하면 preparedstatement 모양을 총 14개의 모양으로 최적화가 가능하다.



## 하이버네이트 6.2 변경사항

- 위에서 batch_size를 사용시 캐싱에 대해 설명했다.

- 하지만 하이버네이트 6.2로 들어서면서 where in 문법 대신 array_contains를 사용하면서 위와 같은 문제를 해결했다.

- `where in 문법`

  - preparedstatement 캐싱해서 사용해도 size = 100 일 때 14개의 SQL 문을 캐싱해야됨

  - ex)

    ```java
    where item.item_id in(?)
    where item.item_id in(?,?)
    where item.item_id in(?,?,?,?)
    ```

- `array_contains 문법`

  - where in과 결과는 같지만 성능 최적화 가능

  - array_contains는 배열을 이용해서 하나의 SQL 문 캐싱으로 해결 가능

  - () 안에 왼쪽에 배열이 들어가고 오른쪽에 들어간 값과 같다면 참으로 반환

  - ex)

    ```java
    select ... where array_contains(?,item.item_id)
    --------------------------------------------------
    //where in VS array_contains
        
    //array_contains
    select ... where array_contains([1,2,3],item.item_id) 
    //where in
    select ... item.item_id where in(1,2,3)
    ```
  
  - 위 두 SQL 문은 같은 결과를 반환한다.
  
  - 이로써 하나의 SQL 문을 캐싱하고 동적으로 늘어나도 같은 SQL 구문을 그대로 사용해 성능 최적화가 가능하다.



출처)

[default_batch_fetch_size 관련질문 - 인프런 (inflearn.com)](https://www.inflearn.com/questions/34469/default-batch-fetch-size-관련질문)

