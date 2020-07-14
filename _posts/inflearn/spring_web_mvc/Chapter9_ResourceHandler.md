---
title: "Spring Web MVC - 9장 : ResourceHandler"
date: 2020-07-011 16:59:29
thumbnail: https://user-images.githubusercontent.com/56240505/87219627-c1f8af80-c397-11ea-96bb-83c3f59b7229.png
categories:
- Back-End
- Spring & Spring Boot
tags: [Spring]
---

## Resource Handler

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("mobile/**")
                .addResourceLocations("classpath:/mobile")
                .setCacheControl(CacheControl.maxAge(10, TimeUnit.MINUTES));
    }
}
```

* 이미지, 자바스크립트, CSS 그리고 HTML 파일과 같은 정적인 리소스를 처리하는 핸들러이다.
* 디폴트(Default) 서블릿은 WAS 등 서블릿 컨테이너가 기본으로 제공하는 서블릿으로 정적인 리소스를 처리할 때 사용한다.
* Spring은 이러한 디폴트 서블릿에 요청을 위임해서 정적인 리소스를 처리한다.
* Spring Boot는 기본 정적 리소스 핸들러 및 캐싱을 제공한다.

<br>

## Spring MVC 리소스 핸들러 맵핑 등록

* DefaultServletHandlerConfigurer.
* 가장 낮은 우선 순위로 등록한다.
* 다른 핸들러 맵핑이 “/” 이하 요청을 처리하도록 허용하고, 최종적으로 리소스 핸들러가 처리하도록 한다.

<br>

## 리소스 핸들러 설정

* 어떤 요청 패턴을 지원할 것인가?
* 어디서 리소스를 찾을 것인가?
* 캐싱.
* ResourceResolver : 요청에 해당하는 리소스를 찾는 전략이다.
	* 캐싱, 인코딩(gzip, brotli), WebJar 등.
* ResourceTransformer : 응답으로 보낼 리소스를 수정하는 전략이다.
	* 캐싱, CSS 링크, HTML5 AppCache 등.

<br>

---

## Reference

*	스프링 웹 MVC (백기선, Inflearn)
