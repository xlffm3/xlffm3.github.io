---
title:  "Spring Framework 핵심 기술 - 2장 : ApplicationContext 외 다양한 Bean 설정 방법"
categories:
  - Back-End
  - Spring & Spring Boot
  - Inflearn
tags:
  - Spring & Spring Boot
  - Inflearn
toc: true
toc_sticky: true
last_modified_at: 2020-07-09T08:07:00-05:00
---

## Spring IoC Container의 역할

* Bean Instance를 생성한다.
* 의존 관계를 설정한다.
* Bean을 제공한다.

<br>

## ApplicationContext

* XML : ClassPathXmlApplicationContext.
* Java : AnnotationConfigApplicationContext.

<br>

## Bean 명세서

* 다음과 같은 Bean에 대한 정의를 담고 있다.
  * 이름.
  * 클래스.
  * 스코프.
  * 생성자 아규먼트(Constructor).
  * 프로퍼티(Setter).

<br>

## Bean 등록

> BookService.java

```java
public class BookService {

    BookRepository bookRepository;

    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
}
```

<br>

> Application.xml

```java
<bean id="bookService" class="com.example.demo.BookService">
    <property name="bookRepository" ref="bookRepository"/>
</bean>
<bean id="bookRepository" class="com.example.demo.BookRepository"/>
```

<br>

> DemoApplication.java

```java
@SpringBootApplication
public class DemoApplication {

    public static void main(String[] args) {
        ApplicationContext context = new ClassPathXMLApplicationContext("Application.xml");
        String[] beanDefinitionNames = context.getBeanDefinitionNames();
        System.out.println(Arrays.toString(beanDefinitionNames)); // [bookService, bookRepository]
        BookService bookService = (BookService)context.getBean("bookService");
        System.out.println(bookService.bookRepository != null); //true
    }
}
```

* property Name에 해당하는 bookRepository는 setter 메소드의 인자를 의미한다.
* property ref에 해당하는 bookRepository는 Bean ID로서, 해당 Bean을 참조한다는 의미이다.
* 이런 설정을 통해, Bean으로 등록된 bookRepository를 가져와 setter 메소드에 주입한다.
* 설정이 번거롭기 때문에 잘 사용하지 않는 방법이다.

<br>

## ApplicationContext

> BookService.java

```java
@Service
public class BookService {

    @Autowired
    BookRepository bookRepository;

    public void setBookRepository(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }
}
```

<br>

> BookRepository.java

```java
@Repository
public class BookRepository {}
```

<br>

> Application.xml

```java
<context:component-scan basepackage="com.example.demo"/>
```


* XML에 등록된 component-scan 기능을 토대로 특정 패키지 이하의 모든 클래스 중에 @Component Annotation을 이용한 Class를 스캐닝하여 Bean으로 등록한다.
* @Service 및 @Repository는 @Component를 확장한 Annotation이다.
* @Autowired를 통해 의존성을 주입한다.
* 그러나 Bean 설정을 XML이 아닌 Java로 관리하기 위한 목적으로 Java 설정 파일이 등장하게 되었다.

<br>

## @Configuration

> ApplicationConfig.Java

```java
@Configuration
public class ApplicationConfig {

    @Bean
    public BookRepository bookRepository() {
        return new BookRepository();
    }

    @Bean
    public BookService bookService() { // 혹은 bookService(BookRepository bookRepository)
        BookService bookService = new BookService();
        bookService.setBookRepository(bookRepository());
        return new BookService();
    }
}
```

<br>

> DemoApplication.java

```java
ApplicationContext context = new AnnotationConfigApplicationContext(ApplicationConfig.class);
```

* @Configuration Annotation을 통해 Bean을 더욱 유연하게 설정할 수 있다.
* ``bookService()`` 메소드 내부에서 setter를 통해 의존성을 직접 주입할 수 있으며, 혹은 메소드 파라미터를 사용해서 의존성을 주입할 수 있다.
* 위 예제의 경우 생성자가 없이 setter만 사용하는 간단한 예제이기 때문에, ``bookService()`` 메소드 내부에서 직접 의존성을 주입할 필요 없이, 단순히 Bean 등록을 하고 @Autowired를 사용해도 된다.

<br>

## @ComponentScan

> ApplicationConfig.java

```java
@Configuration
@ComponentScan(basePackageClasses = DemoApplication.class)
//or
@ComponentScan(basePackages = "com.example.demo")
```

*	특정 패키지나 클래스가 있는 위치에서 Bean을 스캔하여 등록하는 방법이다.
*	SpringBoot와 가장 유사한 방법이며, SpringBoot는 @SpringBootApplication을 이용한다.
*	@SpringBootApplication은 @ComponentScan과 @Configuration을 모두 갖고 있기 때문에, ApplicationConfig 파일이 필요없다.

<br>

---

## Reference

*	스프링 프레임워크 핵심 기술 (백기선, Inflearn)
