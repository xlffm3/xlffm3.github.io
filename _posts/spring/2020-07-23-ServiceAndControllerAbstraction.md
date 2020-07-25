---
title: "Spring Controller 및 Service 추상화"
categories:
  - Back-End
  - Spring & Spring Boot
tags:
  - Spring & Spring Boot
toc: true
toc_sticky: true
last_modified_at: 2020-07-23T10:55:00-05:00
---

## 1. CrudInterface 정의

> CrudInterface.java

```java
public interface CrudInterface<Request, Response> {

    ResponseEntity<Response> create(HttpEntity<Request> request);
    ResponseEntity<Response> read(Long id);
    ResponseEntity<Response> update(HttpEntity<Request> request);
    ResponseEntity delete(Long id);
}
```

Controller를 추가하게 되면 CRUD 등 기본 기능들을 재정의하는 코드 중복이 발생한다. CRUD 기능을 Interface로 분리하며, Generics로 Request와 Response를 추가한다.

HttpEntity와 ResponseEntity 또한 특정 T 타입의 data를 담을 수 있도록 Generics가 존재한다. 특정 타입을 받고 반환하는 것 보다 Generics를 사용하면 더욱 유연하게 다양한 타입의 데이터를 담을 수 있다.

<br>

## 2. BaseService 정의

> BaseService.java

```java
@Component
public abstract class BaseService<Request,Response,Entity> implements CrudInterface<Request,Response> {

    @Autowired(required = false)
    protected JpaRepository<Entity,Long> baseRepository;
}
```

기존에 정의한 CrudInterface를 상속받는 BaseService 추상 클래스를 정의한다. Service가 사용하는 Repository들은 일반적으로 JpaRepository를 상속받아 사용하기 때문에, baseRepository 또한 내부에 정의해준다. Long 타입의 Id에 맵핑되는 Domain 객체 자료형은 Entity Generics로 명시해준다.

<br>

## 3. 세부 Service 구현

> OrderGroupApiLogicService.java

```java
@Service
public class OrderGroupApiLogicService extends BaseService<OrderGroupApiRequest,OrderGroupApiResponse,OrderGroup> {

    @Autowired
    private UserRepository userRepository;

    @Override
    public ResponseEntity<OrderGroupApiResponse> create(HttpEntity<OrderGroupApiRequest> request) {
        return null;
    }

    @Override
    public ResponseEntity<OrderGroupApiResponse> read(Long id) {
        return null;
    }

    @Override
    public ResponseEntity<OrderGroupApiResponse> update(HttpEntity<OrderGroupApiRequest> request) {
        return null;
    }

    @Override
    public ResponseEntity delete(Long id) {
        return null;
    }
}
```

예제를 위해 Service 단에서도 HttpEntity 및 ResponseEntity를 사용하게 했으나, 실제로 구현할 때는 ResponseDto를 반환하도록 한다.

기존의 CrudInterface를 구현하는 BaseService를 상속받아 Service를 정의한다. 기능에 필요한 Response와 Request 및 Entity 등의 타입을 정의한 뒤 명시해준다. CRUD 기능을 각 Service에 적절하게 강제 구현하면서, 각각의 서비스마다 별도로 필요하는 서비스가 있다면 커스텀하게 추가해준다.

<br>

## 4. CrudController 정의

> CrudController.java

```java
@Component
public abstract class CrudController<Request,Response,Entity> implements CrudInterface<Request,Response> {

    @Autowired(required = false)
    protected BaseService<Request,Response,Entity> baseService;

    @Override
    @PostMapping("")
    public ResponseEntity<Response> create(@RequestBody HttpEntity<Request> request) {
        return baseService.create(request);
    }

    @Override
    @GetMapping("{id}")
    public ResponseEntity<Response> read(@PathVariable Long id) {
        return baseService.read(id);
    }

    @Override
    @PutMapping("")
    public ResponseEntity<Response> update(@RequestBody HttpEntity<Request> request) {
        return baseService.update(request);
    }

    @Override
    @DeleteMapping("{id}")
    public ResponseEntity<Response> delete(@PathVariable Long id) {
        return baseService.delete(id);
    }
}
```

컨트롤러가 사용하는 BaseService 또한 @Component로 등록되어 있어서 의존성을 주입받을 수 있다. CrudInterface가 명시한 CRUD 기능들을 간단하게 구현해준다.

<br>

## 5. 세부 Controller 구현

> OrderGroupApiController.java

```java
@RestController
@RequestMapping("/api/orderGroup")
public class OrderGroupApiController extends CrudController<OrderGroupApiRequest, OrderGroupApiResponse, OrderGroup> {
}
```

컨트롤러는 Generics 타입을 명시한 CrudController 추상 클래스를 상속받고, URI 맵핑에 필요한 세부 주소를 추가해준다. Generics에 정의된 타입에 따라 적합한 Service와 Repository Bean을 탐색하여 주입하고, 적합한 Response 및 RequestDto를 사용하게 된다.

> UserApiController.java

```java
@RestController
@RequestMapping("/api/user")
public class UserApiController implements CrudInterface<UserApiRequest, UserApiResponse> {

    @Autowired
    private UserApiLogicService userApiLogicService;

    //CRUD 기능 Override 및 별도의 기능 로직 구현...
}
```

필요하다면 CRUD를 오버라이딩하여 각 컨트롤러의 특성에 맞게 커스텀할 수 있다. 또한 CrudController가 아닌 CrudInterface를 직접 구현하면, CrudInterface를 상속받는 BaseService가 아닌 구체적인 Service 객체를 위의 예제처럼 사용할 수 있다. 이 경우, CRUD 뿐만 아니라 특정 Service 객체에 있는 기능을 통해 커스텀한 컨트롤러 맵핑을 구현할 수 있게 된다.

<br>

## 6. 마치며

이처럼 Generics와 Interface를 통해 컨트롤러와 서비스를 분리한다면, 번거롭게 CRUD 기능을 일일히 중복 작성할 필요가 없다. 서비스 레이어와 이에 필요한 DTO 및 Bean을 정의한 뒤, 추상 클래스를 상속받으며 Generics를 명시하면 될 뿐이다.

CRUD 외에도 여러 컨트롤러에 걸쳐서 자주 사용하는 중복 맵핑 기능들이 있다면, 이를 인터페이스와 Generics로 분리하여 코드의 중복을 줄일 수 있다.

<br>

---

## Reference

* [Java 웹 개발 마스터 올인원 패키지 Online](https://www.fastcampus.co.kr/dev_online_jvweb)
