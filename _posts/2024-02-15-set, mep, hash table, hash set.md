---
title : set, map과 hash function(hash table)
categories : [자료구조]
tags : [set, map, hash set, hash map, hash table]
---

이번 포스트는 ADT인  set, map과 DS(Data Structure)인 Hash map에 대해 알아보자.

## Set

- 데이터를 저장하는 ADT
- `순서를 보장하지 않음`
- `데이터 중복을 허용 X`
- 데이터 조회가 `List보다 더 빠름`

자바에서 Set을 구현한 대표적인 DS인 HashSet은 Map의 구현체인 HashMap을 사용해 구현되어 있다.



## Map

- key-value pair들을 저장하는 ADT
- 같은 key를 가지는 pair는 최대 한 개만 존재
- associative array, dictionary라고 불림

map은 key와 value를 사용하며 key라고 불리는 데이터를 통해 저장된 데이터(pair)에 접근하는 ADT이다. 

보통 우리는 Hash map(table)과 tree-based로 구현해 많이 사용한다.



## Hash map (Hash table)

- 배열과 해시 함수(hash function)를 사용해 map을 구현한 자료구조

- (일반적으로) 상수 시간으로 데이터에 접근하기 때문에 빠르다



## Hash function

- 임의의 크기를 가지는 type의 데이터를 고정된 크기를 가지는 type의 데이터로 변환하는 함수
- (hash table에서) 임의의 데이터를 정수로 변환하는 함수

