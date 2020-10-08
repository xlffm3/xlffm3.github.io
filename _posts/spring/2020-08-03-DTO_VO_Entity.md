---
title: "DTO vs VO vs Entity"
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-08-03T20:13:00-05:00
---

## 1. 혼란

개발을 진행하다가 구글링을 하면 용어가 뒤죽박죽이다. 누구는 VO라 하고, 누구는 DTO라고 하고. 정리된 글을 읽어도 두 개념 모두 큰 차이가 없고 비슷해 보인다. 정확하게 두 개념을 구분짓기 보다는 혼용하는 사람들이 더 많은 느낌이다. 공부차원에서 개념들을 정리해보았다.

<br>

## 2. DTO(Data Transfer Object)

전송되는 데이터의 컨테이너이다. 데이터를 저장하여 사용하도록 하는 부분에서 필요하다. 특히 DTO는 Controller, View, Business Layer, Persistent Layer 등 계층 간의 데이터 교환을 위한 Java 빈즈를 의미한다.

VO와 비교하여 보면 DTO는 같은 시스템에서 사용되는 것이 아닌, 다른 시스템으로 전달하는 작업을 처리하는 객체이다.

개발 환경에서 보통 데이터는 다음과 같은 흐름으로 이동한다.
* 서버 측 : Database Column Data -> DTO -> API(JSON or XML) -> Client
* 클라이언트 측 : Server -> API(JSON or XML) -> DTO -> View or Local Database System

비즈니스 로직을 가지지 않는 순수한 데이터 객체이고, 가변의 성격을 갖고 있기 때문에 getter와 더불어 setter 메소드도 가진다.

<br>

## 3. VO(Value Object)

데이터 그 자체로 의미있는 것을 담고 있는 객체이다. DTO와 동일한 개념이지만 차이점은 Read-Only 속성이다. 불변성 특성상 getter만 가진다.

VO는 ``equals()`` 및 ``hashcode()``를 오버라이딩해야 한다. VO 내부에 선언된 필드의 모든 값들이 VO 객체마다 값이 같아야, 똑같은 객체라고 판별한다.

Enum이 대표적인 예이며, 클래스의 값을 중간에 바꿀 수 없기 때문에 새로 만들어야 한다는 특징이 있다. (Color.Red 등)

<br>

## 4. Entity

Entity 클래스는 DB의 테이블 내에 존재하는 컬럼만을 속성(필드)로 가지는 클래스, 데이터의 집합이다. 엔티티 클래스는 상속을 받거나 구현체이면 안되며, 테이블 내에 존재하지 않는 컬럼을 가져서도 안 된다.

물론 JPA를 사용하면 ``@Transient`` 어노테이션을 통해 컬럼으로 매핑이 필요하지 않은 필드들을 가질 수 있다.

<br>

## 4. 결론

한글 레퍼런스들을 참조하는데 VO에 대해 말들이 너무 다 달랐다. 결국 스택오버플로우에 있는 내용들을 참조해서 정리했는데... 결과적으로는 넣어진 데이터를 getter로 접근한다는 점에서 주 목적은 같다.

<br>

---

## Reference

* [Difference between DTO, VO, POJO, JavaBeans?](https://stackoverflow.com/questions/1612334/difference-between-dto-vo-pojo-javabeans)
* [[JAVA] DAO, DTO, VO 차이](https://lemontia.tistory.com/591)
* [VO vs DTO](https://ijbgo.tistory.com/9)
