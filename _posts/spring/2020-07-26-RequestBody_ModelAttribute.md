---
title: "Spring : @ModelAttribute vs @RequestBody"
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-07-26T13:22:00-05:00
---

## 1. @ModelAttribute

@ModelAttribute는 전달받은 파라미터들을 Java Object로 맵핑하기 때문에, 바인딩하려는 객체에 Setter 메서드가 존재해야 한다.

name과 content라는 문자열 필드를 지닌 DTO 객체가 있다. 컨트롤러가 @ModelAttribute를 통해 POST 요청을 DTO 객체로 바인딩한다 가정해보고 테스트를 짜보았다.

> ParametersTest.java

```java
@Test
public void 파라미터_전송_바인딩_성공() throws Exception {
    mockMvc.perform(post("/modelattribute")
            .param("name", "model")
            .param("content", "attribute"))
            .andExpect(status().isOk());
}
```

HTTP Request Parameters로 전달된 값들은 정상적으로 DTO 객체에 바인딩한다.

> JSONTest.java

```java
@Test
public void 본문_JSON_전송_실패() throws Exception {
    DTO dto = new DTO("model", "attribute");

    mockMvc.perform(post("/modelattribute")
           .content(objectMapper.writeValueAsString(dto))
           .contentType(MediaType.APPLICATION_JSON_VALUE))
           .andExpect(status().isBadRequest());
```

Http Request Body(요청 본문)에 있는 값(JSON)을 DTO 객체에 바인딩하지 못한다.

<br>

## 2. @RequestBody

@RequestBody는 Http Request Body(요청 본문)의 JSON 및 XML값을 HttpMessageConveter를 통해 타입에 맞는 객체로 **변환**한다. 따라서 객체에 바인딩하는 @ModelAttribute과 다르게, 대상 객체에 Setter 메서드가 없어도 된다.

이번에는 @RequestBody를 통해 POST 요청을 DTO 객체로 변환한다 가정해보고 테스트를 짜보았다.

> JSONTest.java

```java
@Test
public void 본문_JSON_전송_성공() throws Exception {
    DTO dto = new DTO("request", "body");

    mockMvc.perform(post("/requestbody")
           .content(objectMapper.writeValueAsString(dto))
           .contentType(MediaType.APPLICATION_JSON_VALUE))
           .andExpect(status().isOk());
```

요청 본문의 JSON 값을 정상적으로 DTO 객체로 변환시킨다.

> HtmlFormDataTest.java

```java
@Test
public void 파라미터_전송_바인딩_실패() throws Exception {
    mockMvc.perform(post("/requestbody")
            .contentType(MediaType.APPLICATION_FORM_URLENCODED))
            .param("name", "request")
            .param("content", "body"))
            .andExpect(status().isBadRequest());
}
```

당연하게도 Http Request Parameters로 전달되는 값들은 @RequestBody가 DTO 객체로 변환할 수 없다. Parameters를 통해 데이터를 보내는 대표적인 예가 HTML Form Data이다. Content-Type이 JSON이 아닌 application/x-www-form-urlencoded기 때문에, @RequestBody로 처리할 수 없다.

Form Data를 보낼 때 contentType을 억지로 ```MediaType.APPLICATION_JSON_VALUE``` 로 바꾼다면 415 에러가 뜨니 조심하자.

<br>

---

## Reference

* [[Spring] @RequestBody, @ModelAttribute, @RequestParam의 차이](https://mangkyu.tistory.com/72)
