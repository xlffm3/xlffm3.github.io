---
title: "[Spring Boot 개념과 활용] 26장 : Actuator"
excerpt: "Inflearn에 있는 백기선님의 [스프링 부트 개념과 활용] 강의를 듣고 정리한 필기이다."
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-07-13T08:16:00-05:00
---

## Spring Boot Actuator

* 의존성 : spring-boot-starter-actuator.
* 애플리케이션의 각종 정보를 확인할 수 있는 Endpoints.
  * 다양한 Endpoints를 제공한다.
  * JMX 또는 HTTP를 통해 접근 가능하다.
  * shutdown을 제외한 모든 Endpoint는 기본적으로 활성화 상태이다.
* 활성화 옵션 조정.
  * ``management.endpoints.enabled-by-default=false``
  * ``management.endpoint.info.enabled=true``

<br>

## JConsole과 VisualVm

* 어플리케이션 운영 중 현재 어플리케이션에 대한 전반적인 뷰를 보여준다.
  * 클래스 개수.
  * 쓰레드 개수.
  * 메모리 등.
* JConsole은 어플리케이션 운영 중 콘솔에서 jconsole을 입력한다.
* VisualVm은 별도의 프로그램을 설치한다.
* HTTP 사용하기.
  * /actuator
  * health와 info를 제외한 대부분의 Endpoint가 기본적으로 비공개 상태이다.
  * 공개 옵션 조정.
    * ``management.endpoints.web.exposure.include=*``
    * ``management.endpoints.web.exposure.exclude=env,beans``

<br>

## Spring Boot Admin

> AdminServer.xml

```xml
<dependency>
  <groupId>de.codecentric</groupId>
  <artifactId>spring-boot-admin-starter-server</artifactId>
  <version>2.0.1</version>
</dependency>
```

<br>

> ClientServer.xml

```java
<dependency>
  <groupId>de.codecentric</groupId>
  <artifactId>spring-boot-admin-starter-client</artifactId>
  <version>2.0.1</version>
</dependency>
```

<br>

> application.properties

```java
//클라이언트 서버의 포트는 다른 것으로 변경
spring.boot.admin.client.url=http://localhost:8080
management.endpoints.web.exposure.include=*
```

* 별도의 Spring Boot Actuator UI를 제공한다.
* 어드민 서버의 @SpringBootApplication 위치에 @EnableAdminServer를 추가한다.

<br>

---

## Reference

* 스프링 부트 개념과 활용(백기선, Inflearn)
