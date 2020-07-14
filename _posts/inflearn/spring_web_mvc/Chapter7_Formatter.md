---
title: "Spring Web MVC - 7장 : Formatter & Domain Class Converter"
date: 2020-07-011 16:48:29
thumbnail: https://user-images.githubusercontent.com/56240505/87219627-c1f8af80-c397-11ea-96bb-83c3f59b7229.png
categories:
- Back-End
- Spring & Spring Boot
tags: [Spring]
---

## JUNIT Test

> SimpleController.java

```Java
@RestController
public class SampleController {

    @GetMapping("/hello/{name}")
    public String hello(@PathVariable("name") Person person) {
        return "hello " + person.getName();
    }
}
```

<br>

> SimpleControllerTest.java

```Java
@RunWith(SpringRunner.class)
@WebMvcTest
class SampleControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Test
    public void hello() throws Exception{
        this.mockMvc.perform(get("/hello/jipark"))
                .andDo(print())
                .andExpect(content().string("hello jipark"));
    }
}
```

*	Ctrl + Shift + T 를 통해 JUNIT 테스트 Shortcut를 사용하여 테스트 환경을 구축한다.
*	문자열을 객체로 받으려고 하는데 Spring이 방법을 모르기 때문에 Formatter를 구현해야 한다.

<br>

## Formatter

> PersonFormatter.java

```Java
public class PersonFormatter implements Formatter<Person> {

    @Override
    public Person parse(String s, Locale locale) throws ParseException {
        Person person = new Person();
        person.setName(s);
        return person;
    }
}
```

<br>

> WebConfig.java

```java
@Configuration
public class WebConfig implements WebMvcConfigurer {

    @Override
    public void addFormatters(FormatterRegistry registry) {
        registry.addFormatter(new PersonFormatter());
    }
}
```

* Formatter는 2개의 인터페이스를 상속받는다.
  * Printer : 해당 객체를 (Locale 정보를 참고하여) 문자열로 어떻게 출력할 것인가를 다룬다.
  * Parser : 어떤 문자열을 (Locale 정보를 참고하여) 객체로 어떻게 변환할 것인가를 다룬다.
* Spring Boot를 사용하는 경우 WebMvcConfigurer의 메서드를 오버라이드 할 필요 없이, Formatter를 Bean으로 등록하기만 하면 된다.

<br>

## @RequestParam

> SimpleController.java

```java
@RestController
public class SampleController {

    @GetMapping("/hello")
    public String hello(@RequestParam("name") Person person) {
        return "hello " + person.getName();
    }
}
```

<br>

> SimpleControllerTest.java

```java
@Test
public void hello() throws Exception{
    this.mockMvc.perform(get("/hello").param("name", "jipark") )
            .andDo(print())
            .andExpect(content().string("hello jipark"));
}
```

* localhost:8080/hello?name=jipark 으로 접근한다.

<br>

## WebMvcTest

> SimpleControllerTest.java

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
```

* @WebMvcTest는 슬라이싱 테스트로서 Web과 관련된 Bean만 등록하기 때문에, Formatter를 @Controller가 아닌 @Component로 설정한다면 단위 테스트에서 실패한다.
* 따라서 명시적으로 해당 Formatter를 테스트에서 등록해주거나, 통합 테스트로 변경해준다.
  * @MockBean.
* 이 때, MockMvc가 자동 주입되지 않기 때문에 @AutoConfigureMockMvc를 붙어주어야 한다.

<br>

## Domain Class Converter

> pom.xml

```xml
<dependency>
  <groupId>org.springframework.boot</groupId>
  <artifactId>spring-boot-starter-data-jpa</artifactId>
</dependency>
<dependency>
  <groupId>com.h2database</groupId>
  <artifactId>h2</artifactId>
</dependency>
```

<br>

> Person.java

```java
@Entity
public class Person {
    private String name;

    @Id @GeneratedValue
    private long id;
}
```

<br>

> PersonRepository.java

```java
public interface PersonRepository extends JpaRepository<Person, Long> {
}
```

<br>

> SimpleController.java

```Java
@GetMapping("/hello")
public String hello(@RequestParam("id") Person person) {
    return "hello " + person.getName();
}
```

<br>

> SimpleControllerTest.java

```java
@RunWith(SpringRunner.class)
@SpringBootTest
@AutoConfigureMockMvc
class SampleControllerTest {

    @Autowired
    MockMvc mockMvc;

    @Autowired
    PersonRepository personRepository;

    @Test
    public void hello() throws Exception{
        Person person = new Person();
        person.setName("jipark");
        person.setId(10);
        Person savedPerson = personRepository.save(person);
        this.mockMvc.perform(get("/hello").param("id", savedPerson.getId() + ""))
                .andDo(print())
                .andExpect(content().string("hello jipark"));
    }
}
```

*	Spring Data JPA는 Spring MVC용 도메인 클래스 컨버터를 제공한다.
*	도메인 클래스 컨버터는 Spring Data JPA가 제공하는 Repository를 사용해서 ID에 해당하는 엔티티를 읽어온다.
*	@RequestParam을 ID로 객체 맵핑하는 경우, 직접 Formatter를 구현하는 수고스러움을 덜 수 있다.
*	DB에서 자동 생성할 값이라면 @GeneratedValue 를 붙여준다.
*	ID 값에서 Entity로 컨버팅할 때 JpaRepository가 필요하다.
*	단위 테스트에서는 테스트에 만들 객체를 만들어 저장한 다음, ID String을 파라미터로 전달하면 조회가 가능하다.

<br>

---

## Reference

*	스프링 웹 MVC (백기선, Inflearn)
