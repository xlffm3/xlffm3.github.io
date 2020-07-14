---
title: "Spring Boot 개념과 활용 - 16장 : HATEOAS"
date: 2020-07-05 19:40:29
thumbnail: https://user-images.githubusercontent.com/56240505/86525119-eea35780-bebd-11ea-8fbd-ceacfdfae2c6.png
categories:
- Back-End
- Spring & Spring Boot
tags: [Spring Boot]
---

## HATEOAS

> UserController.java

```java
@GetMapping("/hate")
public Resource<Hello> hate() {
    Hello hello = new Hello();
    hello.setPrefix("Hey,");
    hello.setName("jipark");

    Resource<Hello> helloResource = new Resource<>(hello);
    helloResource.add(linkTo(methodOn(UserController.class).hate())).withSelRel();
    return helloResource;
}
```

<br>

> UserControllerTest.java

```java
@Test
public void hateoas() throws Exception {
    mockMvc.perform(get("/hate"))
            .andExpect(status().isOk())
            .andExpect(jsonPath("$._links.self").exists());
}
```

* Hypermedia As The Engine Of Application State.
  * 서버 : 현재 리소스와 연관된 링크 정보를 클라이언트에게 제공한다.
  * 클라이언트 : 연관된 링크 정보를 바탕으로 리소스에 접근한다.
* 연관된 링크 정보.
  * Relation.
  * Hypertext Reference.
* 의존성 : spring-boot-starter-hateoas.

<br>

## 그 외

* ObjectMapper 제공.
  * 커스터마이징 : spring.jackson.*
  * Jackson2ObjectMapperBuilder.
* LinkDiscovers 제공
  * 클라이언트 쪽에서 링크 정보를 Rel 이름으로 찾을때 사용할 수 있는 XPath 확장 클래스이다.

<br>

---

## Reference

* 스프링 부트 개념과 활용 (백기선, Inflearn)
