---
title: "Spring Boot의 @DataJpaTest"
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-08-11T18:22:00-05:00
---

## 1. @DataJpaTest

JPA 관련 설정만 로드하며, DataSource 및 JPA를 통한 데이터의 테스트가 가능하다. 실제 DB를 사용하지 않고 내장형 DB로 테스트할 수 있는 장점이 있다.

<br>

## 2. 사용 예제

```java
@RunWith(SpringRunner.class)
@DataJpaTest
public class ArticleRepositoryTest {

    @Autowired
    private TestEntityManager testEntityManager;

    @Autowired
    private ArticleRepository articleRepository;

    @AfterEach
    public void deleteAll() {
        articleRepository.deleteAll();
    }

    @DisplayName("Article 생성 시 JPA Auditing을 통해 작성 및 수정 시간이 자동 저장")
    @Test
    public void auditCreatedDate() {
        LocalDateTime localDateTime = LocalDateTime.now();

        testEntityManager.persist(Article.builder()
                .title("title")
                .authorName("Tester")
                .content("Test")
                .build());

        Article article = articleRepository.findById(1L)
                .orElse(null);

        assertThat(article.getCreatedDate()).isAfter(localDateTime);
        assertThat(article.getModifiedDate()).isAfter(localDateTime);
    }
}
```

JPA의 EntityManager의 Counterpart인 TestEntityManager를 통해 persist, flush, find 등을 사용할 수 있다. Given-When-Then 패턴으로 테스트를 진행할 때, Transaction이나 특정 DB 관련해서 추가 설정을 할 수 있으니 필요할 때 찾아서 공부해야겠다.

<br>

---

## Reference

* [스프링 부트 테스트 : @DataJpaTest](https://webcoding-start.tistory.com/20)
