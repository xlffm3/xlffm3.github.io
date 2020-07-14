---
title:  "Spring Boot 개념과 활용 - 20장 : PostgreSQL"
categories:
  - Back-End
  - Spring & Spring Boot
  - Inflearn
tags:
  - Spring & Spring Boot
  - Inflearn
toc: true
toc_sticky: true
last_modified_at: 2020-07-13T08:10:00-05:00
---

## PostgreSQL 설정

> Docker

```sh
docker run -p 5432:5432 -e POSTGRES_PASSWORD=pass -e
POSTGRES_USER=keesun -e POSTGRES_DB=springboot --name postgres_boot -d
postgres
docker exec -i -t postgres_boot bash

su - postgres
psql springboot

//데이터베이스 조회
\list

//테이블 조회
\dt

//쿼리
SELECT * FROM account;
```

<br>

> application.properties

```java
spring.datasource.url=jdbc:postgresql://localhost:5432/springboot
spring.datasource.username=jipark
spring.datasource.password=pass
```

* 의존성 추가 : org.postgresq.

<br>

---

## Reference

* 스프링 부트 개념과 활용 (백기선, Inflearn)