![](https://upload.wikimedia.org/wikipedia/commons/thumb/5/58/Hash_table_4_1_1_0_0_1_0_LL.svg/1200px-Hash_table_4_1_1_0_0_1_0_LL.svg.png)

그림과 같이 어떤 데이터가 input으로 넣으면 hash라는 정수 값로 output 해주는 함수이다.



그럼 위의 hash function을 통해 hash table은 어떤 식으로 작동할까? 그림을 통해 알아보자

![](https://upload.wikimedia.org/wikipedia/commons/thumb/7/7d/Hash_table_3_1_1_0_1_0_0_SP.svg/1280px-Hash_table_3_1_1_0_1_0_0_SP.svg.png)

#### hash table에 데이터 저장

우선 key가 될 데이터를 hash function을 통해 hash로 mapping(대응) 시켜준다. 

그때 hash table은 내부적으로 배열을 가지고 있는데 이 배열의 capacity(용적)을 통해 배열의 어떤 index에 넣을지 정해진다.

예를 들어, 배열의 capacity가 16이고 key를 mapping 시켜준 값이 정수 194이라고 가정하자.

194 % 16을 연산 해준 뒤 나온 값인 2가 데이터가 저장될 index가 된다. 위 그림에서 John Smith의 경우가 그렇다.

위 같이 capacity를 통해 hash 값을 연산하는 것은 `모듈러 연산 (Modular arithmetic)` 이라고 부른다

결론적으로 hash table에는 `key -> hash function -> 모듈러 연산 -> 연산 값을 index로 배열에 저장` 과 같은 과정을 거치게 된다.

#### hash table에 있는 데이터 찾기

hash table에는 key 값을 통해 데이터를 가져올 수 있다.

저장할 때와 같이 key를 hash function에 넣어 hash로 mapping한 뒤 내부 배열의 capacity로 나눈 나머지 값을 통해 index에 접근한다.

만약 John Smith를 이용해 전화번호를 찾을 때 John Smith를 위의 연산을 한다면 저장할 때와 같은 2가 나올 것이고, 바로 배열의 해당 index에 접근해 빠르게 데이터를 가져올 수가 있다.

근데 만약 Kim을 hash function을 통해 연산했을 때 John Smith와 같은 2가 나왔을 때는 key값이 다른데 어떻게 둘을 구분해야될까?

이런 문제를 해결하기 위해 배열에 데이터가 저장될 때 pair 값만 아니라 key 값도 같이 저장하게 된다. 그래서 key 값에 대한 올바른 데이터를 반환할 수 있는 것이다.

#### hash collision

위에 데이터를 찾다 발생한 문제같은 것들을 통틀어 hash collision이라고 부른

- key는 다른데 hash가 같을 때
- key도 hash도 다른데 모듈러 연산의 결과가 같을 때

이런 hash collision은 피할 수 없는 문제이다. hash function과 모듈러 연산을 통하다보니 어쩔 수 없다. 즐기자...

#### hash collision 해결 방법

이런 hash collision을 해결하기 위한 방법으로 2가지 방식이 있다.

- open addressing
- separate chaining

## separate chaining

![](https://upload.wikimedia.org/wikipedia/commons/thumb/d/d0/Hash_table_5_0_1_1_1_1_1_LL.svg/675px-Hash_table_5_0_1_1_1_1_1_LL.svg.png)

separate chaining 방식은 그림과 같이 동작하는 방식이다.

만약 hash가 같아 index에 데이터가 있어 충돌할 경우 Linked List를 이용해 노드를 추가하여 값을 추가한다 (JDK 1.8 이후부터는 index에 노드가 8개 이하일 경우는 Linked List, 8개 이상일 경우 Tree 자료 구조를 이용해 select의 성능을 높임)

값을 찾는 경우에 index에 있는 노드 끝까지 돌면서 값이 있는지 확인하게 되고, 삭제일 경우 linked list에서 그 노드를 삭제하게 된다.



## open addressing

![](https://upload.wikimedia.org/wikipedia/commons/thumb/b/bf/Hash_table_5_0_1_1_1_1_0_SP.svg/570px-Hash_table_5_0_1_1_1_1_0_SP.svg.png)

open addressing의 경우 index에 대한 충돌 처리를 Lineked List 대신 배열의 빈 공간을 사용하는 방식이다.

separate chaining의 경우 Linked List를 사용해 추가적인 메모리가 필요한 반면, open addressing의 경우 빈 공간만 이용하기 때문에 메모리적으로 절약할 수 있다.

open addressing을 구현하는 방식에는 여러가지가 있는데 대표적인 Linear Probing을 기준으로 설명하겠다

linear probing의 경우 충돌이 발생시 가장 인접한 빈 index에 데이터를 추가한다

linear probing의 데이터 탐색은 원하는 값이 아닐 때 다음 index로 넘어가 탐색을 계속한다. 글다 빈 index를 만났을 때 원하는 값이 없다고 간주하고 종료된다.

데이터 삭제는 값을 찾고 존재한다면, deleted라는 표시를 삭제한 index에다 해준 뒤 종료하게 된다. 그 이유는 데이터 탐색할 때 빈 곳을 만나면 종료되기 때문에 삭제한 index의 다음 index도 같은 hash 값을 가지고 있을 수 있기 때문에 탐색을 종료하지 않게 하기 위함이다.

#### resizing

open addressing의 경우 resizing이 필수적으로 필요하다. 빈 index에다 값을 추가하다보니 빈 index가 없을 경우 문제가 발생할 수 있기 때문이다.

그때 open addressing의 경우 본래 가지고 있는 배열의 capacity * 2의 사이즈를 가지는 배열을 생성한 뒤 다시 모듈러 연산한 뒤 그 값에 맞는 index에 재배치 해준다.

이런 이유 때문에 open addressing의 경우 모듈러 연산 전 hash 값을 알아야 resizing을 할 수 있기 때문에 데이터에 key와 pair 뿐만 아니라 hash 값도 같이 들어있다.

## separate chaining VS open addressing

![image](https://github.com/sunjong0214/algorithm-study/assets/117134728/42dbfc03-aed3-4f4e-93be-048ae86f4135)

## 정리

Java에서의 HashMap은 separate chaining을 기본으로 사용하기 때문에 알아두자.

또 hash table과 hash map은 사용 방법과 key와 pair 형태인 점까지 거의 똑같다.

하지만 두 클래스의 큰 차이점은 Thread-safe와 NULL처리에 있다.

#### Hash table

- Thread-safe

- NULL을 허용 X 

#### Hash map

- Thread-safe하지 않아 성능이 더 좋음

-  NULL을 허용한다는 차이점이 있다.





출처)

[Hash table - Wikipedia](https://en.wikipedia.org/wiki/Hash_table)

[해시 함수 - 위키백과, 우리 모두의 백과사전 (wikipedia.org)](https://ko.wikipedia.org/wiki/해시_함수)

[Hash table - Wikipedia](https://en.wikipedia.org/wiki/Hash_table)

[맵(map)과 해시 테이블(hash table)](https://www.youtube.com/watch?v=ZBu_slSH5Sk&t=31s&ab_channel=쉬운코드)