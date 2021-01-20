---
title: "[프로그래머스] SQL 고득점 Kit 문제 풀이"
excerpt: "일반 코딩 테스트 난이도가 딱 이 정도면..."
categories:
  - Database
tags:
  - Database
date: 2021-01-20
last_modified_at: 2021-01-20
---

## 1. SQL 고득점 Kit

![image](https://user-images.githubusercontent.com/56240505/105051345-033dea80-5ab2-11eb-838c-5983afd8424b.png)

GROUP BY의 ``입양 시각 구하기(2)`` 한 문제를 제외하면 크게 어렵지 않았다.

<br>

## 2. 입양 시각 구하기(1)

처음에는 WHERE를 사용해서 문제를 해결했다.

```sql
SELECT HOUR(DATETIME) AS HOUR, COUNT(DATETIME) AS COUNT
FROM ANIMAL_OUTS
WHERE 9 <= HOUR(DATETIME) AND HOUR(DATETIME) < 20
GROUP BY HOUR(DATETIME)
ORDER BY HOUR(DATETIME) ASC;
```

일반적으로 GROUP BY에 대한 조건은 HAVING을 많이 사용하기 때문에 다음과 같이 HAVING을 사용하여 문제를 풀 수 있다.

```sql
SELECT HOUR(DATETIME) AS HOUR, COUNT(DATETIME) AS COUNT
FROM ANIMAL_OUTS
GROUP BY HOUR(DATETIME)
HAVING 9 <= HOUR AND HOUR < 20
ORDER BY HOUR(DATETIME) ASC;
```

HAVING에서 HOUR가 아닌 HOUR(DATETIME)을 사용하면 에러가 난다. 구글링해도 원인을 알 수 없었다. HAVING은 SELECT보다 선행되어 처리되기 때문에 Alias 사용이 불가능하다고 알고 있었는데 SQL 제품마다 다른가보다.

```sql
HAVING HOUR BETWEEN 9 AND 19
```

AND 조건 대신 BETWEEN도 활용할 수 있다.

<br>

## 3. 입양 시각 구하기(2)

``입양 시각 구하기(1)`` 문제와 달리 데이터 셋에 없는 시간들에 대해서도 COUNT를 집계해야 한다.

[[MySQL]프로그래머스_입양 시각 구하기(2) (UNION/변수선언)](https://syujisu.tistory.com/172)을 참고하여서 풀었다. UNION을 사용하거나 로컬 변수를 활용해야 한다. UNION을 활용한 풀이는 딱히 어렵지 않지만 하드 코딩을 해야한다는 점에서 마음에 들지 않는다.

```sql
SET @G_HOUR := -1;

SELECT (@G_HOUR := @G_HOUR + 1) AS HOUR,
(SELECT COUNT(*) FROM ANIMAL_OUTS
WHERE HOUR(DATETIME) = @G_HOUR) AS COUNT
FROM ANIMAL_OUTS
WHERE @G_HOUR < 23;
```

해당 풀이 방법은 while 반복문의 동작 원리와 비슷하다. 로컬 변수를 선언한 뒤 WHERE 조건에 위배되지 않는 동안 로컬 변수의 값을 계속 늘려가며 데이터를 조회한다.

* SET 옆에 변수명과 초기값을 설정한다.
  * @가 붙은 변수는 프로시저가 종료되어도 유지된다.
* SQL 문법에서의 대입 연산자는 :=이다.

<br>

## 4. 없어진 기록 찾기

비교적 간단한 JOIN 문제이다.

```sql
SELECT OUTS.ANIMAL_ID, OUTS.NAME
FROM ANIMAL_OUTS AS OUTS LEFT JOIN ANIMAL_INS AS INS
ON OUTS.ANIMAL_ID = INS.ANIMAL_ID
WHERE INS.ANIMAL_ID IS NULL
ORDER BY ANIMAL_ID ASC;
```

EXISTS를 사용해서도 풀 수 있다.

```sql
SELECT ANIMAL_ID, NAME
FROM ANIMAL_OUTS
WHERE NOT EXISTS
  (SELECT NAME FROM ANIMAL_INS WHERE ANIMAL_OUTS.ANIMAL_ID = ANIMAL_INS.ANIMAL_ID)
ORDER BY ANIMAL_ID ASC;
```

EXISTS에 사용하는 서브쿼리는 상관 서브쿼리이다.

<br>

## 5. DATETIME에서 DATE로 형 변환

```sql
SELECT ANIMAL_ID, NAME, DATE_FORMAT(DATETIME, '%Y-%m-%d') AS "날짜"
FROM ANIMAL_INS
ORDER BY ANIMAL_ID ASC;
```

DATE_FORMAT 이외에도 TO_CHAR, SUBSTRING, REGEX 등의 다양한 함수를 사용하는 풀이도 있다.

* 비고 : MySQL 조건식은 CASE 말고도 IF도 사용할 수 있다.

<br>

---

* [[MySQL]프로그래머스_입양 시각 구하기(2) (UNION/변수선언)](https://syujisu.tistory.com/172)
