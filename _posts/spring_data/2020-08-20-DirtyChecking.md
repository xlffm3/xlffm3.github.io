---
title: "Dirty Checking"
categories:
  - Spring Data
tags:
  - Spring Data
  - JPA
toc: true
toc_sticky: true
last_modified_at: 2020-08-20T09:13:00-05:00
---

## 1. Dirty Checking

Dirty Checking은 엔티티의 상태 변경 감지를 의미한다. JPA의 엔티티 메니저의 메소드는 CRUD 중 변경인 Update에 관련된 메소드를 지원하지 않는다.

Service 레이어의 Transaction에서 엔티티의 값을 변경시켜보자. 별도의 Update(혹은 Save) 관련 메소드(쿼리)를 실행하지 않아도, Transaction 안에서 엔티티의 변경이 일어나면 변경 내용이 자동으로 데이터베이스에 반영된다.

### 1.1. 원리

JPA에서 엔티티를 조회하면 해당 엔티티의 조회 상태를 스냅샷으로 남긴다. 이후, Transaction이 끝나는 시점에서 해당 스냅샷과 비교하여 다른 점이 있다면 Update Query를 데이터베이스로 전달한다.

따라서 JPA는 Transaction이 끝나는 시점에 변화가 있는 모든 엔티티 객체를 데이터베이스에 자동으로 반영해준다.

### 1.2. 필요 조건

* 영속성 컨텍스트가 관리하는 영속 상태(Managed) 엔티티여야 한다.
* Transaction 내부에서 엔티티를 변경해야 한다.
  * @Transactional 어노테이션을 달거나, 혹은 EntityTransaction으로 Transaction의 범위를 지정한 상태에서 값을 변경시키면 Dirty Checking이 발동된다.
  * 그렇지 않은 경우 엔티티의 값을 변경시키면 수정 내용이 반영되지 않는다.
    * 반영을 시키려면 ``save()`` 혹은 ``saveAndFlush()`` 메소드를 원하는 시점에 실행시켜 수정 내용을 반영해야  한다.

<br>

---

## Reference

* [더티 체킹 (Dirty Checking)이란?](https://jojoldu.tistory.com/415)
