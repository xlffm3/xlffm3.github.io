---
title:  "Spring Framework 핵심 기술 - 11장 : Resource 추상화"
categories:
  - Back-End
  - Spring & Spring Boot
  - Inflearn
tags:
  - Spring & Spring Boot
  - Inflearn
toc: true
toc_sticky: true
last_modified_at: 2020-07-10T08:16:00-05:00
---

## Resource Abstraction

* org.springframework.core.io.Resource.
* java.net.URL을 추상화 한 것이며, Spring 내부에서 많이 사용하는 인터페이스이다.
* 추상화 이유.
	* 클래스패스 기준으로 리소스 읽어오는 기능이 없다.
	* ServletContext를 기준으로 상대 경로로 읽어오는 기능이 없다.
	* 새로운 핸들러를 등록하여 특별한 URL 접미사를 만들어 사용할 수는 있지만 구현이 복잡하고 편의성 메소드가 부족하다.

<br>

## Resource 읽어오기

```java
public class AppRunner implements ApplicationRunner {

    @Autowired
    ApplicationContext resourceLoader;

    @Override
    public void run(ApplicationArguments args) throws Exception {
        System.out.println(resourceLoader.getClass());
				//WebServerApplicationContext
        Resource resource = resourceLoader.getResource("classpath://test.txt");
        System.out.println(resource.getClass());
				//ClassPathResource
        Resource resource2 = resourceLoader.getResource("test.txt");
        System.out.println(resource2.getClass());
				//ServletContextResource
		}
}
```

* Resource의 타입은 location 문자열과 ApplicationContext의 타입에 따라 결정 된다.
  * ClassPathXmlApplicationContext -> ClassPathResource.
  * FileSystemXmlApplicationContext -> FileSystemResource.
  * WebApplicationContext -> ServletContextResource.
* ApplicationContext의 타입에 상관없이 리소스 타입을 강제하려면 java.net.URL 접두어(+ classpath:) 중 하나를 사용할 수 있다.
  * classpath:me/whiteship/config.xml -> ClassPathResource.
  * file:///some/resource/path/config.xml -> FileSystemResource.

<br>

---

## Reference

*	스프링 프레임워크 핵심 기술 (백기선, Inflearn)
