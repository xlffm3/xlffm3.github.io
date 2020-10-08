---
title:  "Spring Framework 핵심 기술 - 5장 : Bean Scope"
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-07-09T08:10:00-05:00
---

## Scope

> Single.java

```java
@Component
public class Single {

    @Autowired
    private Proto proto;

    public Proto getProto() {
        return proto;
    }
}
```

<br>

> Proto.java

```java
@Component @Scope("prototype")
```

<br>

> AppRunner.java

```java
@Component
public class AppRunner implements ApplicationRunner {
    @Autowired
    ApplicationContext ctx;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(ctx.getBean(Single.class).getProto());
        System.out.println(ctx.getBean(Single.class).getProto());
        System.out.println(ctx.getBean(Single.class).getProto());
    }
}
```

* Singleton : 해당 빈의 인스턴스가 어플리케이션에서 단 1개인 것을 의미한다.
  * Spring은 기본적으로 모든 Bean을 Singleton으로 생성하여 관리한다.
* Prototype : 해당 빈의 인스턴스가 어플리케이션에서 여러 개인 것을 의미한다.
* Singleton 인스턴스는 ApplicationContext 초기 구동시 생성되며, Property가 공유되기 때문에 Thread-Safe 하지 않다.
* Bean Scope가 Prototype인 경우 참조될 때 마다 다른 인스턴스가 생성되어 다른 주소값이 출력된다.
* Prototype이 Singleton을 참조하는 경우 문제가 없지만, Singleton이 Prototype을 참조하는 경우 프로퍼티인 Prototype Bean이 변경되지 않는 문제가 발생한다.
* 위 예제는 Singleton Bean이 생성된 시점에 지닌 Proto의 동일한 주소 3개를 출력한다.
* 이러한 문제는 `@Scope(value = "prototype", proxyMode = ScopedProxyMode.TARGET_CLASS)` 와 같이 Proxy를 통해 해결할 수 있다.
  * scoped-porxy.
  * Object-Provider.
  * Provider(표준).

<br>

---

## Reference

*	스프링 프레임워크 핵심 기술 (백기선, Inflearn)
