---
title: "[Spring Web MVC] 30장 : @ControllerAdvice"
excerpt: "Inflearn에 있는 백기선님의 [스프링 웹 MVC] 강의를 듣고 정리한 필기이다."
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-07-08T10:40:00-05:00
---

## @(Rest)ControllerAdvice

> BaseController.java

```java
@ControllerAdvice
//특정 범위 필터
@ControllerAdvice(assignableTypes = {SampleController.class, EventApi.class})
public class BaseController {

    @InitBinder
    //중략
    @ExceptionHandler
    //중략
    @ModelAttribute
    //중략
}
```

* 예외 처리, 바인딩 설정, 모델 객체를 모든 컨트롤러 전반에 걸쳐 적용하고 싶은 경우에 사용한다.
	* @ExceptionHandler.
	* @InitBinder.
	* @ModelAttributes.
* 적용할 범위를 지정할 수도 있다.
	* 특정 애노테이션을 가지고 있는 컨트롤러에만 적용하기.
	* 특정 패키지 이하의 컨트롤러에만 적용하기.
	* 특정 클래스 타입에만 적용하기.

<br>

## TO-DO

* @JSONView.
* 비동기 요청 처리.
* CORS 설정.
* HTTP/2.
* 웹 소켓.
* 웹 플럭스.

<br>

---

## Reference

*	스프링 웹 MVC(백기선, Inflearn)
