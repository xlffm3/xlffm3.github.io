---
title:  "Spring Boot 개념과 활용 - 13장 : Web Jar"
categories:
  - Back-End
  - Spring & Spring Boot
  - Inflearn
tags:
  - Spring & Spring Boot
  - Inflearn
toc: true
toc_sticky: true
last_modified_at: 2020-07-12T08:04:00-05:00
---

## Web Jar

> hello.html

```html
<script src="/webjars/jquery/dist/3.3.1/jquery.min.js"></script>
<script>
  $(function() {
  console.log("ready!");
  });
</script>
```

* JQuery 등 클라이언트 라이브러리도 Jar를 통해 추가할 수 있다.
* 웹JAR 맵핑 “/webjars/\*\*”
* JQuery의 경우, pom.xml에 의존성을 추가하고 html에 Web Jar를 맵핑한다.
* 버전을 생략하려면 webjars-locator-core 의존성을 추가해준다.

<br>

---

## Reference

* 스프링 부트 개념과 활용 (백기선, Inflearn)