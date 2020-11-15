---
title: "WebTestClient로 Session 테스트하기"
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
  - WebTestClient
toc: true
toc_sticky: true
last_modified_at: 2020-11-15T14:13:00-05:00
---

## 1. 멘붕

WebTestClient를 처음 사용할 때, 사용법이 Mockito보다 어렵다고 느꼈다. Mockito가 익숙한 탓도 있겠으나, 메소드 이름이 다소 직관적이지 않은 점이 컸다. 그래도 공식 레퍼런스를 참고하면서 Controller 테스트를 진행하는 등 친해지려고(?) 많은 노력을 했고, 많이 친해졌다고 생각했는데...

![image](https://user-images.githubusercontent.com/56240505/99171340-71310300-274b-11eb-82d6-2c025b76fe85.png)

**Session Test** 때문에 다시 어색한 사이가 되버렸다...

Session의 유무에 따른 Controller 테스트를 진행하기 위해, 테스트에 사용하는 WebTestClient Bean에게 Session을 주입해야 했다.

WebTestClient가 ``exchange()``로 테스트 요청을 보내니, 그 이전의 메서드 체이닝에서 Session 관련 설정을 할 수 있을 것이라고 안일하게 생각했다. 메서드에 점을 찍어가며 IntelliJ가 추천하는 메서드 목록을 보았지만, Session이라는 단어가 들어간 메서드는 없었다.

어떻게 WebTestClient에게 Session을 주입(혹은 등록)하지? 구글에 ``How to test session with WebTestClient`` 등 별별 키워드로 검색을 해보았으나, 원하는 자료를 찾을 수 없었다. 그나마 나오는 자료들도 WebFlux와 Mono 등 내가 아직 모르는 내용들이라 멘붕이 시작되었다.

일단 WebFlux 레퍼런스를 뒤져보기 시작했다. ``WebHandler API`` 부분에서 유저 세션 관련 내용을 확인할 수 있었다. ``WebSessionManager`` 라는 Bean이 있는데, ``getSession()`` 같은 메서드가 있는 것으로 보아 세션을 테스트할 때 필요할 것으로 추측했다.

테스트 환경에서의 사용법에 대해 이리저리 연구해보았지만, 결국 알아내는데 실패했다. ㅋㅋ... ㅋㅋㅋ

![image](https://user-images.githubusercontent.com/56240505/99177914-6a0cf380-2751-11eb-8f89-ac72fc38d6be.png)

<br>

## 2. 감사합니다

좌절하던 찰나, 우연히 [[Spring] Spring Interceptor (인터셉터)와 WebTestClient Session (세션) 테스트 적용기](https://pjh3749.tistory.com/257) 라는 글을 구글에서 발견했다.

내가 고민하던 주제에 대한 정답 및 해결 과정을 상세하게 기록해주셔서 정말 많은 도움이 되었다. 너무 잘 쓴 글이다. 혹시 나와 비슷한 어려움을 겪고 있다면 해당 글을 꼭 참조해보길 바란다.

> SessionTest.java

```java
private WebTestClient sessionWebTestClient = WebTestClient.bindToWebHandler(exchange -> {
    return exchange.getSession()
            .doOnNext(webSession -> {
                webSession.getAttributes()
                        .put("loginDto", new LoginDto());
            }).then();
}).build();
```

모든 Path에 대해 Session을 주입하는 경우 위와 같은 형태로 WebTestClient를 설정해주었다.

> SessionTest2.java

```java
private WebTestClient sessionWebTestClient = WebTestClient.bindToWebHandler(exchange -> {
    String path = exchange.getRequest().getURI().getPath();
    if (path.equals("/login") || path.equals("/logout")) {
        return exchange.getSession()
                .doOnNext(webSession -> {
                    webSession.getAttributes()
                            .put("loginDto", new LoginDto());
                }).then();
    }
    return null;
}).build();
```

특정 Path에 대해서만 Session을 주입하는 경우 위처럼 Path에 대한 조건을 걸어주었다.

<br>

## 3. 느낀점

WebTestClient로 Session을 테스트하는 방법은 의외로 간단했다. 다만 해당 방법을 찾기 위한 과정에서 내가 많이 부족하다고 느꼈다.

나는 구글링이나 레퍼런스를 뒤지다 보면 사용 방법이 나올 것이라는 안일한 생각이었다. 실제로 Spring이나 Mockito는 관련 자료들이 많아서 그런 방식이 잘 먹혔지만, WebTestClient처럼 관련 자료가 없는 경우 어려움이 많았다. 원하는 자료를 찾지 못하니 자포자기 심정이 들기도 했고.

반면 내가 참조한 포스팅의 저자는 WebTestClient에 Session을 등록하기 위해 다음과 같은 단계를 밟았다.

* 먼저 WebTestClient 클래스 코드에서, 주석을 참고하며 적합한 메서드를 탐색한다.
  * 주어진 WebHandler 인자를 이용해 Mock 서버를 통합 테스트할 수 있는 ``bindToWebHandler()`` 메서드를 찾았다.
* ``bindToWebHandler()``의 인자인 WebHandler 인터페이스 코드로 이동한다.
  * 웹 서버 실행을 관리하는 메서드 ``handle()``을 찾았다.
* ``handle()`` 메서드의 인자인 ServerWebExchange 인터페이스 코드로 이동한다.
  * 주석을 통해, ServerWebExchange 인터페이스가 HTTP 요청-응답 및 추가적인 서버사이드 프로세스를 관리하는 것을 확인했다.
  * 이후 현재 요청의 세션을 가져오는 ``getSession()`` 메서드를 찾았다.
* WebTestClient에 ``bindToWebHandler()``를 통해 WebHandler를 등록하고, 해당 WebHandler는 Session을 관리하는 ServerWebExchange를 구현해 넣어주면 Session 주입이 가능하다는 결론을 도출했다.

코드를 직접 뜯어 보면서 공부하는 것이 중요하다는 것을 알고 있지만, 편한 방법을 쫓으려는 습관의 관성 때문에 어느 순간부터는 기본을 잊고 구글링에만 의존하고 있었다.

내게 지금 중요한 것은 문제 해결 속도가 아니라, 스스로 올바르게 문제를 해결하는 능력이다. 문제가 주어졌을 때 빠른 해결에 집착하기 보다는, 기본에 충실하며 문제해결능력을 기를 수 있도록 의식적으로 연습해야겠다.

<br>

---

## Reference

* [[Spring] Spring Interceptor (인터셉터)와 WebTestClient Session (세션) 테스트 적용기](https://pjh3749.tistory.com/257)
* [[번역] 스프링 웹플럭스 레퍼런스 (Web on Reactive Stack)](https://parkcheolu.tistory.com/134#webflux-web-handler-api)
