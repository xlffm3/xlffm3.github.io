---
title: "[Effective Java] Item 86. Serializable을 구현할지는 신중히 결정하라"
excerpt: "보호된 환경에서만 쓰일 클래스가 아니라면 Serializable 구현은 아주 신중하게 이뤄져야 한다."
categories:
  - Java
tags:
  - Java
  - Effective Java
date: 2021-02-11
last_modified_at: 2021-02-11
---

## 1. Serializable 구현의 단점

### 1.1. 캡슐화

Serializable을 구현한 클래스는 간단하게 직렬화할 수 있으나 릴리스한 뒤에는 수정하기 어렵다. 구현하면 직렬화된 바이트 스트림 인코딩(직렬화 형태) 또한 하나의 공개 API가 된다. 클래스가 널리 퍼지면 해당 직렬화 형태도 다른 공개 API와 마찬가지로 영원히 지원해야 한다.

* 커스텀 직렬화를 사용하지 않고 Java 기본 직렬화를 사용하면, 직렬화 형태는 최초 적용 당시 클래스의 내부 구현 방식에 영원히 종속된다.
  * 기본 직렬화 형태의 private 및 package-private 인스턴스 필드들 마저 API로 공개되어 캡슐화가 깨진다.
* 모든 직렬화된 클래스는 고유 식별 번호인 static final long UID를 부여 받는데, 이를 명시하지 않으면 시스템이 런타임에 자동으로 생성해 넣어준다.
  * 해당 값을 생성하는데 클래스 멤버들이 고려된다.
  * 나중에 이들 중 하나라도 수정한다면 UID 값이 변한다.
  * 자동 생성되는 값에 의존하면 쉽게 호환성이 깨져 런타임 에러가 발생한다.

### 1.2. 버그 및 보안

객체 생성의 기본 매커니즘인 생성자를 이용하지 않는다. 즉, 역직렬화란 숨은 생성자로서 공격자가 객체 생성 도중 객체 내부를 들여다 볼 수 있게 한다. 기본 역직렬화를 사용하면 불변식 깨짐과 허가되지 않은 접근에 쉽게 노출된다.

### 1.3. 신규 릴리스와 테스트

Serializable을 구현한 클래스는 신버전을 릴리스할 때 테스트할 것이 늘어난다. 신버전 인스턴스를 직렬화한 후 구버전으로 역직렬화할 수 있는지 등을 테스트해야 한다. 테스트해야 할 양은 직렬화 가능 클래스의 수와 릴리스 횟수에 비례해 증가한다.

* 양방향 직렬화/역직렬화가 모두 성공하고, 원래 객체를 잘 복제해내는지 반드시 확인해야 한다.

<br>

## 2. 주의사항

역사적으로 **값** 클래스와 컬렉션 클래스들은 Serializable을 구현했으나, 스레드 풀처럼 **동작**하는 객체를 표현하는 클래스들은 대부분 구현하지 않았다.

상속용으로 설계된 클래스와 인터페이스 대부분은 Serializable을 구현 및 확장하면 안 된다. 하위 클래스들에 큰 부담이 된다. 단, Serializable을 구현한 클래스만 지원하는 프레임워크를 사용하는 경우 다른 방도가 없이 구현해야 한다.

클래스의 인스턴스 필드가 모두 직렬화 및 확장이 가능해야 한다면, 반드시 하위 클래스에서 ``finalize()`` 메서드를 재정의하지 못하게 해야 한다. ``finalize()`` 메서드를 자신이 재정의하고 final로 선언하지 않으면 finalizer 공격을 당할 수 있다.

* 인스턴스 필드 중 기본값으로 초기화되면 위배되는 불변식이 있다면 클래스에 ``readObjectNoData()`` 메서드를 반드시 추가해야 한다.
  * 내부에서 예외를 호출한다.
  * 기존 직렬화 가능 클래스에 직렬화 가능 상위 클래스를 추가하는 드문 경우에 사용한다.

상속용 클래스인데 직렬화를 지원하지 않으면 하위 클래스에서 직렬화를 지원하려 할 때 부담이 늘어난다. 이런 클래스를 역직렬화하려면 상위 클래스에서 매개변수가 없는 생성자를 제공하거나, 하위 클래스에서 직렬화 프록시 패턴을 사용해야 한다.

내부 클래스는 직렬화를 구현하지 말아야 한다. 단, 정적 멤버 클래스는 구현해도 된다.

* 내부 클래스는 바깥 인스턴스의 참조와 유효 범위 안의 지역변수 값들을 저장하기 위해 컴파일러가 생성한 필드들이 자동 추가된다.
  * 해당 필드들이 클래스 정의에 어떻게 추가되는지 정의되지 않은 만큼, 기본 직렬화 형태가 분명하지 않다.

<br>

---

## Reference

* Effective Java 3/E(Joshua Bloch 저)