---
title : Stream API - map, flatMap
categories : [Java]
tags : [stream, java8, map, flatmap]  
---

자바 8의 Stream API 중 map과 flatMap의 차이에 대해 정리한 글이다.

## map VS flatMap

- `map`과 `flatMap`은 `특정 데이터를 선택하는 매핑을 위한 메서드`이다.
- 인수로 제공된 함수는 각 요소에 적용되며, 함수를 적용한 결과가 새로운 요소로 반환된다.
  - 제공된 함수의 반환 타입에 따라 새로운 Stream을 만들어 반환한다.

### .map() 사용

- ex) List 안 요소들의 단어 길이를 반환

  ```
  java코드 복사List<String> list = Arrays.asList("modern", "java", "in", "action");
  // 스트림
  List<Integer> lengths = list.stream()
                              .map(String::length)
                              .collect(Collectors.toList());
  // lengths -> [6, 4, 2, 6]
  ```

- 하지만 `map`만을 사용하면 배열이나 객체로 감싸져 있는 Stream을 처리하기 힘들다.

- ex) 각 단어를 하나하나 쪼개서 하나의 List를 결과로 받고 싶다면:

  ```
  java코드 복사List<String[]> result = list.stream()
                              .map(word -> word.split(""))
                              .collect(Collectors.toList());
  // result -> [["m", "o", "d", "e", "r", "n"], ["j", "a", "v", "a"], ["i", "n"], ["a", "c", "t", "i", "o", "n"]]
  ```

  - `map`에서 사용한 `split` 메서드는 배열을 반환하기 때문에 배열 타입을 가지는 Stream이 반환된다.
  - 그러나 우리는 모든 단어가 하나의 List로 반환되기를 원한다.
  - 물론 `map`만으로도 이를 달성할 수 있지만 필요 이상으로 복잡해진다.
  - 이럴 때 사용할 수 있는 것이 `flatMap`이다.

### .flatMap() 사용

- `flatMap`은 매핑된 모든 원소들을 단일 원소 스트림으로 반환한다. 즉, `스트림 평면화`가 가능하다.

- 각 요소에 대해 함수가 반환하는 결과가 또 다른 컬렉션일 때, 중첩된 컬렉션을 하나의 평면 컬렉션으로 만든다.

- 위의 예제를 통해 살펴보자.

  ```
  java코드 복사List<String> result = list.stream()
                            .map(word -> word.split(""))
                            .flatMap(Arrays::stream)
                            .collect(Collectors.toList());
  // result -> ["m", "o", "d", "e", "r", "n", "j", "a", "v", "a", "i", "n", "a", "c", "t", "i", "o", "n"]
  ```

  - `flatMap`을 사용하여 `String[]`을 `Stream<String>`으로 평면화한 결과를 확인할 수 있다.

### 정리

예제를 통해 `map`과 `flatMap`의 차이점에 대해 알아보았다. 단일 원소로 된 스트림을 사용하고 싶다면 `flatMap`을 사용하고, 그렇지 않다면 `map`을 사용하자.