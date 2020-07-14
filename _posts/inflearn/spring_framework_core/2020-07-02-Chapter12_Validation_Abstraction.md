---
title:  "Spring Framework 핵심 기술 - 12장 : Validation 추상화"
categories:
  - Back-End
  - Spring & Spring Boot
  - Inflearn
tags:
  - Spring & Spring Boot
  - Inflearn
toc: true
toc_sticky: true
last_modified_at: 2020-07-10T08:17:00-05:00
---

## Validation Abstraction

> EventValidator.java

```java
public class EventValidator implements Validator {

  @Override
  public boolean supports(Class<?> clazz) {
    retrun Event.class.equals(clazz);
  }

  @Override
  public void validate(Object target, Errors errors) {
    ValidationUtils.rejectIfEmptyOrWhitespaces(errors, "ttitle", "not empty");
  }
}
```

<br>

> AppRunner.java

```java
public class AppRunner implements ApplicationRunner {

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Event event = new Event(11);
        EventValidator eventValidator = new EventValidator();
        Errors errors = new BeanPropertyBindingResult(event, "event");

        eventValidator.validate(event, errors);
    }
}
```

* org.springframework.validation.Validator.
* 어플리케이션에서 사용하는 객체 검증용 인터페이스이다.
* 어떠한 계층과도 관계가 없어 웹과 서비스 및 데이터 등 모든 계층에서 사용해도 좋다.
* 구현체 중 하나로, JSR-303(Bean Validation 1.0)과 JSR-349(Bean Validation 1.1)을 지원한다.(LocalValidatorFactoryBean)
* DataBinder에 들어가 바인딩 할 때 같이 사용되기도 한다.
* ``boolean supports(Class clazz)`` : 어떤 타입의 객체를 검증할 때 사용할 것인지 결정한다.
* ``void validate(Object obj, Errors e)`` : 실제 검증 로직을 이 안에서 구현하며, 구현할 때 ValidationUtils 사용하며 편리하다.

<br>

## Spring Boot 2.0.5 이상 버전

> Event.java

```java
public class Event {

  @NotEmpty
  String title;

  @Min(0)
  Integer limit;
}
```

<br>

> AppRunner.java

```java
public class AppRunner implements ApplicationRunner {

  @Autowired
  Validator validator;

  public void run(ApplicationArguments args) throws Exception {
    Event event = new Event();
    Errors errors = new BeanPropertyBindingResult(event, "event");
    validator.validate(event, errors);
  }
}
```

* LocalValidatorFactoryBean 빈으로 자동 등록되어 Validator 인스턴스를 Autowired할 수 있다.
* JSR-380(Bean Validation 2.0.1) 구현체로 hibernate-validator를 사용한다.
* @NotEmpty, @Email, @Size 등 다양한 Annotation으로 유효성을 검사할 수 있다.

<br>

---

## Reference

*	스프링 프레임워크 핵심 기술 (백기선, Inflearn)
