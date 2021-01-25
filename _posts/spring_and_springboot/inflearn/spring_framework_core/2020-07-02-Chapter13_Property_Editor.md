---
title:  "[Spring Framework 핵심 기술] 13장 : PropertyEditor"
excerpt: "Inflearn에 있는 백기선님의 [스프링 프레임워크 핵심 기술] 강의를 듣고 정리한 필기이다."
categories:
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-07-10T08:18:00-05:00
---

## Data Binding

* org.springframework.validation.DataBinder.
* 기술적인 관점 : Property 값을 타겟 객체에 설정하는 기능이다.
* 사용자 관점 : 사용자 입력값을 애플리케이션 도메인 모델에 동적으로 변환해 넣어주는 기능이다.
* 입력값은 대부분 “문자열”인데, 그 값을 객체가 가지고 있는 다양한 도메인 자료형으로 변환해서 넣어주는 기능이다.

<br>

## Property Editor

> EventController.java

```java
@RestController
public class EventController {

  @InitBinder
  public void init(WebDataBinder webDataBinder) {
    webDataBinder.registerCustomEditor(Event.class, new EventEditor());
  }

  @GetMapping("/event/{event}")
  public String getEvent(@Pathvariable Event event) {
    System.out.println(event);
    return event.getId().toString();
  }
}
```

<br>

> EventEditor.java

```java
public class EventEditor extends PropertyEditorSupport {

    @Override
    public String getAsText() {
        Event event = (Event)getValue();
        return event.getId().toString();
    }

    @Override
    public void setAsText(String text) throws IllegalArgumentException {
        setValue(new Event(Integer.parseInt(text)));
    }
}
```

<br>

> EventControllerTest.java

```java
@RunWith(SpringRunner.class)
@WebMvcTest
public class EventControllerTest {

    @Mock
    MockMvc mockMvc;

    @Test
    public void getTest() throws Exception {
        mockMvc.perform(get("/event/1"))
                .andExpect(status().isOk())
                .andExpect(content().string("1"));
    }
}
```

* Spring 3.0 이전까지 DataBinder가 변환 작업 사용하던 인터페이스이다.
* URL로 들어온 매개변수는 String이지만, Event로 적절히 변환해주는 데이터 바인딩 기능이 없으면 Argument Resolve 시점에서 에러가 발생한다.
* 따라서 PropertyEditorSupport를 상속받아 데이터 바인딩을 처리해주는 메소드를 오버라이딩한다.
* getValue 및 setValue 등을 통해 PropertyEditor 내부 상태 정보가 변경된다.
  * Thread-Safe하지 않아 Thread Scope 등 예외 상황이 아니면 Bean으로 등록하지 않고 사용한다.
* Object와 String 간의 변환만 할 수 있어 사용 범위가 제한적이다.

<br>

---

## Reference

*	스프링 프레임워크 핵심 기술(백기선, Inflearn)
