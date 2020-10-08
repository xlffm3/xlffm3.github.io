---
title:  "Spring Boot 개념과 활용 - 8장 : Logging"
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-07-11T08:10:00-05:00
---

## 로깅 퍼사드 vs 로거

* Commons Logging : 로거 API를 추상화 한 인터페이스이다.
* SLF4j, JUL, Log4J2, Logback.
* Spring-JCL.
  * Commons Logging을 컴파일 시점에 SLF4J나 Log4J2로 변경한다.
  * pom.xml에 exclusion을 시키지 않아도 된다.
  * Spring Boot는 Commons Logging을 사용한다.
  * 최종적으로 Logback(SLF4j의 구현체)으로 찍히게 된다.

<br>

## Spring Boot Logging

> application.properties

```properties
logging.path=logs
logging.level.me.glenn.test=DEBUG
```

<br>

> AppRunner.java

```java
private Logger logger = LoggerFactory.getLogger(AppRunner.class);
```

* 기본 포맷
  * VM option 혹은 application.properties에 옵션을 추가한다.
* --debug (일부 핵심 라이브러리만 디버깅 모드로 쓴다.)
* --trace (전부 다 디버깅 모드로 쓴다.)
* 컬러 출력 : spring.output.ansi.enabled
* 파일 출력 : logging.file 또는 logging.path.
  * 전자는 고정 path에 특정 파일을 생성하고, 후자는 특정 path에 spring.log 파일을 생성한다.
* 로그 레벨 조정 : logging.level.패지키 = 로그 레벨.

<br>

## 커스텀 로그 설정

* Logback(권장) : logback-spring.xml
* Log4J2 : log4j2-spring.xml
* JUL(권장x) : logging.properties
* Logback extension.
  * 프로파일 : < springProfile name=”프로파일” >
  * Environment 프로퍼티 : < springProperty >
  * [레퍼런스 참조](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-logging.html)

<br>

## 로거를 Log4J2로 변경하기

* 웹 서버 변경과 동일한 방식을 적용하면 된다.
* [레퍼런스 참조](https://docs.spring.io/spring-boot/docs/current/reference/html/howto-logging.html#howto-configure-log4j-for-logging)

<br>

---

## Reference

* 스프링 부트 개념과 활용 (백기선, Inflearn)
