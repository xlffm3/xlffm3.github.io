---
title:  "[Spring Framework 핵심 기술] 3장 : @Autowired"
excerpt: "Inflearn에 있는 백기선님의 [스프링 프레임워크 핵심 기술] 강의를 듣고 정리한 필기이다."
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-07-09T08:08:00-05:00
---

## @Autowired

* 필요한 의존 객체의 "타입"에 해당하는 빈을 찾아 주입한다.
* 해당 Annotation이 붙어있으면 의존성 주입을 시도하며, 실패하면 어플리케이션 구동이 실패한다.
* 따라서 의존성 주입이 Optional인 경우, ``@Autowired(required = false)`` 옵션을 줄 수 있으며 기본값은 true이다.
* 생성자와 세터 및 필드에 사용할 수 있다.

<br>

## 경우의 수

* 해당 타입의 Bean이 없는 경우.
* 해당 타입의 Bean이 한 개인 경우.
* 해당 타입의 Bean이 여러 개인 경우.
  * Bean 이름으로 시도한다.
  * 같은 이름의 Bean을 찾으면 해당 Bean을 사용한다.
  * 같은 이름을 못 찾으면 실패한다.

<br>

## 같은 타입의 Bean이 여러 개인 경우

* BookRepository라는 인터페이스를 필드로 가지고 있을 때, 해당 인터페이스를 구현하는 여러 가지 Bean들 중 원하는 Bean을 찾기 힘들다.
* 사용하고자 하는 Bean의 @Component 관련 Annotation 옆에 @Primary를 추가한다.
* 혹은 @Autowired Annotation 옆에 @Qualifier("Bean Name")를 추가한다.
* 혹은 List를 통해 해당 타입의 Bean을 모두 주입받는다.

<br>

## Lifecycle

-	Bean 인스턴스를 생성한 다음 Initialization이 되는데, 그 이전 혹은 이후 Callback 관련 작업이 수행될 수 있는 라이프 사이클을 가진다.
-	BeanPostProcessor : 새로 만든 Bean 인스턴스를 수정할 수 있는 라이프 사이클 인터페이스이다.
-	AutowiredAnnotationBeanPostProcessor extends BeanPostProcessor : Spring이 제공하는 @Autowired와 @Value 및 JSR-330의 @Inject Annotation을 지원하는 처리기이다.
-	Bean Initialization 이전에, AutowiredAnnotationBeanPostProcessor 클래스가 동작하여 @Autowired의 작업을 수행하여 주입한다.
-	이후 @PostConstruct와 같은 Callback 관련 Annotation이 동작한다.

<br>

---

## Reference

*	스프링 프레임워크 핵심 기술(백기선, Inflearn)
