---
title: "Neither BindingResult nor plain target object for bean name available as request attribute"
excerpt: "객체가 없다면 객체를 실어서 보내자."
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
  - Error
toc: true
toc_sticky: true
last_modified_at: 2020-09-12T14:13:00-05:00
---

## 1. java.lang.IllegalStateException

> signup.html

```html
<form method="post" th:action="@{/users}" th:object="${userDto}">
    <div class="form-group">
        <label for="name">Name</label>
        <input id="name" name="name" th:field="*{name}" type="text">
        <small class="text-danger" th:errors="*{name}" th:if="${#fields.hasErrors('name')}"></small>
    </div>
    <div class="form-group">
        <label for="password">Password</label>
        <input  id="password" name="password" th:field="*{password}" type="text">
        <small class="text-danger" th:errors="*{password}" th:if="${#fields.hasErrors('password')}"></small>
    </div>
...
</form>
```

이름 및 패스워드 등 Form Data를 POST 요청을 보내 회원가입을 진행하는 페이지이다. @Valid 유효성 검증을 실패하는 경우, BindException이 발생하고 ``th:if``가 들어간 태그에서 에러 메시지를 출력해준다.

여기까지는 테스트했을 때 별다른 문제가 없었다.

> ControllerExceptionHandler.java

```java

@ExceptionHandler(DuplicatedUserEmailException.class)
public ModelAndView handleDuplicatedUserEmailException(DuplicatedUserEmailException duplicatedUserEmailException) {
    ModelAndView modelAndView = new ModelAndView();
    modelAndView.addObject("error", true);
    modelAndView.addObject("message", duplicatedUserEmailException.getMessage());
    modelAndView.setViewName("signup");
    return modelAndView;
}
```

> signup.html

```html
<div class="alert alert-danger" id="email-duplication" role="alert" th:if="${error}" th:text="${message}"></div>
```

이메일이 중복이라면 modelAndView에 error와 message를 담아 리턴하고, View에서 에러 메시지를 출력해주는 코드를 추가해주었다.

이메일 중복 테스트를 진행하니 에러 메시지를 출력하지 못하고, 오히려 500 에러를 띄우는 문제가 발생했다.

<br>

## 2. 원인

> java.lang.IllegalStateException: Neither BindingResult nor plain target object for bean name 'userDto' available as request attribute.

요청 필드 속성들을 바인딩할 Bean 모델 객체 userDto가 없다는 에러 메세지가 출력되었다. 기존에는 데이터 바인딩이 잘 동작했기 때문에, 컨트롤러의 @ModelAttribute 문제는 아닌 것으로 판단했다.

회원가입 페이지의 코드를 자세히 살펴보니 다음과 같은 문제점이 있었다.

> signup.html

```html
<form method="post" th:action="@{/users}" th:object="${userDto}">
...
<input id="name" name="name" th:field="*{name}" type="text">
<small class="text-danger" th:errors="*{name}" th:if="${#fields.hasErrors('name')}"></small>
...
<input  id="password" name="password" th:field="*{password}" type="text">
<small class="text-danger" th:errors="*{password}" th:if="${#fields.hasErrors('password')}"></small>
```

``th:object``를 통해 데이터를 userDto라는 객체로 바인딩하고 있으며, ``*`` 표현식을 통해 객체의 필드를 참조 중이었다.

이메일 중복 예외가 발생하면, @ExceptionHandler에서 ModelAndView 객체에 에러 메시지와 View Name을 담아 리턴한다. 페이지가 리로드될 때, 데이터를 바인딩하고 필드를 참조할 userDto 객체가 없어서 생기는 문제로 추정되었다.

<br>

## 3. 해결 방법

> ControllerExceptionHandler.java

```java
modelAndView.addObject("userDto", new UserDto());
```

해당 예외를 처리하는 @ExceptionHandler에서, 필요한 모델 객체를 만들어 ModelAndView 객체에 전달하면 해결된다.

<br>

---

## Reference

* [Neither BindingResult nor plain target object for bean name available as request attribute [duplicate]](https://stackoverflow.com/questions/8781558/neither-bindingresult-nor-plain-target-object-for-bean-name-available-as-request)
