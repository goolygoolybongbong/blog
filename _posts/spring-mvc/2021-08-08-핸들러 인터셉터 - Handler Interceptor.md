---
layout: single
classes: wide
categories: [Spring]
title: "핸들러 인터셉터 - Handler Interceptor"
tags: [Spring-MVC]
---



## Handler Interceptor

- 핸들러 맵핑에 설정할 수 있는 인터셉터
- 아래의 3가지 시점에 부가적인 작업을 하고 싶은 경우에 사용한다.
  - 핸들러 실행 **전** 시점 (**렌더링 이전**) 
  - 핸들러 실행 **후** 시점 (**렌더링 이전**)
  - 핸들러 및 렌더링을 마친 **완료** 시점
- 여러 핸들러에서 반복적인 동작을 정의할 때 사용(로깅, 인증 체크, Locale 변경 등..)





### `boolean preHandle(HttpServletRequest request, HttpServletResponse response, Object handler)`

- 핸들러를 실행하기 전에 호출된다.
- 3번째 인자로 받은 handler를 통해 서블릿 필터에 비해 보다 세밀한 로직을 구현할 수 있다.  handler를 처리하거나, 특정 handler를 차단하거나 인수를 바꾸는 등 활용할 수 있다.
- 리턴값으로 계속 다음 인터셉터 또는 핸들러로 요청, 응답을 전달(true)할지 응답 처리가 끝났는지(false)를 알린다.
- `false`를 리턴하면 해당 인터셉터의 `afterCompletion`을 건너뛴다.





### `void postHandle(HttpServletRequest request, HttpServletResponse response, Object handler, ModelAndView modelAndView)`

- 핸들러 실행이 끝나고 아직 뷰를 랜더링 하기 이전에 호출 된다.
- preHandle 이 후 뷰의 렌더링이 끊기면 호출되지 않는다.
- `modelAndView` 홀더를 통해 뷰에 전달할 추가적이거나 여러 핸들러에 공통적인 모델 정보를 담는데 사용할 수도 있다.
- 이 메소드는 인터셉터 역순으로 호출된다.
- 비동기적인 요청 처리 시에는 호출되지 않는다.





### `void afterCompletion(HttpServletRequest request, HttpServletResponse response, Object handler, Exception ex)`

- 요청 처리가 완전히 끝난 뒤(뷰 랜더링 끝난 뒤)에 호출 된다.
- `preHandler`에서 `true`를 리턴한 경우에만 호출된다.
- 이 메소드는 인터셉터 역순으로 호출된다.
- 비동기적인 요청 처리 시에는 호출되지 않는다





## 서블릿 필터와 비교

- 서블릿 보다 구체적인 처리가 가능하다.
- 서블릿은 보다 일반적인 용도의 기능을 구현하는데 사용하는 것이 좋다.
