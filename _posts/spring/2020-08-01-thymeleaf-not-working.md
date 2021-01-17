---
title: "Spring Boot에서 Thymeleaf를 사용할 때의 404 ERROR"
excerpt: "Spring이 Thymeleaf을 인식하지 못하는 에러를 해결해보자."
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
  - Thymeleaf
toc: true
toc_sticky: true
last_modified_at: 2020-08-01T18:22:00-05:00
---

## 1. 문제

정말 간단한 @GetMapping조차도 컨트롤러가 View Page를 resolve하지 못하고 계속 404 에러를 띄웠다. 컨트롤러에 명시된 Path가 문제라기 보다는, Spring이 Path를 잘 인식하지 못하는 것으로 보였다.

<br>

## 2. 해결

> application.properties

```properties
spring.thymeleaf.prefix=classpath:templates/
spring.thymeleaf.suffix=.html
```

HTML 파일들이 src/main/resources/templates/ 디렉토리에서 관리되고 있었는데, Prefix와 Suffix를 설정함으로써 간단하게 해결했다.

문제 해결을 위해 구글링을 하다가 생각이 난 정보. CSS, JS Path 잡지 못한다면 다음과 같이 Path를 설정해보자.

> Config.java

```java
@Configuration
public class Config implements WebMvcConfigurer {

    @Override
    public void addResourceHandlers(ResourceHandlerRegistry registry) {
        registry.addResourceHandler("/{path}/...")
                .addResourceLocations("classpath:{path}/");
    }
}
```

<br>

---

## Reference

* [[Spring Boot] CSS,JS 404 ERROR](https://universecoding.tistory.com/80)
