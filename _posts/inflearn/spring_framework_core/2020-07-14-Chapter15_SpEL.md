---
title:  "Spring Framework 핵심 기술 - 15장 : SpEL"
categories:
  - Back-End
  - Spring & Spring Boot
  - Inflearn
tags:
  - Spring & Spring Boot
  - Inflearn
toc: true
toc_sticky: true
last_modified_at: 2020-07-14T08:20:00-05:00
---

## SpEL(Spring Expression Language)

```Java
ExpressionParser parser = new SpelExpressionParser();
StandardEvaluationContext context = new StandardEvaluationContext(bean);
Expression expression = parser.parseExpression(“SpEL 표현식”);
String value = expression.getvalue(context, String.class);
```

* 객체 그래프를 조회하고 조작하는 기능을 제공한다.
* Unified EL과 비슷하지만, 메소드 호출을 지원하며 문자열 템플릿 기능도 제공한다.
* OGNL, MVEL, JBOss EL 등 Java에서 사용할 수 있는 여러 EL이 있지만, SpEL은 모든 Spring 프로젝트 전반에 걸쳐 사용하는 EL이다.
* Spring 3.0 부터 지원한다.

<br>

## 문법

> Test.Java

```Java
@Component
public class Test {
	@Value{"#{1 + 1}"}
	int data;

	@Value{"#{1 eq 1}"}
	boolean trueOrFalse;

	@Value{"${my.value}"}
	int value;
}
```

* #{“표현식"}.
* ${“프로퍼티"}.
* #{${my.data} + 1} 과 같이 표현식은 프로퍼티를 가질 수 있지만, 반대는 안 된다.
* 표현식에서 특정 Bean의 Data를 가져올 수 있다.
	* JSP 객체 참조처럼 객체.data 형태로 가져온다.

<br>

## Use Case

* @Value.
* @ConditionalOnExpression.
* Spring Security.
	* Method Security, @PreAuthorize, @PostAuthorize, @PreFilter, @PostFilter.
	* XML 인터셉터 URL 설정.
	* ...
* @Query.
* Thymeleaf.
* ...

<br>

---

## Reference

*	스프링 프레임워크 핵심 기술 (백기선, Inflearn)
