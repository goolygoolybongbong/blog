---
title: "핸들러 메소드 @ModelAttribute"
layout: single
classes: wide
categories: [Spring]
tags: [Spring-MVC]
---

### `@ModelAttribute`

- 여러 곳(URI 패스, 요청 매개변수, 세션 등..)에 있는 단순 타입 데이터를 **복합 타입 객체**로 받아오거나 해당 객체를 새로 만들 때 사용할 수 있다.
- 생략할 수 있다.

- 값을 바인딩할 수 없는 경우 `BindingException`과 400(Bad Request) Error가 발생한다.

- 바인딩 에러(`BindingException`)를 직접 다루고자 하는 경우: `BindingResult`타입의 아규먼트를 `@ModelAttribute`의 바로 오른쪽에 추가한다.

- 바인딩 후 검증 작업 추가 하기

  - `spring-boot-starter-validation`를 의존성에 추가해 준다.
  - 복합 타입을 정의하는 필드에 적절한 검증 제약사항 애노테이션을 붙여 적용한다.
  - 제약 사항의 그룹화 하기 위해서는 **비어있는 인터페이스**를 복합 타입 내부에 정의하고 검증 제약사항 애노테이션에 `groups` 값으로 원하는 정의한 비어있는 인터페이스를 추가하면 된다.
  - 제약사항을 적용하기 위해  `@ModelAttribute`가 붙은 아규먼트 앞에 그룹이 따로 없는 경우 `@Valid` 애노테이션, 그룹이 있는 경우 `@Validated`애노테이션을 사용하며 `value`값으로 그룹 인터페이스 타입을 넘기면 된다.

