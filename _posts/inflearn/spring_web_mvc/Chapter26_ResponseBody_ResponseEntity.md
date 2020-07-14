---
title: "Spring Web MVC - 26장 : @ResponseBody & ResponseEntity"
date: 2020-07-012 12:30:29
thumbnail: https://user-images.githubusercontent.com/56240505/87219627-c1f8af80-c397-11ea-96bb-83c3f59b7229.png
categories:
- Back-End
- Spring & Spring Boot
tags: [Spring]
---

## @ResponseBody

> EventApi.java

```java
@RestController
@RequestMapping("/api/events")
public class EventApi {

    @PostMapping
    @ResponseBody
    public Event createEvent(@RequestBody @Valid Event event, BindingResult bindingResult) {
        return event;
    }

    @PostMapping
    public ResponseEntity<Event> createEvent(@RequestBody @Valid Event event, BindingResult bindingResult) {
        if (bindingResult.hasErrors()) {
            return ResponseEntity.badRequest().build();
        }
        return ResponseEntity.ok(event);
        // return new ResponseEntity<Event>(event, HttpStatus.CREATED);
    }
}
```

<br>

> SampleControllerTest.java

```java
@Test
public void createEvent() throws Exception {
    Event event = new Event();
    event.setName("jipark");
    event.setId(10);

    String jsonMessage = objectMapper.writeValueAsString(event);

    this.mockMvc.perform(post("/api/events")
        .contentType(MediaType.APPLICATION_JSON_UTF8)
        .content(jsonMessage)
        .accept(MediaType.APPLICATION_JSON))
        .andDo(print())
        .andExpect(status().isOk())
        .andExpect(jsonPath("name").value("jipark"))
        .andExpect(jsonPath("id").value(10));
}
```

* 데이터를 HttpMessageConverter를 사용해 응답 본문 메시지로 보낼 때 사용한다.
* @RestController 사용시 자동으로 모든 핸들러 메소드에 적용 된다.
* ResponseEntity : 응답 헤더 상태 코드 본문을 직접 세밀하게 다루고 싶은 경우에 사용한다.

<br>

---

## Reference

*	스프링 웹 MVC (백기선, Inflearn)
