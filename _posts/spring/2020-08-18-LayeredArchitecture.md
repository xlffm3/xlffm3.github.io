---
title: "Layered Architecture와 Spring"
excerpt: "Spring에서 Layered Architecture란 무엇인가?"
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-08-15T09:13:00-05:00
---

## 1. Layered Architecture란?

위키백과에서 정의한 Layered Architecture란 비즈니스 로직을 완전히 분리하여 데이터베이스 시스템과 클라이언트의 사이에 배치한 클라이언트 서버 시스템의 일종이다.

쉽게 말하자면 유사한 관심사들을 레이어로 나눠서 추상화하여 수직적으로 배열하는 아키텍처이다.  필요한 경우 한 층을 다른 것으로 교체해도 그 구조가 유지된다. 이 경우, 전체 시스템을 다른 환경에서도 사용할 수 있기 때문에 유연성 및 재사용성이 높다.

<br>

## 2. 3-Tier Architecture

3-Tier는 인프라(서버)의 구성 구조이며, MVC는 어플리케이션의 개발에 사용되는 구조이기 때문에 사뭇 다른 면모가 있다.

### 2.1. Presentation Layer

프레젠테이션 계층은 응용 프로그램의 최상위에 위치하고 있으며, 서로 다른 층에 있는 데이터 등과 커뮤니케이션을 한다. GUI 및 Front-End의 영역이며, 사용자 인터페이스를 제공한다.

### 2.2. Application Layer

어플리케이션 계층은 비즈니스 로직 계층 또는 트랜잭션 계층이라고도 한다. 비즈니스 로직은 워크스테이션으로부터의 클라이언트 요청에 대해 마치 서버처럼 행동한다. 그것은 차례로 어떤 데이터가 필요한지를 결정하고, 메인프레임 컴퓨터 상에 위치하고 있을 세 번째 계층의 프로그램에 대해서는 마치 클라이언트처럼 행동한다. Middleware, 즉 Back-End의 영역이다.

### 2.3. Data Layer

데이터 계층은 데이터베이스와 그것에 액세스해서 읽거나 쓰는 것을 관리하는 프로그램을 포함한다. 애플리케이션의 조직은 이것보다 더욱 복잡해질 수 있지만, 3계층 관점은 대규모 프로그램에서 일부분에 관해 생각하기에 편리한 방법이다. Back-End 영역이지만 주로 DB 서버를 의미한다.

<br>

## 3. Spring MVC(Model-View-Controller)

Spring의 Layered Architecture에 대한 정리에 앞서, MVC를 짧게 정리해본다. MVC에 관해서도 나중에 기회가 된다면 상세하게 정리해야겠다. Spring은 MVC 패턴을 사용하여 웹 서비스를 제작한다.

MVC 패턴이란 사용자 인터페이스와 비즈니스 로직을 분리하여 개발하는 방식을 말한다.
* 어플리케이션의 데이터에 해당되는, 비즈니스 규칙을 표현하는 도메인 Model.
* 모델을 사용자에게 보여주는, 프레젠테이션을 표현하는 View.
* 양측 사이에서 이를 제어하는 Controller.

### 3.1. Front Controller

MVC는 프론트 컨트롤러 패턴과 함께 사용된다. 서버로 들어오는 클라이언트의 요청을 가장 앞선에서 받아 처리하는 것이 프론트 컨트롤러이다. Spring은 DispatcherServlet이라는 프론트 컨트롤러를 제공하며, 해당 Servlet이 MVC 아키텍쳐를 관리한다.

DispatcherServlet의 작동 방식에 대한 글은 아니기 때문에 이에 대한 설명은 짧게 축약한다.
* 프론트 컨트롤러가 요청을 받으면, HandlerMapping을 통해 요청을 처리할 수 있는 컨트롤러를 찾아 매핑한다.
* 그 다음, HandlerAdapter를 통해 요청 처리를 위임하여 해당 컨트롤러가 요청을 처리한다.
* 컨트롤러가 비즈니스 로직을 수행한 뒤 처리 결과 Model과 이를 출력할 View를 DispatcherServlet에게 반환한다.
* 해당 정보를 바탕으로 DispatcherServlet은 ViewResolver를 통해 적합한 View 객체를 얻는다.
* View 객체가 사용자에게 보여줄 응답 화면을 출력한다.

<br>

## 4. Spring Layered Architecture

