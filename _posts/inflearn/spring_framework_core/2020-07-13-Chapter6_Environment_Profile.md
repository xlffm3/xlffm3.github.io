---
title:  "Spring Framework 핵심 기술 - 6장 : Profile"
categories:
  - Back-End
  - Spring & Spring Boot
  - Inflearn
tags:
  - Spring & Spring Boot
  - Inflearn
toc: true
toc_sticky: true
last_modified_at: 2020-07-13T08:11:00-05:00
---

## Environment

> AppRunner.java

```java
@Component
public class AppRunner implements ApplicationRunner {
    @Autowired
    ApplicationContext ctx;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        Environment evn = ctx.getEnvironment();
        System.out.println(Arrays.toString(environment.getActiveProfiles())); //[]
        System.out.println(Arrays.toString(environment.getDefaultProfiles())); //[default]
    }
}
```

* Profile과 Property를 다루는 인터페이스이다.
* EnvironmentCapable을 상속받는 ApplicationContext의 ``getEnvironment()`` 메소드로 사용한다.
* 별도의 Profile을 지정해주지 않는다면, Bean들은 기본적으로 Default Profile에 속한다.

<br>

## Profile

> TestConfiguration.java

```java
@Configuration @Profile("test")

public class TestConfiguration {

  @Bean //여기에 @Profile("test")도 가능함
  public BookRepository bookRepository() {
    return new BookRepository();
  }
}
```

<br>

> TestBookRepository.java

```java
@Repository @Profile("test")
```

* Bean들의 그룹을 의미한다.
* Environment의 역할은 활성화 할 Profile의 확인 및 설정이다.
* 특정 환경에서는 사용하고 싶은 Bean이 다를 경우 이용한다.
  * 어플리케이션이 test라는 프로파일로 실행되지 않는 이상 해당 configuration에 정의된 Bean을 사용할 수 없다.
* 테스트 환경 구성 등이 대표적인 유스케이스이다.
* 클래스 및 메소드에 각각 정의할 수 있다.
  * 클래스에 정의하는 경우 Configuration 클래스에 정의하거나, 해당 Bean에 직접 정의한다.
  * 메소드에 정의하는 경우 ``@Bean @Profile("test")`` 를 사용한다.
* 설정 방법.
  * ``-Dspring.profiles.avtive=”test,A,B,..."`` 혹은 @ActiveProfiles Annotation을 통해 Profile을 설정한다.
  * Spring Boot의 경우 application.propertis에 spring.profiles.active=TEST 등의 설정이 가능하다.

<br>

## Profile 표현식

*	! (not).
*	& (and).
*	| (or).

<br>

---

## Reference

*	스프링 프레임워크 핵심 기술 (백기선, Inflearn)
