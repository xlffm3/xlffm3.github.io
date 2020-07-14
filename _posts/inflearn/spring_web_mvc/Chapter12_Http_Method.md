---
title: "Spring Web MVC - 12장 : Http Method"
date: 2020-07-011 17:20:29
thumbnail: https://user-images.githubusercontent.com/56240505/87219627-c1f8af80-c397-11ea-96bb-83c3f59b7229.png
categories:
- Back-End
- Spring & Spring Boot
tags: [Spring]
---

## GET 요청

*	클라이언트가 서버의 리소스를 요청할 때 사용한다.
*	캐싱 할 수 있으며, 조건적인 GET으로 바뀔 수 있다.
*	브라우저 기록에 남는다.
*	북마크 할 수 있다.
*	URL에 공개되기 때문에 민감한 데이터를 보낼 때 사용하지 않는다.
*	idempotent.

<br>

## POST 요청

*	클라이언트가 서버의 리소스를 수정하거나 새로 만들 때 사용한다.
*	서버에 보내는 데이터를 POST 요청 본문에 담는다.
*	캐시할 수 없다.
*	브라우저 기록에 남지 않는다.
*	북마크 할 수 없다.
*	데이터 길이 제한이 없다.

<br>

## PUT 요청

*	URI에 해당하는 데이터를 새로 만들거나 수정할 때 사용한다.
*	POST와 다른 점은 “URI”에 대한 의미가 다르다.
	*	POST의 URI는 보내는 데이터를 처리할 리소스를 지칭한다.
	*	PUT의 URI는 보내는 데이터에 해당하는 리소스를 지칭한다.
* Idempotent.

<br>

## PATCH 요청

*	PUT과 비슷하지만, 기존 엔티티와 새 데이터의 차이점만 보낸다는 차이가 있다.
*	Idempotent.

<br>

## DELETE 요청

*	URI에 해당하는 리소스를 삭제할 때 사용한다.
*	Idempotent.

<br>

## Spring MVC에서 HTTP method 맵핑하기

> SampleController.java

```java
@Controller
public class SampleController {

    @RequestMapping(value = "/hello", method = {RequestMethod.GET, RequestMethod.PUT})
    @ResponseBody
    public String hello() {
        return "hello";
    }
}
```

-	특정 요청을 명시하지 않으면 모든 요청을 허용한다.
-	@RequestMapping을 Class 레벨에서 공통으로 Method 설정할 수 있다.
-	@GetMapping, @PostMapping 등 다양한 Annotation을 지원한다.

<br>

---

## Reference

*	스프링 웹 MVC (백기선, Inflearn)
