---
title: "[SQL 첫걸음] 요약 정리"
excerpt: "SQL 첫걸음(아사이 아츠시 저)을 읽고 요약 정리한다."
categories:
  - Database
tags:
  - Database
  - 도서
date: 2021-01-19
last_modified_at: 2021-01-19
---

## 1. 서론

![image](https://user-images.githubusercontent.com/56240505/104988482-8122d700-5a5b-11eb-94b6-e738e87e7732.png)

예전에 SQLD 자격증 취득을 위해 Database와 SQL을 학습했었는데, SQL을 직접 사용할 일이 많이 없다보니 기억이 가물가물해졌습니다. 😥 리마인드한다는 마음으로 가볍게 읽을 수 있는 SQL 관련 서적을 찾던 중 알게된 책입니다. 입문자를 위한 책이다 보니 내용이 크게 어렵거나 딱딱하지 않습니다. 읽는 내내 예전에 배웠던 내용이 새록새록 떠올라서 재미있게 몰입할 수 있었습니다. 😁

<br>

## 2. 데이터베이스와 SQL

#### DBMS

데이터란 컴퓨터 안에 기록되어 있는 2진수 숫자 정보를 의미하며, 데이터베이스는 비 휘발성 저장장치에 저장되는 영속된 데이터의 집합이다. DBMS(Database Management System)은 DB를 효율적으로 관리하는 프로그램이다. DBMS와 같은 전용 소프트웨어를 사용하면서 얻는 이점은 다음과 같다.

* 생산성.
  * CRUD 등 기본 기능을 DBMS가 제공하기 때문에, 시스템 개발 및 구축시 이러한 기본 기능을 구현할 필요가 없어져 비용 절감 등 생산성이 향상된다.
* 기능성.
  * 대용량 및 대규모 데이터 요청을 처리하는 기능을 제공하며, 유저가 관리 기능을 확장할 수 있어 시스템 개발이 유연해진다.
* 신뢰성.
  * DBMS를 여러 하드웨어에 두어 확장성 및 부하 분산을 구현함으로써 대규모 요청을 신뢰성있게 대응할 수 있다.

DBMS는 데이터 저장 방법에 따라 몇 가지로 분류할 수 있다.

* 계층형 DB.
  * 폴더와 파일 등의 계층 구조로 데이터를 저장하는 방식이다.
  * 하드디스크나 DVD 파일 시스템이 사용하는 DB.
  * 현재 DBMS로서 채택되는 경우는 많지 않다.
* 관계형 DB.
  * 관계 대수에 착안하여 고안되었다.
  * 비유하자면 행과 열을 가진 표 형식의 데이터를 저장하는 형태이다.
* 객체지향 DB.
  * 객체 그대로를 데이터로 저장하는 DB이다.
* XML DB.
  * 마크업 문서 작성에 사용되는 XML 형식으로 기록된 데이터를 저장하는 DB이다.
* KVS(Key-Value Store).
  * 키와 그에 대응하는 값이라는 단순한 형태의 데이터를 저장하는 DB이다.
  * NoSQL이라는 슬로건을 가지며, 열 지향 DB라고도 불린다.

#### SQL

SQL은 관계형 모델에 의해 구축된 DB인 RDBMS를 조작할 때 사용하는 언어다.

* DML : 데이터 조작.
* DDL : 데이터 정의.
* DTL : 데이터베이스 제어.

#### RDBMS

RDBMS 또한 웹 시스템처럼 클라이언트/서버 모델로 시스템이 구성된다. 그러나 RDBMS는 사용자 별로 DB 접근을 제한하기 때문에, SQL 명령을 요청하기 이전에 사용자 인증이 필요하다. 한 번 DB에 접속하면 재접속 없이 SQL 명령을 여러 번 보낼 수 있다.

웹 서버는 동적 컨텐츠를 제공하기 위해 CGI라는 제어용 프로그램을 가지고 있다. 클라이언트로부터 요청이 들어오면 CGI 프로그램을 통해 DB에 접속하여 SQL 명령을 수행하고, 실행 결과를 클라이언트에 반환한다. 이 때, CGI 프로그램이 DB의 클라이언트가 된다. 대규모 시스템은 성능 향상을 위해 웹 서버와 DB 서버를 분산한다.

<br>

## 3. 테이블에서 데이터 검색

#### 테이블 구조

표 형식의 데이터는 행(레코드)와 열(컬럼/필드)로 구성된다. 행과 열이 만나는 지점을 셀이라고 하며, 하나의 데이터 값이 저장된다. 각 열은 일반 프로그래밍 언어들처럼 자료형을 가진다.

* ``DESC 테이블명`` : 각 열에 대한 정보 등 테이블 구조를 확인하는 명령어다.

자료형에는 INTEGER, CHAR, VARCHAR, DATE, TIME이 있다.

* CHAR : 고정 길이 문자열에 대한 자료형.
  * CHAR(10)인 경우 길이가 11인 문자열은 저장이 불가능하며, 길이가 10보다 작은 경우 나머지를 공백 문자로 채운 후 저장한다.
* VARCHAR : 가변 길이 문자열에 대한 자료형.
  * 최대 길이를 지정할 수 있으나, 데이터 크기에 맞춰 저장 공간의 크기가 변경된다.

#### 검색 조건

``SELECT 열 FROM 테이블명 WHERE 조건식``을 통해 특정 행을 추출한다.

* 조건식 참/거짓은 ``!=`` 뿐만 아니라 ``<>`` 기호도 사용이 가능하다.
* CHAR, DATE, TIME 자료형의 리터럴 상수를 비교할 때는 \`(싱글 쿼트)를 감싸 사용한다.
  * 한글 등 영어가 아닌 열 이름과 테이블 이름은 "(더블 쿼트)를 감싸 참조한다.
* NULL은 ``IS NULL`` 혹은 ``IS NOT NULL``을 통해 조건을 검사한다.
* AND, OR, NOT 연산자 규칙은 일반 프로그래밍 언어와 동일하다.
  * ``WHERE NO = 1 OR 2``는 상수 2가 항상 참이기 때문에 잘못된 결과가 나온다.
* 문자열은 ``LIKE``로 패턴 매칭할 수 있다.
  * ``%``는 임의의 문자열이며 빈 문자열에도 매치된다.
  * ``_``는 임의의 문자 하나이다.
* 메타 문자를 검색할 때는 이스케이프 문자를 사용한다.
  * \'는 \\가 아닌 \'를 이스케이프 문자로 사용한다.

<br>

## 4. 정렬과 연산

#### 정렬

명령어를 지정하지 않으면 기본적으로 ``SELECT`` 조회 결과는 오름차순 정렬이 적용된다. ``ORDER BY 열 DESC`` 및 ``ORDER BY 열 ASC`` 구를 사용하여 조회 결과를 특정 열을 기준으로 내림차순 혹은 오름차순 정렬한다. 문자열 자료형은 사전식 순서에 의해 정렬된다.

* ``SELECT * FROM 테이블명 ORDER BY 열1 ASC, 열2 DESC`` 처럼 복수의 열을 정렬 기준에 포함할 수 있다.
  * ``ORDER BY``로 지정된 열에 NULL 값이 있는 행은 가장 처음 혹은 가장 마지막에 표시된다.
* ``SELECT * FROM 테이블명 ORDER BY 열 LIMIT 숫자`` 처럼 표시할 상위 데이터 개수를 제한할 수 있다.
* ``SELECT * FROM 테이블명 ORDER BY 열 LIMIT 숫자 OFFSET 인덱스 번호``로 몇 번째 행부터 데이터를 표시할지 위치를 지정할 수 있다.
  * 인덱스는 배열의 인덱스처럼 0부터 시작한다.

#### 사칙연산 및 기타 함수

사칙연산은 일반적인 프로그래밍 언어와 규칙이 유사하다. ``SELECT``, ``WHERE``, ``ORDER BY`` 등의 구에서 사용이 가능하다. SQL은 숫자 관련 함수(``MOD``, ``ROUND``)와 문자열 관련 함수(``SUBSTRING``, ``CONCAT``, ``LENGTH``) 및 날짜 관련 함수 등을 제공한다.

* ``SELECT 열1, 열2 * 열 3 AS 별명 FROM 테이블명``처럼 연산의 결과를 하나의 열로 표시해 보일 수 있다.
  * 열 이름에 ``AS``로 별명을 지을 수 있다.
* 기본 예약어 명령어는 ``WHERE`` -> ``GROUP BY`` -> ``HAVING`` -> ``SELECT`` -> ``ORDER BY`` 순으로 처리된다.
  * ``WHERE`` 구에서는 ``SELECT`` 구에서 지정한 별명을 사용할 수 없다.
  * ``ORDER BY`` 구에서는 별명이 사용 가능하다.
* NULL 값은 사칙연산을 적용해도 값이 NULL이다.
* ``OCTET_LENGTH``는 ASCII 문자수(``CHAR_LENGTH``)가 아닌 바이트 단위로 길이를 계산한다.
  * 인코딩 방식에 따라 한글은 바이트 수가 서로 상이하다.
* ``COALESCE(A, X)`` 함수는 A열의 데이터가 NULL일 때 X로 치환해주는 함수이다.

#### 조건문

```sql
SELECT
CASE A
  WHEN 1 THEN "남자"
  WHEN 2 THEN "여자"
  ELSE "미지정"
END AS "성별" FROM 테이블명;
```

* ``ELSE``를 생략하면 자동적으로 ``ELSE NULL``이 된다.
* 위 조건문은 A열을 비교 연산자로 값들과 비교하기 때문에 NULL을 비교할 수 없다.
  * ``CASE WHEN A = 1 THEN "남자" WHEN A IS NULL THEN "미지정"`` 처럼 검색 조건문을 사용해야 한다.

<br>

## 5. 데이터의 추가, 삭제, 갱신

#### 명령어 정리

* ``INSERT INTO 테이블명 VALUES(값1, 값2...)``로 테이블에 한 행을 추가한다.
  * 값에 DEFAULT 키워드를 사용하면 열의 DEFAULT 값이 명시적으로 저장된다.
* ``INSERT INTO 테이블명(열1, 열2...) VALUES(값1, 값2...)``처럼 값을 저장할 특정 열을 지정할 수 있다.
  * 별도로 지정하지 않은 열은 묵시적으로 DEFAULT 값이 자동 저장된다.
* ``DELETE FROM 테이블명 WHERE 조건식`` 으로 특정 행을 삭제할 수 있다.
* ``UPDATE 테이블명 SET 열1 = 값1, 열2 = 값2, ... WHERE 조건식``으로 특정 행에 데이터 갱신이 가능하다.
  * ``UPDATE 테이블명 SET 열1 = 열1 + 1``과 같은 연산도 가능하다.
    * ``SET`` 구의 실행 순서는 DB 제품마다 상이하다.

#### 물리 삭제와 논리 삭제

``DELETE`` 키워드를 통한 물리 삭제 외에도 논리 삭제라는 개념이 존재한다. 데이터를 실제로 삭제하지 않고, 삭제 플래그의 값을 변경하여 값을 삭제한 것으로 가정하는 것이다.

* 데이터를 삭제하지 않기 때문에 삭제되기 전의 상태로 되돌릴 수 있다.
* 추후에 통계 등에 데이터를 유용하게 사용할 수 있다.
* 실제로 데이터가 삭제되지 않기 때문에 DB 크기가 증가하여 검색 속도가 떨어질 수 있다.

<br>

## 6. 집계와 서브쿼리

#### 집계 함수

SQL에서는 ``COUNT``, ``SUM``, ``AVG``, ``MIN``, ``MAX`` 등의 집계 함수를 제공한다. 집계 함수는 집합 안에 있는 NULL값을 제외하고 계산한다.

* ``SELECT DISTINCT 열 FROM 테이블명``으로 중복값을 제거할 수 있다.
  * ``ALL`` 키워드를 사용하면 중복이 제거되지 않는다.
* ``COUNT(DISTINCT 열)``처럼 집계 함수에서도 ``ALL``, ``DISTINCT`` 등의 키워드를 사용할 수 있다.

#### 그룹화

``GROUP BY`` 구를 통해 테이블을 그룹화할 수 있으며, ``DISTINCT``와 같이 중복이 제거되는 효과가 있다. ``GROUP BY``는 집계 함수와 같이 사용하여, 각 그룹들에 대해 여러 집계 결과를 표시할 때 유용하다.

* 기본 예약어 명령어는 ``WHERE`` -> ``GROUP BY`` -> ``HAVING`` -> ``SELECT`` -> ``ORDER BY`` 순으로 처리된다.
* ``WHERE`` 구 시점에서는 그룹화 처리가 이루어지지 않기 때문에 집계 함수를 사용할 수 없다.
  * ``WHERE COUNT(열) = 1 GROUP BY 열``의 케이스를 생각해보자.
* 따라서 ``HAVING`` 구에서 집계함수를 사용한 조건식을 사용할 수 있다.
  * 마찬가지로 ``HAVING``과 ``GROUP BY`` 구는 ``SELECT``에서 지정한 별명의 사용이 불가능하다.
* ``GROUP BY``로 지정한 열이 아닌 열들은 집계 함수를 사용하지 않으면 ``SELECT`` 구에 사용할 수 없다.
  * ``SELECT NAME, QUANTITY FROM TABLE GROUP BY NAME``는 에러가 발생한다.
    * 1개 NAME에 대해 반환해야 할 QUANTITY 값이 여러 개일수 있기 때문이다.

#### 서브쿼리

서브쿼리란 ``SELECT`` 명령에 의한 데이터 질의로, 상부가 아닌 하부의 부수적인 질의를 의미한다.

* ``DELETE FROM TABLE WHERE NO = (SELECT MIN(A) FROM TABLE)``과 같이 서브 쿼리를 통해 하부 쿼리를 포함할 수 있다.
  * 서브쿼리의 결과가 단 하나의 값만 반환하는 것을 스칼라 값이라고 한다.
  * 스칼라 값은 = 비교 연산이 가능하다.
* ``SELECT``, ``SET``, ``FROM``, ``INSERT`` 구에서도 사용이 가능하며 서브쿼리에 ``AS`` 별명을 지을 수도 있다.
  * ``FROM`` 구에서는 스칼라 서브쿼리가 아닌 서브쿼리도 사용이 가능하다.
  * ``INSERT INTO TABLE SELECT * FROM TABLE2``와 같이 명령의 결과를 테이블에 바로 추가함으로써 데이터 복사를 간편하게 수행할 수 있다.
    * 열과 자료형이 일치해야 한다.

#### 상관 서브쿼리

서브쿼리와 부모쿼리가 서로 연관된 경우 서브쿼리는 상관 서브쿼리라고 칭한다. ``EXISTS``, ``NOT EXISTS``, ``IN``, ``NOT IN`` 술어를 통해 서브쿼리를 사용했을 때 특정 데이터가 집합에 존재하는지 아닌지 등을 판별할 수 있다.

```sql
UPDATE TABLE1 SET A = "있음" WHERE
  EXISTS (SELECT * FROM TABLE2 WHERE TABLE1.NO = TABLE2.NO);

SELECT * FROM TABLE1 WHERE NO IN (3, 5);

SELECT * FROM TABLE1 WHERE NO IN (SELECT NO FROM TABLE2);
```

열 이름이 중복되는 경우 테이블 명을 명시해서 참조한다. ``IN``과 ``NOT IN``은 집합에 NULL이 있는 경우 제대로된 비교가 불가능하다.

<br>

## 7. 데이터베이스 객체 작성과 삭제

#### 데이터베이스 객체

DB 관점에서의 객체란 DB 내에서 실체를 가지는 어떤 것을 의미한다. 테이블, 뷰, 인덱스 등이 객체에 해당된다. 스키마란 DB 객체가 만들어지는 장소이자 그릇이다.

* 즉, 스키마는 테이블들을 정의하여 담고 테이블은 열들을 정의하여 담는 역할을 한다.
* 스키마 설계 : DDL로 테이블을 작성해서 DB를 구축해나가는 작업이다.
  * 하나의 스키마에서는 테이블명이 중복되면 안 되지만, 서로 다른 스키마끼리는 테이블명이 중복되어도 상관없다.

#### 테이블 관리

테이블을 생성할 때는 ``CREATE TABLE 테이블명 (열 정의1, 열 정의 2, ...)`` 명령어를 사용한다. 열 정의는 ``열명 자료형 [DEFAULT 기본값] [NULL | NOT NULL]``을 명시한다.

테이블 삭제는 ``DROP TABLE 테이블명``을 사용하며, 테이블 정의를 그대로 둔 채 데이터만 삭제하는 경우 ``TRUNCATE TABLE 테이블명``을 이용한다. ``DROP FROM 테이블명``도 모든 행을 삭제하지만, 여러 가지 내부 처리로 인해 행이 많은 경우 처리 속도가 느려지는 단점이 있다.

테이블 수정은 ``ALTER TABLE 테이블명 변경명령``을 사용한다.

* ``ALTER TABLE 테이블명 ADD 열 정의``
* ``ALTER TABLE 테이블명 MODIFY 열 정의``
  * 문자열 자료형 열의 최대 크기를 줄일 때, 테이블에 기존에 저장된 데이터의 일부가 잘려나갈 수 있다.
* ``ALTER TABLE 테이블명 CHANGE [기존 열 이름] [신규 열 정의]``
* ``ALTER TABLE 테이블명 DROP 열명``

#### 제약

테이블을 정의할 때 다양하게 제약 사항을 정의할 수 있다.

```sql
CREATE TABLE SAMPLE (
  열1 INTEGER NOT NULL UNIQUE,
  ...
  CONSTRAINT PKEY_SAMPLE PRIMARY KEY (열1, 열2)
);
```

``CONSTRAINT`` 키워드로 제약 이름을 설정할 수 있다. ``ALTER`` 키워드로 테이블의 열들에 대해 수정하는 것 처럼 제약 역시 추가 및 삭제가 가능하다.

* ``ALTER TABLE 테이블명 MODIFY 열 정의 [제약 사항]``
  * 열 단위의 제약을 추가하거나 삭제가 가능하다.
* ``ALTER TABLE 테이블명 ADD CONSTRAINT [제약 이름] [제약 사항]``
* ``ALTER TABLE 테이블명 DROP CONSTRAINT [제약 이름]``
  * 기본키는 테이블당 하나만 설정할 수 있기 때문에 제약명을 지정하지 않아도 된다.
    * ``ALTER TABLE 테이블명 DROP PRIMARY KEY``

기본키(Primary Key)는 테이블의 행 한 개를 특정할 수 있는 검색키다.

* 유일성 제약 : 기본키로 설정된 열은 중복하는 데이터 값을 가질 수 없다.
* 기본키는 NULL을 허용하지 않으므로 NOT NULL 설정이 필요하다.
* 기본키를 구성하는 열은 복수라도 상관없다.
  * A열과 B열이 기본키인 경우, 한 행의 A열 데이터가 중복된 값이더라도 B열의 데이터가 중복된 값이 아니면 기본키 제약을 위반한 것이 아니다.

#### 인덱스

인덱스는 테이블에 붙여진 색인이며, 검색 속도를 향상시키는 역할을 한다. 테이블에 인덱스가 지정되어 있으면 ``WHERE`` 조건이 지정된 ``SELECT`` 명령의 처리 속도가 향상된다.

* 인덱스는 스키마 내부에서 테이블과 별도로 독립된 객체로 작성된다.
* 테이블에 의존하는 객체이다.
  * 테이블이 삭제되면 인덱스도 함께 삭제된다.
* 인덱스는 이진 트리를 통한 탐색을 사용한다.
* 단점.
  * 모든 ``SELECT`` 명령에 만능인 것은 아니다.
  * ``INSERT`` 명령의 경우 인덱스를 최신 상태로 갱신하기 때문에 처리 속도가 증가한다.
  * 데이터의 종류가 적을수록 효율이 떨어진다.
    * 인덱스로 지정한 열에 ``예, 아니오`` 두 가지 데이터만 있다면 이진 탐색 효율성이 떨어진다.

``CREATE INDEX 인덱스명 ON 테이블명 (열명1, 열명2, ...)``로 인덱스 객체를 생성한다. 해당 인덱스가 어느 테이블의 어느 열에 관한 것인지 지정해야 한다.

``DROP INDEX 인덱스명``으로 스키마 객체의 삭제가 가능하며, 테이블 내의 객체인 경우 ``ON 테이블명``을 명시한다.

``SELECT`` 명령시 ``WHERE`` 절에 인덱스로 지정한 열을 사용하지 않으면 해당 인덱스를 전혀 사용하지 않게 된다.

* ``EXPLAIN SQL 명령`` : 어떤 상태로 해당 명령을 수행하는지 설명해준다.
* DB는 ``SELECT`` 명령을 실행하기 전에 인덱스 유무 및 인덱스 품질 등을 고려하여 최적화 실행 계획을 세운다.

#### 뷰

단순한 ``SELECT`` 등의 명령은 스키마 객체가 될 수 없으나, 자주 사용하거나 복잡한 ``SELECT`` 서브 쿼리는 뷰 객체(가상 테이블)로 등록하여 편리하게 사용할 수 있다.

* ``CREATE VIEW 뷰명 AS [SELECT 명령]``
* ``CREATE VIEW 뷰명(열명1, 열명2, ...) AS [SELECT 명령]``
* ``DROP VIEW 뷰명``

뷰는 DB 객체로서 저장장치에 저장되지만, 테이블처럼 대량의 저장 공간을 필요하지 않는다. 단순히 ``SELECT`` 명령 쿼리만 저장되기 때문이다.

* 뷰를 참조할 때마다 뷰에 등록된 ``SELECT`` 명령이 새로 실행되기 때문에 CPU 자원을 많이 사용한다.
  * 대용량 데이터의 집계 처리를 할 때 뷰를 중첩해서 사용하면 성능 하락으로 이어진다.
* Materialized View.
  * 뷰가 처음 참조되었을 때 데이터를 캐싱해둔다.
  * 이후 다시 참조할 때는 ``SELECT`` 명령을 실행하는 것이 아닌, 캐싱된 데이터를 사용한다.
* 뷰를 구성하는 ``SELECT`` 명령은 단독으로 실행 가능해야 한다.
  * 부모 쿼리와 연관된 서브쿼리는 사용이 불가능하다.
    * 함수 테이블(사용자 정의 함수)를 통해 약점의 회피가 가능하다.
    * 함수 테이블은 인수값에 따라 ``WHERE`` 조건을 붙여 상관 서브쿼리처럼 동작할 수 있기 때문이다.

<br>

## 8. 복수의 테이블 다루기

#### 집합 연산

테이블의 집합 연산은 세로(행) 방향으로 데이터가 늘어나거나 줄어들게 된다.

* 합집합 : ``[SELECT 명령1] UNION [SELECT 명령2] UNION ...``
  * 서로 다른 열 구성의 테이블은 애스터리스크를 사용할 수 없다.
    * 열을 따로 지정해주면 합집합 사용이 가능하다.
  * ``ORDER BY``는 마지막 구에서만 지정이 가능하며, 정렬 열명은 별명을 붙여 통일해야 한다.
    * ``SELECT A AS C FROM T1 UNION SELECT B AS C FROM T2 ORDER BY C``
  * ``UNION ALL``을 통해 중복을 제거하지 않은 합집합 결과를 얻는다.
* 교집합 : ``INTERSECT``
* 차집합 : ``EXCEPT``

#### 결합 연산

테이블의 결합 연산은 가로(열) 방향으로 데이터가 늘어난다.

* 교차 결합(곱집합).
  * ``SELECT * FROM 테이블1, 테이블2, ...``

그러나 교차 결합은 테이블 수가 많아지면 조합의 수가 너무 많아진다.

#### 내부 결합

교차 결합으로 계산된 곱집합에서 원하는 조합을 검색하는 것을 내부 결합(Inner Join)이라고 한다.

```sql
SELECT S.상품명, M.메이커명
FROM 상품 S INNER JOIN 메이커 M
ON S.메이커코드 = M.메이커코드
WHERE S.상품분류 = '식료품';
```

테이블 명이 길어지는 경우 별명을 추가하여 사용할 수 있다.

* 기본키 : 테이블에서 하나의 행을 특정할 수 있는 중복되지 않는 값을 가지는 열이다.
* 외래키 : 다른 테이블의 기본키를 참조하는 열이다.
* 두 테이블이 결합할 때 어느 쪽이 하나의 행만 가지는지에 따라 1:1, 1:多, 多:1, 多:多 등의 관계가 결정된다.

그러나 내부 결합의 경우 ``ON`` 절의 결합 조건에 해당하는 데이터가 어느 한 쪽에 없는 경우, 해당 데이터의 결과는 조회에서 누락된다.

#### 외부 결합

외부 결합은 어느 한 쪽에만 존재하는 데이터행을 어떻게 다룰지 변경할 수 있는 결합 방법이다.

```sql
SELECT S.상품명, J.재고수
FROM 상품 S LEFT JOIN 재고수 J
ON S.상품코드 = J.상품코드
WHERE S.상품분류 = '식료품';
```

기준이 되는 특정 테이블과의 결합 방향을 지정하면 다채로운 결합 결과를 조회할 수 있다. ``LEFT`` 외에도 ``RIGHT``를 사용할 수 있다.

#### 관계형 모델

관계형 모델은 다음과 같은 정의를 하고 있다.

* 열 = 속성.
* 행 = 튜플.
* 테이블 = 릴레이션.

릴레이션에 대한 연산이 집합에 대한 연산에 대응된다는 관계 대수 이론을 기반으로 관계형 모델이 형성된다. 릴레이션(테이블)을 연산한 결과는 릴레이션(테이블)이다.

<br>

## 9. 데이터베이스 설계

#### ER 다이어그램

ER다이어그램은 테이블을 설계할 때 테이블 간의 관계를 명확히 하기 위해 사용하는 설계도다.

* 물리명 : ``CREATE TABLE``로 지정하는 테이블 및 열 이름들을 의미한다.
* 논리명 : 설계상의 이름이다.
* 너무 큰 데이터의 경우 LOB 자료형을 사용하지만, 인덱스를 사용할 수 없다.
* ``PRIMARY_KEY`` 혹은 ``UNIQUE`` 등으로 유일성을 지정한 열은 ``AUTO_INCREMENT``를 통해 자동증가 열로 사용할 수 있다.
  * ``INSERT``될 때마다 기본키의 값을 자동으로 증가시켜서 저장해준다.

#### 정규화

정규화란 DB의 테이블을 규정된 올바른 형태로 변경 및 분할해나가는 작업이다. 테이블에서 중복 및 반복되는 부분을 찾아내서 테이블을 분할하고 기본키를 작성하는 것이 기본 개념이다. 정규화는 **하나의 데이터는 한 곳에 있어야 한다**는 규칙에 근거한다.

* OOP의 경우, 클래스 및 메서드는 하나의 역할과 책임을 가져야 한다는 비슷한 규칙이 있다.
  * 상호 의존성을 최대한 낮춤으로써 외부 변경에 의한 영향의 전파 가능성을 낮출 수 있다.
* DB 또한 정규화가 잘 이루어지지 않으면 데이터를 변경할 때 여러 테이블에 접근해야 한다.
  * 인덱스가 지정된 열의 데이터가 변경되는 경우 인덱스 또한 재구축해야 한다.
* 기본키는 분할한 테이블끼리 연결하기 위한 내부 데이터이므로 변경될 일이 거의 없다.
  * 정규화로 인덱스 재구축을 억제할 수 있다.

#### 트랜잭션

트랜잭션이란 DB의 상태를 변환시키는 하나의 논리적 기능을 수행하기 위한 작업의 단위 또는 한꺼번에 모두 수행되어야 할 일련의 연산들을 의미한다.

자동 커밋이 켜져 있는 상태면 트랜잭션을 사용하기 위해서는 시작 명령어가 필요하다.

* ``START TRANSACTION``
* ``ROLLBACK``
* ``COMMIT``

작업 상태에 따라 해당 트랜잭션의 결과를 DB에 영속적으로 반영하거나 취소한다.

<br>

---

## Reference

* SQL 첫걸음(아사이 아츠시 저)
