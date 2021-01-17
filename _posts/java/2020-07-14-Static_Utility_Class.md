---
title: "[Java] 정적 유틸리티 클래스의 객체 생성을 막는 방법"
excerpt: "클래스 용도를 확실하게 명시하는 것이 좋다."
categories:
  - Java
tags:
  - Java
toc: true
toc_sticky: true
last_modified_at: 2020-07-14T08:25:00-05:00
---

## Static Utility Class

* 유틸리티 클래스는 상태를 가지고 있지 않는 클래스이다.
* 어떤 메소드가 인스턴스가 생성되지 않았더라도, 호출 할 것이라면 정적 메소드를 사용한다.
* 정적 유틸리티 클래스가 OOP 관점에서 볼 때 상태를 지니지 않고 메소드만 지닌 구조라 단점도 다수 존재한다.

<br>

## Private 생성자

* IntelliJ에서 정적 유틸리티 클래스를 만들면 Sonar 플러그인이 **"add a private constructor to hide the implicit public one."** 경고를 발생시킨다.
  * 생성자가 하나도 없는 경우에는 묵시적으로 생성자가 만들어진다.
  * 그러므로 유틸리티 클래스의 객체 생성을 방지하기 위해서는 반드시 private 생성자를 하나 둬야한다.
* 클래스 용도를 확실하게 static한 클래스로 제한하는 것을 명시하는 역할이다.
* 사용하지 않는 것은 막는 것이 바람직하다.

<br>

---

## Reference

*	[정적 메소드는 언제 써야하는가? :: 마이구미](https://mygumi.tistory.com/253)
