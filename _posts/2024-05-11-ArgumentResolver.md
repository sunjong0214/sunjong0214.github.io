---
title : ArgumentResolver로 로그인 세션 관리
categories : [Spring, 프로젝트]
tags : [spring, argumentresolver, session, login]
---



스프링의 ArgumentResolver를 이용해 로그인 세션을 확인하는 중복 로직을 줄여보자.

## 기존 로직

```java
@GetMapping("/")
public String homeController(
        @SessionAttribute(name = SessionConst.LOGIN_MEMBER, required = false) 
		Member loginMember,
		Model model) {
	//세션에 회원 데이터가 없으면 home 
	if (loginMember == null) {
		return "home";
    }
    //세션이 유지되면 로그인으로 이동
	model.addAttribute("member", loginMember); 
	return "loginHome";
}
```

위와 같이 Session에 @SessionAttribute을 붙이고 Session에 loginMember를 확인하는 로직이다.

이런 로직이 로그인 유저를 확인해야하는 여러 컨트롤러에서 중복적으로 이뤄지고 있었다.



## @LoginUser 어노테이션 적용

```java
@GetMapping("/")
public String homeController(
        @LoginUser Member loginMember,
		Model model) {
    
    //세션이 유지되면 로그인으로 이동
    model.addAttribute("member", loginMember); 
	return "loginHome";
}
```

@LoginUser 어노테이션을 지정한 뒤 ArgumentResolver를 통해 Session 관련 로직을 처리하자.

```java
@Target(ElementType.PARAMETER) // 파라미터에서 사용됨
@Retention(RetentionPolicy.RUNTIME) // Runtime에서 사용
public @interface LoginUser {
}
```

```java
@RequiredArgsConstructor
@Component
public class LoginUserArgumentResolver implements HandlerMethodArgumentResolver {

  private final HttpSession session;

  @Override
  public boolean supportsParameter(MethodParameter parameter) {
    boolean isLoginUserAnno = parameter.getParameterAnnotation(LoginUser.class) != null;
    boolean isMemberDtoClass = MemberDto.class.equals(parameter.getParameterType());
    return isLoginUserAnno && isMemberDtoClass;
  }

  @Override
  public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
      NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
      HttpServletRequest request = (HttpServletRequest) webRequest.getNativeRequest();
    HttpSession session = request.getSession();
    return session.getAttribute("loginUser");
  }
}
```



#### 문제 코드

```java
  @Override
  public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
      NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {
    HttpServletRequest request = (HttpServletRequest) webRequest.getNativeRequest();
    HttpSession session = request.getSession();   
    return session.getAttribute("loginUser");
  }
```

위와 같이 session을 request.getSession을 실행할 시 만약 session이 있다면 정상 작동하지만 session == null일 시 로그인한 유저가 없음에도 session을 생성해준다. (Session의 특징)

또한 supportsParameter 메서드에서 class 타입을 확인하는 부분에서 equals보다는 isAssignableFrom이 더 적절해 리팩토링이 필요했다.

![image](https://github.com/sunjong0214/algorithm-study/assets/117134728/8c29310e-0bdb-4182-a132-d59f9800c21c)

이런 문제를 해결해보았다.

```java
@RequiredArgsConstructor
@Component
public class LoginUserArgumentResolver implements HandlerMethodArgumentResolver {

//  private final HttpSession session;

  @Override
  public boolean supportsParameter(MethodParameter parameter) {
    boolean isLoginUserAnno = parameter.getParameterAnnotation(LoginUser.class) != null;
    boolean isMemberDtoClass = MemberDto.class.isAssignableFrom(parameter.getParameterType());
    return isLoginUserAnno && isMemberDtoClass;
  }

  @Override
  public Object resolveArgument(MethodParameter parameter, ModelAndViewContainer mavContainer,
      NativeWebRequest webRequest, WebDataBinderFactory binderFactory) throws Exception {

    HttpServletRequest request = (HttpServletRequest) webRequest.getNativeRequest();
    HttpSession session = request.getSession(false);
    if (session == null) {
      return null;
    }
    return session.getAttribute("loginUser");
  }
}
```

webRequest에서 HttpSevletRequest를 꺼낸 뒤 Session을 메서드 안에서 꺼내주었다. 또 getSession(false)로 false 값을 넘겨주면 session == null이라도 새로 session을 생성하지 않아 불필요한 session 생성을 방지해주었다.

마지막으로 WebConfig에 등록해주면 끝이다.

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {
  private final LoginUserArgumentResolver loginUserArgumentResolver;

  @Override
  public void addArgumentResolvers(List<HandlerMethodArgumentResolver> resolvers) {
    resolvers.add(loginUserArgumentResolver);
  }
}
```

