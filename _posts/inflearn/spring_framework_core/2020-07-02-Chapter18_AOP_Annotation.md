---
title:  "Spring Framework 핵심 기술 - 18장 : @AOP"
categories:
  - Back-End
  - Spring & Spring Boot
  - Inflearn
tags:
  - Spring & Spring Boot
  - Inflearn
toc: true
toc_sticky: true
last_modified_at: 2020-07-10T08:23:00-05:00
---

## Dependency 추가

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-aop</artifactId>
</dependency>
```

<br>

## Aspect와 Advice 및 Pointcut 정의

> PerfAspect.java

```java
@Aspect
@Component
public class PerfAspect {

    @Around("execution(* com.example..*.EventService.*(..))")
    @Around("@annotation(PerLogging)")
    @Around("bean(SimpleEventService)")
    public Object logPeer(ProceedingJoinPoint pip) throws Throwable {
        long begin = System.currentTimeMillis();
        System.out.println();
        Object retval = pip.proceed(); //실제 원하는 Target Method의 작업 수행
        System.out.println(System.currentTimeMillis() - begin);
        return retval;
    }

    @Before("bean(SimpleEventService)")
    public void hello() {
      System.out.println("hello");
    }
}
```

<br>

> PerLogging.java

```java
@Documented
@Retention(RetentionPolicy.CLASS)
@Target(ElementType.METHOD)
public @interface PerLogging {
}
```

* @Aspect를 정의하며, Bean 등록을 위해 컴포넌트 스캔을 사용한다면 @Component도 추가한다.
* Advice 정의에 사용되는 어노테이션.
  * @Before.
  * @AfterReturning.
  * @AfterThrowing.
  * @Around.
* Pointcut 정의에 사용되는 어노테이션.
  * @Pointcut(표현식).
  * 주요 표현식.
    * execution.
    * @annotation.
    * bean.
  * Pointcut 조합.
    * &&.
    * ||.
    * !.
* 특정 Annotation 지시를 통해 일부 메서드의 AOP 지정을 누락시킬 수 있다.

<br>

---

## Reference

*	스프링 프레임워크 핵심 기술 (백기선, Inflearn)
