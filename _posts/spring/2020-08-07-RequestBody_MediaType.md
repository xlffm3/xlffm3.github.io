---
title: "Post 요청과 Content-Type 및 WebTestClient 테스트"
excerpt: "WebTestClient로 Form Data 테스트를 작성해보자."
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-08-07T18:22:00-05:00
---

## 1. Form Data

RestAPI는 JSON을 주로 사용하기 때문에, Content-Type을 application/json로 설정해서 테스트를 진행했다. 그러자 415 에러가 당당하게 터져나왔다.

> Controller.java

```java
@PostMapping("/form")
public String form(@ModelAttribute DTO dto) {
    return "main";
}
```

문제가 되는 부분을 보니 요청을 HTML에서 Form Data로 받고 있었다. 검색해보니 Ajax 및 HTML Form Data는 MediaType이 application/x-www-form-urlencoded 였다.

> ControllerTest.java

```java
@Test
public void 폼_데이터_테스트() {
    webTestClient.method(HttpMethod.POST)
            .uri("/")
            .contentType(MediaType.APPLICATION_FORM_URLENCODED)
            .body(BodyInserters.fromFormData("name", "test")
                    .with("content", "test"))
            .exchange()
            .expectStatus()
            .isOk();
}
```

위와 같이 MediaType을 적합하게 변경해주고, body 메소드를 BodyInserters를 통해 값을 Mock해준다.

<br>

## 2. JSON Data

> Controller.java

```java
@PostMapping("/req")
public String req(@RequestBody DTO dto) {
    return "articles";
}
```

JSON을 처리하는 @RequestBody가 선언된 경우 다음과 같이 테스트를 진행할 수 있다.

> ControllerTest.java

```java
@Test
public void RequestBody_테스트() {
    String inputJson = "{\"name\":\"test\", \"content\":\"test\"}";
    webTestClient.method(HttpMethod.POST)
            .uri("/req")
            .contentType(MediaType.APPLICATION_JSON)
            .body(Mono.just(inputJson), String.class)
            .exchange()
            .expectStatus()
            .isOk();
}

public void RequestBody_테스트2() {
    Mono<DTO> dtoMono = ...
    webTestClient.method(HttpMethod.POST)
            .uri("/req")
            .contentType(MediaType.APPLICATION_JSON)
            .body(dtoMono, DTO.class)
            .exchange()
            .expectStatus()
            .isOk();
}
```

JSON 데이터를 테스트하는 것은 Mockito가 메서드 명이 더 직관적이라 편한 느낌이다. ObjectMapper 등으로 JSON String을 만들거나, Mono<T>를 선언해서 테스트를 진행할 수 있다.

<br>

---

## Reference

* [[Spring] Post 요청과 Content-Type의 관계](https://blog.naver.com/writer0713/221853596497)
