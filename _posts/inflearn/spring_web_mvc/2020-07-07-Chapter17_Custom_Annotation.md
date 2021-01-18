---
title: "[Spring Web MVC] 17장 : Custom Annotation"
excerpt: "Inflearn에 있는 백기선님의 [스프링 웹 MVC] 강의를 듣고 정리한 필기이다."
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-07-15T08:08:00-05:00
---

## 메타(Meta) 애노테이션

* 애노테이션에 사용할 수 있는 애노테이션이다.
* 스프링이 제공하는 대부분의 애노테이션은 메타 애노테이션으로 사용할 수 있다.

<br>

## 조합(Composed) 애노테이션

* 한 개 혹은 여러 메타 애노테이션을 조합해서 만든 애노테이션이다.
* 코드를 간결하게 줄일 수 있다.
* 보다 구체적인 의미를 부여할 수 있다.

<br>

## @Retention

> HelloGetMapping.java

```java
@Target(ElementType.METHOD)
@Retention(RetentionPolicy.RUNTIME)
@RequestMapping(method = RequestMethod.GET, value = "/hello")
public @interface GetHelloMapping {
}
```

* 해당 애노테이션 정보를 언제까지 유지할 것인가에 대한 애노테이션이다.
* Source : 소스 코드까지만 유지를 의미하며,컴파일 하면 해당 애노테이션 정보는 사라진다.
* Class : 컴파인 한 .class 파일에도 유지를 의미하며, 런타임 시 클래스를 메모리로 읽어오면 해당 정보는 사라진다.
* Runtime : 클래스를 메모리에 읽어왔을 때 까지 유지하며, 코드에서 이 정보를 바탕으로 특정 로직을 실행할 수 있다.

<br>

## @Target

* 해당 애노테이션을 어디에 사용할 수 있는지 결정한다.

<br>

## @Documented

* 해당 애노테이션을 사용한 코드의 문서에 그 애노테이션에 대한 정보를 표기할지 결정한다.

<br>

---

## Reference

*	스프링 웹 MVC(백기선, Inflearn)
