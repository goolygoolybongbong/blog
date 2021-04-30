---
layout: single
classes: wide
categories: spring
title: "JdbcTemplate"
---

스프링은 JDBC를 이용하는 DAO에서 사용할 수 있도록 준비된 다양한 템플릿과 콜백을 제공한다. 자주 사용되는 패턴을 가진 콜백은 다시 템플릿에 결합시켜서 간단한 메소드 호출만으로 사용이 가능하도록 만들어져 있다.

## update()

업데이트 카운트를 리턴하며 `PreparedStatementCreator`를 콜백을 받거나 혹은 SQL문 스트링 한 줄만 넘겨 내장 콜백을 사용하도록 해도 된다.

## query()

- 인자로 두 개의 콜백`PreparedStatementCreator`, `ResultSetExtractor` 을 넘겨주는 경우

    `ResultSetExtractor`의 콜백에서 정의된 타입에 따라 추출, 반환형은 `ResultSetExtractor`에서 결정된다.

- 인자로 Stirng SQL문과 `RowMapper` 콜백을 넘겨주는 경우

    쿼리문(여러 개의 로우가 결과로 나오는 경우)의 출력값과 `RowMapper`정의한 타입 DTO 클래스에 맵핑한다. 반환형은 `RowMapper<T>`에서 결정된 `T`의 `List<T>`형이 반환된다.

    > 이 query메소드는 `RowMapper<T>`을 인자를 받는 템플릿 `class RowMapperResultSetExtractor<T> implements ResultSetExtractor**<List<t>>**` 클래스를 이용하여 `ResultSetExtractor<>`의 타입이 `<List<T>>`로 정의됨에 따라 이어서 호출된 다음 `query`의 리턴형이 `<T>` 로 보여도 `<List<T>>`형을 반환함

## queryForObject()

특정 오브젝트 단 한 개를 받기 위해 사용되는 메소드로 쿼리문, 목표 오브젝트 클래스를 넘겨주면 해당 클래스의 오브젝트를 받을 수 있다.

## 출처

토비의 스프링(책)