![image](https://user-images.githubusercontent.com/56240505/95432828-1fe26680-098a-11eb-8a90-00f4d3342fc0.png)

Spring Layered Architecture에 대한 레퍼런스를 여러개 참조해본 결과, Layer 구분이 사람들마다 사뭇 다르다. 아마 JPA 사용에 따른 Domain 모델의 중요성을 인식하여 Layer를 조금 더 엄격하고 세밀하게 구분하느냐의 차이인듯 싶다.

![image](https://user-images.githubusercontent.com/56240505/95434101-d4c95300-098b-11eb-9d03-e6d3a586ebaa.png)

Service Layer가 Controller와 커뮤니케이션할 때는 DTO를, Repository와 커뮤니케이션할 때는 Domain Model을 사용한다는 점에서, 아래의 사진이 더 직관적이고 심플하다.

두 사진 모두 Layer를 크게 3가지로 구분하고 Domain Model과 상호작용하는 모습을 묘사했다. 그러나 최근 참고한 레퍼런스들은 Domain Model 또한 Sublayer로 추가하여 Spring Architecture를 4개의 층으로 구분한다.

### 4.1. Presentation Layer

* **Presentation Layer == Web Layer == UI Layer**
  * @Controller 어노테이션을 다는 클래스.

### 4.2. Service Layer

* **Service Layer == Application Layer**
  * @Service 어노테이션을 다는 클래스.
* 일반적으로 Domain 모델의 비즈니스 로직 1개를 호출해서는 복잡한 요청을 처리할 수 없다.
* 다양한 Domain과 비즈니스 로직을 사용하여 응답을 처리하는 Layer가 필요하다.
* 여러 비즈니스 로직을 의미있는 수준으로 묶어 제공하는 역할을 한다.
* 단, 핵심 비즈니스 로직을 직접 구현하지 않고 얇게 유지한다.
  * Service Layer는 트랜잭션 및 Domain 모델의 기능(비즈니스 로직) 간의 순서를 보장해야 한다.

### 4.3. Business Layer

* **Business Layer == Domain Model**
  * (JPA를 사용한다면) @Entity 어노테이션을 다는 클래스.
  * 무조건 DB와 관계가 있어야 하는 것은 아니며, VO처럼 값 객체들도 이 영역이다.
* Data와 그와 관련된 비즈니스 로직(핵심 기능)을 가진 객체이다.

### 4.4. Repository Layer

* **Repository Layer == Persistence Layer**
  * @Repository 어노테이션을 다는 DAO 클래스.
* Data Source 추상화.

<br>

## 5. 결론

그런데 왜 어떤 이들은 Domain 모델까지 Layer로 포함하여 Spring Layered Architecture의 논리적 계층을 4가지로 구분하는 걸까? 개인적으로는 JPA를 많이 사용함에 따라 인식이 변한게 아닐까 추정해본다.

```java
//직관적인 코드
User user = findUser();
Group group = user.getGroup();

//RDB의 문제
User user = userDao.findUser();
Group group = groupDao.findGroup(user.getGroupId());
```

과거 JPA가 아닌 MyBatis 등 SQL Mapper를 자주 사용할 때는, 객체 모델링보다는 테이블 모델링에 집중하고 객체를 단순히 테이블에 맞추어 데이터 전달 역할만 하는 기현상이 발생한다. RDB는 어떻게 데이터를 저장할지에 초점이 맞춰진 반면, OOP 언어는 기능과 속성을 한 곳에서 관리하는 기술이기 때문이다.

위 예제를 보면, User와 Group이 어떤 관계인지 알기 모호하다. 패러다임 불일치로 인해 상속 및 1:N 등 다양한 객체 모델링을 DB로 구현하는데 문제가 발생하기 때문이다.

그 외에도, 반복적인 코드 및 SQL 작업 등도 문제가 있다. 또한 트랜잭션 스크립트는 모든 로직이 서비스 클래스 내부에서 처리되며, 서비스 계층이 무의미해지고 객체가 데이터 덩어리로 전락하는 부작용이 있었다.

요약하자면, JPA를 많이 사용하지 않던 시절에는 Domain 모델이 단순한 데이터 덩어리로 취급되고, 비즈니스 로직이 Service Layer에 위치하는 경우가 많았다. 때문에 당시 작성되었던 Spring Layered Architecture 예시 사진들은 3가지 Layer로 구성되어 있고, Domain 모델은 옆에서 상호작용하는 모습이 많다.

하지만 JPA를 점차 많이 사용하면서 Domain 모델에 비즈니스 로직들이 위치하게 되었고 Service Layer가 얇아졌다. 이런 변화로 인해, Service Layer를 Business Layer라고 부르지 않고 오히려 Domain 모델을 Business Layer로 인식하여 추가한게 아닐까 감히 추정해본다.

<br>

---

## Reference

* [3 Tier Architecture(3계층 구조)](http://blog.naver.com/PostView.nhn?blogId=limoremo&logNo=220073573980)
* [[Spring] Spring MVC와 Dispatcherservlet](https://gangnam-americano.tistory.com/59)
* [[Spring] MVC : Controller와 Service의 책임 나누기](https://umbum.dev/1066)
* [Spring Layered Architecture](https://yoonho-devlog.tistory.com/25)
* 스프링 부트와 AWS로 혼자 구현하는 웹 서비스 (이동욱 저)
