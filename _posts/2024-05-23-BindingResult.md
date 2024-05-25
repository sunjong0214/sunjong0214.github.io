---
title : BindingResult
categories : [Spring]
tags : [bindingResult, spring]
---



BindingResult를 통해 서버 사이드에서 검증할 때 MVC 패턴에서는 타임리프를 이용해 쉽게 error 메시지 출력이 가능했다.

그러다 프로젝트 중 요구사항이 변경되어 MVC가 아닌 Rest API 방식으로 검증 오류 메시지 출력이 필요해졌다.



## MVC에서의 BindingResult

```java
@PostMapping("edit")
public String update(@Validated @ModelAttribute Member member, BindingResult bindingResult) {
	if (bindingResult.hasErrors()) {
        return "member/edit";
    }
    //////
    
    return "redirect:/";
}
```

다음과 같이 error가 발생되면 @ModelAttribute가 붙어있는 Member 객체에 bindingResult가 함께 model에 담겨 랜더링이 가능했다.

## 요구사항 변경에 따른 문제

기존의 경우 수정 버튼 눌렀을 시 새로운 페이지를 랜더링해서 오류 메시지를 출력했다.

하지만 요구사항이 변경되면서 회원정보 수정 버튼을 눌렀을 때 alert 창에 메시지가 출력되게 바꿔야했다.

처음에는 bindingResult 객체 그 자체를 응답바디에 담아서 보내려고 했는데 이렇게 하면 원하는 메시지를 뽑아내기 힘들었다.

#### ex) BindingResult 객체를 응답 바디에 담을 시

```java
{
    "timestamp": "2024-05-23T04:10:03.285+00:00",
    "status": 500,
    "error": "Internal Server Error",
    "trace": //생략
    "message": "Type definition error: [simple type, class org.springframework.validation.DefaultMessageCodesResolver]; nested exception is com.fasterxml.jackson.databind.exc.InvalidDefinitionException: No serializer found for class org.springframework.validation.DefaultMessageCodesResolver and no properties discovered to create BeanSerializer (to avoid exception, disable SerializationFeature.FAIL_ON_EMPTY_BEANS) (through reference chain: org.springframework.validation.BeanPropertyBindingResult[\"messageCodesResolver\"])",
    "path": "/study/update"
}
```



이런 문제를 해결하기 위해 서버에서 BindingResult 객체의 error 메시지를 뽑아냈다.



```java
List<String> errorMessages = bindingResult.getFieldErrors().stream().map(FieldError::getDefaultMessage).toList();
```

스트림을 통해 List로 errorMessage를 뽑아냈다.

```java
결과 -> ["제목을 입력해주세요."]
```

리스트를 response 바디에 바로 담으면 Json의 key-value로 파싱이 되질 않아 객체를 만들어 처리해줬다.



## Rest API 방식

```java
@Data
public class ErrorMessage {
    private List<String> message;

    public ErrorMessage(List<String> message) {
      this.message = message;
    }
}
```



#### Controller

```java
@PostMapping("edit")
public ResponseEntity<ErrorMessage> update(@Validated @ModelAttribute Member member, BindingResult bindingResult) {
	if (bindingResult.hasErrors()) {
      ErrorMessage errorMessages = new ErrorMessage( bindingResult.getFieldErrors().stream().map(FieldError::getDefaultMessage).toList());
      return new ResponseEntity<>(errorMessages, HttpStatus.BAD_REQUEST);
    }
    //////
    
    return new ResponseEntity<>(HttpStatus.OK);
}
```



```java
결과 -> {"message":["제목을 입력해주세요."]}
```

다음과 같이 Json으로 잘 파싱된 것을 확인 할 수 있다.

