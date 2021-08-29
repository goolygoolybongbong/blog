---
title: "핸들러 메소드 @RequestParam(요청 매개 변수)"
layout: single
classes: wide
categories: [Spring]
tags: [Spring-MVC]
---

## `@RequestParam`(요청 매개변수)

- 요청 매개변수에 들어있는 **단순 타입 데이터를 메소드 아규먼트로** 받아올 수 있다.
- 값이 **반드시 존재**해야 한다.
  - required=false 또는 java Optional을 사용해서 부가적인 값을 설정할 수 있다.
- String이 아닌 값들의 타입 컨버전을 지원한다.
- `Map<String, String>` 또는 `MultivalueMap<String, String>`에 사용해서 모든 요청 매개변수를 받아 올 수 있다.
- 이 애노테이션은 생략이 가능하다.



### 요청 매개변수란?

- 쿼리 매개변수
- 폼 데이터



### 예제코드

```java
@PostMapping("/events")
@ResponseBody
public Event getEvent(@RequestParam String name, @RequestParam Integer limit) {
    Event event = new Event();
    event.setName(name);
    event.setLimit(limit);
    return event;
}
```



### 테스트 코드

- 쿼리 매개변수 테스트
    ```java
    @Test
    public void postEventsTest() throws Exception {
        mockMvc.perform(post("/events")
                        .param("name", "testName")
                        .param("limit", "20"))
                .andDo(print())
                .andExpect(status().isOk())
                .andExpect(jsonPath("name").value("testName"));
    }
    ```

- 핸들러 뷰, 모델 테스트 (MVC 확인)

    ```java
    @Test
    public void eventForm() throws Exception {
        mockMvc.perform(get("/events/form"))
                .andDo(print())
                .andExpect(view().name("events/form")) // 뷰 이름 확인
                .andExpect(model().attributeExists("event")); // 모델에 들어간 값 확인
    }
    ```

