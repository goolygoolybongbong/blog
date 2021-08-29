---
layout: single
classes: wide
categories: [Spring]
title: "HTTP 메시지 컨버터(Resource Handler)"
tags: [Spring-MVC]
---

## HTTP 메시지 컨버터

- 요청 본문에서 메시지를 읽어들이거나(@RequestBody), 응답 본문에 메시지를 작성할때(@ResponseBody) 사용한다.

- Ctrl + Shift + T : 테스트 작성하기
- 기본 HTTP 메시지 컨버터
  - 바이트 배열 컨버터
  - 문자열 컨버터
  - Resource 컨버터
  - Form 컨버터(Form 데이터 to/from MultiValueMap<String, String>)
  - (JAXB2, Jackson, Jackson2, Gson, Atom, RSS) 이들은 Gradle에서 추가해야함



### 설정 방법

- 기본으로 등록해주는 컨버터에 새로운 컨버터 추가하기 위해서는 `WebMvcConfigurer`의 `extendMessageConverters` 메소드를 구현한다
- 기본으로 등록해주는 컨버터는 전부 무시하고 새로운 컨버터를 추가하기 위해서는 `WebMvcConfigurer`의 `configureMessageConverters` 메소드를 구현한다.
- **의존성 추가로 컨버터 등록하기 (추천하는 방식)**
  - Maven 또는 Gradle 설정에 의존성을 추가하면 그에 따른 컨버터가 자동으로 등록된다.
  - `WebMvcConfigurationSupport`(Spring MVC에서 제공하는 클래스) 클래스패스에 컨버터 라이브러리의 존재 유무를 파악하여 자동으로 메시지 컨버터를 등록하는 클래스



```java

// springboot 에서는 ObjectMapper 가 이미 빈으로 등록되어 있다.
@Autowired
ObjectMapper objectMapper;

@Test
public void jsonMessage() throws Exception {
    Person person = new Person();
    person.setName("adsf");
    person.setId(10L);

    String jsonString = objectMapper.writeValueAsString(person);

    this.mockMvc.perform(get("/jsonMesage")
                         .contentType(MediaType.APPLICATION_JSON) // JSON으로 요청을 보내고
                         .accept(MediaType.APPLICATION_JSON) // JSON으로 응답이 오기를 바란다.
                         .content(jsonString))
        .andDo(print())
        .andExpect(status().isOk());
}
```

