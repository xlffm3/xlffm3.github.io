---
title:  "Spring Framework 핵심 기술 - 1장 : IoC & Bean"
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-07-09T08:06:00-05:00
---

## IoC(Inversion of Control)

* 의존 관계 주입(Dependency Injection)을 뜻한다.
* 어떤 객체가 사용하는 의존 객체를 직접 만들어서 사용하는 것이 아니라, 주입 받아 사용하는 방식을 의미한다.

<br>

## Spring IoC Container

* IoC 기능 및 Bean을 담고 있는 Container 역할을 한다.
* Bean 설정 소스로부터 Bean 정의를 읽어들인 뒤, Bean을 구성하여 제공한다.
* 어플리케이션 컴포넌트의 중앙 저장소이며, 최상위 인터페이스는 'BeanFactory'이다.
* ApplicationContext : BeanFactory를 상속받은 인터페이스이다.
  * 메시지 소스 처리 기능(i18n).
  * 이벤트 발행 기능.
  * 리소스 로딩 기능.
  * ...

<br>

## Bean

* Spring IoC Container가 관리하는 객체이다.
* 장점.
  * 의존성 관리.
  * Life Cycle Interface : Bean 생성 이후 Callback 작업 등이 편리하다.
  * Scope.
    * Singleton : 하나의 객체만 사용하는 것을 의미한다.
    * Prototype : 매번 다른 객체를 사용하는 것을 의미한다.
    * Singleton의 경우, 런타임 성능 및 메모리 측면에서 효율적이다.
    * 별도의 Annotation이 없다면 Spring에서 Bean을 등록할 때, Singleton으로 등록한다.
    * bookService 라는 객체가 어플리케이션 전반에서 여러 개일 필요가 있을까?

<br>

## DI 및 Unit Test

> BookService.java

```java
@Service
public class BookService {

    private BookRepository bookRepository;

    public BookService(BookRepository bookRepository) {
        this.bookRepository = bookRepository;
    }

    public Book save(Book book) {
        book.setCreated(new Date());
        book.setBookStatus(BookStatus.DRAFT);
        return bookRepository.save(book);
    }

    @PostConstruct
    public void postConstruct() {
        System.out.println("POST CONSTRUCT");
    }
}
```

<br>

> BookServiceTest.java

```java
@RunWith(SpringRunner.class)
public class BookServiceTest {

    @Mock
    BookRepository bookRepository;

    @Test
    public void save() {
        Book book = new Book();

        when(bookRepository.save(book)).thenReturn(book);
        BookService bookService = new BookService(bookRepository);

        Book bookResult = bookService.save(book);

        assertThat(book.getDate()).isNotNull();
        assertThat(book.getBookStatus()).isEqualTo(BookStatus.DRAFT);
        assertThat(bookResult).isNotNull();
    }
}
```

* 위 코드의 단위 테스트를 만들기 위해서는 bookRepository를 구현해야 한다.
* 현재는 생성자로 의존성을 주입받지만, 객체 내부에서 bookRepository를 생성하는 경우 테스트가 어려워진다.
* DI를 이용하면 단위 테스트에서 Mock 객체를 생성하여 테스트를 진행할 수 있다.

<br>

---

## Reference

*	스프링 프레임워크 핵심 기술 (백기선, Inflearn)
