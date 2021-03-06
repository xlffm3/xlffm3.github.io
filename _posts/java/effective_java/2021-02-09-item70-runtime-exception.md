---
title: "[Effective Java] Item 70. 복구할 수 있는 상황에는 검사 예외를, 프로그래밍 오류에는 런타임 예외를 사용하라"
excerpt: "검사 예외도 아니고 런타임 예외도 아닌 Throwable은 정의하지도 말자."
categories:
  - Java
tags:
  - Java
  - Effective Java
date: 2021-02-09
last_modified_at: 2021-02-09
---

## 1. Throwable

![image](https://user-images.githubusercontent.com/56240505/107320267-b7e09000-6ae3-11eb-968f-82617c74696d.png)

Java는 문제 상황을 알리는 타입(Throwable)로 검사 예외, 런타임 예외, 에러 등 세 가지를 제공한다.

Exception, Error를 상속하지 않는 Throwable을 만들 수 있으나, 이는 암묵적으로 검사 예외(Exception의 하위 클래스 중 RuntimeException을 상속하지 않은)처럼 간주된다. 절대 사용하지 말자.

* Throwable 클래스들은 JVM이나 릴리즈에 따라 포맷이 달라지기 때문에, 오류 메시지 포맷을 대부분 상세히 기술하지 않는다.

<br>

## 2. 검사 예외

호출하는 쪽에서 복구하리라 여겨지는 상황이라면 검사 예외를 사용한다. 검사 예외를 던지면 호출자는 그 예외를 try-catch로 잡아 처리하거나, 더 바깥으로 전파하도록(throws) 강제한다. 메서드의 검사 예외는 메서드를 호출했을 때 발생할 수 있는 유력한 결과임을 API 사용자에게 알려준다.

* 호출자가 예외 상황에서 벗어나는 데 필요한 정보를 알려주는 메서드를 함께 제공하는 것이 중요하다.
  * 잔고 부족으로 인한 검사 예외가 있을 때, 잔고를 확인하는 접근자 메서드를 제공해야 한다.

<br>

## 3. 비검사 Throwable

비검사 Throwable은 런타임 예외와 에러다. 프로그램에서 잡을 필요가 없거나 혹은 통상적으로 잡지 않으며, 프로그램에서 비검사 예외나 에러를 던졌다는 것은 복구가 불가능하거나 실행해도 득보다 실이 많음을 의미한다. Throwable을 잡지 않은 스레드는 오류 메시지를 뱉고 중단된다.

### 3.1. RuntimeException

프로그래밍 오류를 나타낼 때는 런타임 예외를 사용한다. 대부분의 예외는 특정 명세의 전제조건을 만족하지 못했을 때 발생한다.

* 특정 예외가 복구 가능하다고 믿으면 검사 예외를, 아니라면 런타임 예외를 사용한다.
  * 확신하기 어렵다면 비검사 예외를 사용하는 것이 낫다.

### 3.2. Error

에러는 보통 JVM이 자원 부족, 불변식 깨짐 등으로 작업을 수행할 수 없음을 나타낸다. Error 클래스를 상속해 하위 클래스를 만드는 일은 지양한다. 개발자가 작성하는 비검사 Throwable은 반드시 RuntimeException의 하위 클래스여야 한다.

<br>

---

## Reference

* Effective Java 3/E(Joshua Bloch 저)
