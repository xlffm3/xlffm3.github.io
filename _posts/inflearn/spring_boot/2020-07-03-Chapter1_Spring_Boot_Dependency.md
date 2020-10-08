---
title:  "Spring Boot 개념과 활용 - 1장 : 의존성 관리"
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-07-11T08:03:00-05:00
---

## Spring Boot의 의존성 관리

```xml
<!-- Inherit defaults from Spring Boot -->
<parent>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-parent</artifactId>
  <version>2.0.3.RELEASE</version>
</parent>

<!-- Add typical dependencies for a web application -->
<dependencies>
  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-web</artifactId>
  </dependency>

  <dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-jpa</artifactId>
  </dependency>

  <dependency>
    <groupId>org.modelmapper</groupId>
    <artifactId>modelmapper</artifactId>
    <version>2.1.0</version>
</dependencies>

<properties>
    <spring.version>5.0.6.RELEASE</spring.version>
</properties>
```

* 일반적인 Maven 프로젝트에서 Spring Boot에 해당하는 의존성을 xml에 추가한다.
* parent POM을 따라 올라가다 보면 여러 라이브러리(의존성)들이 명시되어 있다.
* 해당 xml에 특정 버전을 명시하지 않아도, Spring Boot가 자동적으로 의존성을 관리해준다.
  * Spring Boot가 관리하지 않는 의존성은 특정 버전을 명시해주어야 한다.
  * Spring Boot가 관리하는 의존성의 버전을 변경하려면, properties POM에 값을 명시한다.
* Parent POM을 쓰지 않고, Dependency Management를 통해 커스텀한 의존성들을 사용할 수 있다.
  * 그러나 기본적인 Parent POM이 인코딩 등 Spring Boot에 최적화된 설정을 제공하기 때문에 권장하지 않는다.
* Parent가 관리하는 버전을 특정 버전으로 변경하려면 properties를 추가한다.

<br>

---

## Reference

* 스프링 부트 개념과 활용 (백기선, Inflearn)
