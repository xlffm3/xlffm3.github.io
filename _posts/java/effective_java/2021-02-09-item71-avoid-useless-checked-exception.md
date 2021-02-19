---
title: "[Effective Java] Item 71. 필요 없는 검사 예외 사용은 피하라"
excerpt: "검사 예외를 남용하면 쓰기 고통스러운 API를 낳는다."
categories:
  - Java
tags:
  - Java
  - Effective Java
date: 2021-02-09
last_modified_at: 2021-02-09
---

## 1. 검사 예외의 단점

검사 예외를 잘 활용하면 API와 프로그램의 질을 높일 수 있다. 발생하는 문제를 개발자가 처리하여 안전성을 높이게끔 해주기 때문이다. 그러나 다음과 같은 단점으로 인해 여러모로 부담을 주기 마련이다.

* 그러나 과하게 사용하면 쓰기 불편한 API가 된다.
  * try-catch로 잡거나 바깥으로 예외를 던져 전파해야 한다.
* 검사 예외를 던지는 메서드는 스트림 안에서 직접 사용할 수 없다.

API를 제대로 사용해도 발생할 수 있는 예외이거나, 개발자가 의미 있는 조치를 취할 수 있는 경우라면 검사 예외를 사용해도 된다. 이에 해당하지 않는다면 비검사 예외를 사용하는 것이 낫다.

* 다른 검사 예외를 던지는 상황에서 또 다른 검사 예외를 추가하는 경우?
  * 단순히 catch 문 하나만 추가하면 된다.
* 검사 예외를 하나만 던지는 경우?
  * 해당 예외로 인해 검사 예외의 단점이 모두 생기기 때문에 검사 예외를 안 던지는 방법을 고민해본다.

<br>

## 2. 검사 예외의 대안

가장 쉬운 방법은 적절한 결과 타입을 담은 옵셔널을 반환하는 것이다.

* 검사 예외를 던지는 대신 빈 옵셔널을 반환하면 된다.
  * 그러나 예외가 발생한 이유 등 부가 정보를 담을 수 없다.
* 예외는 구체적인 예외 타입 및 부가 정보를 제공할 수 있다.

혹은 검사 예외를 던지는 메서드를 2개로 쪼개 비검사 예외로 바꾼다.

> CheckedException.java

```java
try {
    obj.action(args);
} catch (TheCheckedException e) {
    //에러 상황 대처
}
```

> CheckedException.java

```java
if (obj.actionPermitted(args)) {
    obj.action(args);
} else {
  //에러 상황 대처
}
```

* 해당 리팩토링을 모든 상황에 적용할 수는 없으나 조금 더 쓰기 편한 API를 제공해준다.
* 상태 검사 메서드를 사용하기 때문에 Item 69의 상태 의존적인 메서드와 상태 검사 메서드의 장단점을 참조하자.
  * 스레드 안정성 및 중복 기능 수행으로 인한 성능 저하 등을 고려한다.

<br>

---

## Reference

* Effective Java 3/E(Joshua Bloch 저)