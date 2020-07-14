---
title:  "Spring Boot 개념과 활용 - 7장 : Profile"
categories:
  - Back-End
  - Spring & Spring Boot
  - Inflearn
tags:
  - Spring & Spring Boot
  - Inflearn
toc: true
toc_sticky: true
last_modified_at: 2020-07-11T08:09:00-05:00
---

## Profile

* @Profile의 위치
  * @Configuration.
  * @Component.
* 어떤 프로파일을 활성화 할 것인가?
  * spring.profiles.active=XXX.
  * application.properties에 추가할 수 있으며, properties 우선순위에 영향을 받는다.
  * 즉, 커맨드 라인이나 VM 옵션 등으로 사용할 수 있다.
* 프로파일용 프로퍼티.
  * application-{profile}.properties=.
  * 일반 application.properties보다 우선순위가 높다.
  * active 시키는 프로파일 이름에 해당하는 properties를 참조한다.
* 어떤 프로파일을 추가할 것인가?
  * spring.profiles.include=XXX.
  * 현재 참조 중인 properties에서 다른 profile을 추가할 경우, 해당 프로파일에 해당하는 프로퍼티를 참조할 수 있다.

<br>

---

## Reference

* 스프링 부트 개념과 활용 (백기선, Inflearn)
