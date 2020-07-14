---
title: "Spring Boot 개념과 활용 - 4장 : 독립적으로 실행 가능한 JAR"
date: 2020-07-04 16:59:29
thumbnail: https://user-images.githubusercontent.com/56240505/86525119-eea35780-bebd-11ea-8fbd-ceacfdfae2c6.png
categories:
- Back-End
- Spring & Spring Boot
tags: [Spring Boot]
---

## JAR

* MAVEN 혹은 GRADLE 프로젝트를 JAR 파일로 패키징할 수 있다.
  * mvn package를 하면 실행 가능한 JAR 파일 “하나가" 생성된다.
  * 이는 spring-maven-plugin이 해주는 패키징 작업이다.
  * 압축을 해제해보면 Jar 파일 내부에 어플리케이션 파일과 의존성 Jar들이 내장되어 있다.
* 과거에는 “uber” jar 를 사용했다.
  * 모든 클래스(의존성 및 애플리케이션)를 하나로 압축하는 방법이다.
  * 무슨 라이브러리를 사용하는지, 해당 클래스가 어디 소속인지 알기 어렵다.
  * 또한 내용은 다르지만 이름이 같은 파일의 경우 처리하기 어렵다.
* Spring Boot의 전략
  * 내장 JAR : 기본적으로 Java에는 내장 JAR를 로딩하는 표준적인 방법이 없다.
  * 애플리케이션 클래스와 라이브러리 위치를 구분한다.
  * org.springframework.boot.loader.jar.JarFile을 사용해서 내장 JAR를 읽는다.
  * org.springframework.boot.loader.Launcher를 사용해서 실행한다.

<br>

---

## Reference

* 스프링 부트 개념과 활용 (백기선, Inflearn)
