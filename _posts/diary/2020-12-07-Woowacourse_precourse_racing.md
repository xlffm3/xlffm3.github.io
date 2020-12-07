---
title: "[우아한테크코스] 프리코스 2주차 미션 학습 내용 및 회고"
categories:
  - Java
  - ETC
tags:
  - Java
  - ETC
toc: true
toc_sticky: true
last_modified_at: 2020-12-08T08:30:00-05:00
---

## 1. 자동차 경주 게임

우아한테크코스의 프리코스 2주차 미션은 자동차 경주 게임 구현이다. 구현 내용은 [프리코스 2주차 미션 저장소](https://github.com/xlffm3/java-racingcar-precourse/tree/xlffm3)에 업로드했다.

1주차 미션에 대한 피드백 및 2주차 미션을 진행하면서 고민한 내용들에 대해 개인적으로 공부하고 정리해보았다.

<br>

## 2. 1주차 미션 피드백

### 2.1. 이름을 통해 의도를 드러내라

> 이름을 통해 변수의 역할, 함수의 역할, 클래스의 역할에 대한 의도를 드러내기 위해 노력하라.

올바른 네이밍은 코드의 가독성을 높인다. 1주차 과제 코드를 다시 살펴보니, 사용자의 입력값을 받는 InputView 클래스에서 **input**이라는 단어를 동사 및 명사 형태로 혼용하고 있었다.

> InputView.java

```java
public List<Integer> inputBaseballNumbers() {
    System.out.print(INPUT_BASEBALL_NUMBERS_MESSAGE);
    String inputBaseballNumbers = scanner.nextLine();
    //중략...
}
```

클래스, 메서드, 변수 이름들이 각각 input을 사용하면서 의도는 명확하게 드러내고 있다. 다만 동사 및 명사 형태를 한 곳에서 혼용하다 보니, Java 네이밍 컨벤션을 어기는 것 처럼 보인다. 메서드에서 input을 동사로 사용하면서 의미를 잘 드러내고 있으니, 변수 이름까지 input을 붙이는 것은 redundant하다고 느끼고 이를 삭제했다.

### 2.2 불필요하게 공백 라인을 만들지 않는다.

> 공백 라인을 뛰우는 것도 코드상에 문맥이 달라지는 부분에 의도를 가지고 뛰우면 좋겠다.

나는 공백 라인을 잘 이용하지 않는 스타일이다. 다만, 해당 피드백 내용을 보고 테스트 코드에서는 공백 라인을 활용하여 의도를 드러내고 가독성을 높일 필요성을 느꼈다.

> RacingGameTest.java

```java
@Test
void race_시도_횟수가_1만큼_차감되고_게임은_종료된다() {
    Cars cars = Cars.createCars(carNames, new RandomMovingStrategy());
    GameState gameState = GameState.initiate(1);
    RacingGame racingGame = new RacingGame(cars, gameState);
    racingGame.race();
    boolean isEnd = racingGame.isEnd();
    assertThat(isEnd).isTrue();
}
```

테스트 코드가 짧아서 읽는데 어려움은 없지만, 분량이 많을 때 공백 라인이 없다면 코드를 읽는데 확실히 애로사항이 생길 것이다.

> RacingGameTest.java

```java
@Test
void race_시도_횟수가_1만큼_차감되고_게임은_종료된다() {
    //given
    Cars cars = Cars.createCars(carNames, new RandomMovingStrategy());
    GameState gameState = GameState.initiate(1);
    RacingGame racingGame = new RacingGame(cars, gameState);
    racingGame.race();

    //when
    boolean isEnd = racingGame.isEnd();

    //then
    assertThat(isEnd).isTrue();
}
```

주석을 달지 않더라도, Given-When-Then으로 구분되는 파트를 공백으로 나누어 테스트 코드를 작성하면 그 의도를 분명하게 드러내어 가독성이 높아진다.

<br>

## 3. 클린 코드를 위해

### 3.1. do-while을 사용할까?

> Application.java

```java
private static void runBaseballGame(InputView inputView) {
    BaseballGameMachine baseballGameMachine = new BaseballGameMachine();
    GameState gameState = GameState.initiate();
    while (gameState.isAbleToPlay()) {
        baseballGameMachine.play();
        gameState = GameState.findGameState(inputView.inputGameState(gameResult));
    }
}
```

미션 내용을 살펴보면, while보다는 do-while을 쓰는 것이 편할 것 같은 부분이 종종 있다. 예를 들어, 숫자 야구 게임은 1회차에는 무조건 실행되는 규칙을 가지고 있다. 즉, 1회차 플레이할 때는 gameState 변수의 상태를 확인할 필요가 없다. 그러나 while을 쓰면 1회차 플레이도 gameState 조건을 검사하기 때문에, while 이전에 gameState 변수를 초기화하는 ``GameState.initiate()`` 메서드가 필요하다.

> Application.java

```java
private static void runBaseballGame(InputView inputView) {
    BaseballGameMachine baseballGameMachine = new BaseballGameMachine();
    GameState gameState;
    do {
      baseballGameMachine.play();
      GameState.findGameState(inputView.inputGameState(gameResult));
    } while (gameState.isAbleToPlay())
}
```

만약 do-while을 썼다면 해당 메서드는 불필요할 것이다. 이런 부분을 보고, do-while이 프로그램 로직에 잘 부합하다고 생각하여 사용을 고려했다.

그러나 클린 코드와 관련된 글들을 검색해보면서, do-while은 삼항연산자 및 goto 구문과 같이 가독성을 떨어뜨리기 때문에 되도록 지양할 것을 권고하고 있다.

확실히 do-while은 코드를 아래에서 위로 역순으로 읽어야 하는 부자연스러운 흐름이 존재하기 때문에, 조건을 미리 확인할 수 있는 while을 사용하는 것이 낫다고 생각한다.

### 3.2. Custom Exception

Domain에서 발생하는 예외에 대해 항상 고민을 많이 한다. 사용자 정의 예외를 사용할까? 아니면 이미 있는 표준 예외를 사용할까?

[custom exception을 언제 써야 할까?](https://woowacourse.github.io/javable/2020-08-17/custom-exception) 라는 글에서 두 예외의 장단점을 학습할 수 있었다.

정답은 없다. 다만 비용, 클래스 관리 복잡도, 예외 처리 편의성 등 Trade-Off를 고려하여 상황에 맞게 잘 선택해야 한다.

2주차 미션은 1주차 미션보다 예외 상황이 많았고, 그만큼 사용자 정의 예외 클래스를 많이 생성하게 되었다. 관리 측면에서 확실히 표준 예외보다 불편한 점이 있으니, 상황을 고려하면서 표준 예외와 함께 적절하게 사용해야겠다.

### 3.3. Application 클래스 구현

프로그래밍 요구사항 중에 "Application의 구현은 변경하지 않는다."는 내용이 있다. main 메서드밖에 없는 클래스인데, 나는 1주차 과제 때 메인 메서드의 코드가 길어져서 해당 클래스에 private 메소드를 추가해서 사용했다.

그 때는 이게 구현을 변경하지 않는 것이라고 생각했다. 그러나 돌이켜보니 메인 메서드 외에 메서드를 추가한 것 자체가 구현을 변경한 것이라는 생각이 들었다. 확실하진 않지만 찜찜하다. :(:(:(

생각해보니 App 클래스 자체는 private 메서드를 가지면 역할이 애매해지는 것 같다. 별개로 실행의 역할만 해야할 것 같다. 그렇다면 main 메서드를 어떻게 사용해야 할까?

> 소프트웨어 시스템은 애플리케이션 객체를 제작하고 의존성을 서로 연결하는 준비과정과 준비과정 이후에 이어지는 런타임 로직을 분리해야 한다.<br>
Clean Code - Robert C. Martin

시스템 제작과 사용을 분리를 의미한다. 생성과 관련된 코드를 main이나 main이 호출하는 모듈로 옮기고, 나머지 시스템은 모든 객체가 생성되었고 모든 의존성이 연결되었다고 가정한다. 즉 main 메서드는 다음의 작업을 수행한다.

* 어플리케이션 영역에서 사용될 객체를 생성한다.
* 각 객체 간의 의존 관계를 설정한다.
* 어플리케이션을 실행한다.

해당 내용이 어려워서 정확하게 이해하지는 못했는데, 일단 이에 입각하여 자동차 경주 게임 코드를 작성해보았다.

> Application.java

```java
public class Application {
    public static void main(String[] args) {
        final Scanner scanner = new Scanner(System.in);
        InputView inputView = new InputView(scanner);
        List<String> carNames = inputView.inputCarNames();
        int racingTryCounts = inputView.inputRacingTryCounts();
        MovingStrategy movingStrategy = new RandomMovingStrategy();
        Cars racingCars = Cars.createCars(carNames, movingStrategy);
        GameState gameState = GameState.initiate(racingTryCounts);
        RacingGame racingGame = new RacingGame(racingCars, gameState);
        RacingGameController racingGameController = new RacingGameController(racingGame);
        racingGameController.run();
        scanner.close();
    }
}
```

> RacingGameController.java

```java

public class RacingGameController {

    private final RacingGame racingGame;

    public RacingGameController(RacingGame racingGame) {
        this.racingGame = racingGame;
    }

    public void run() {
        OutputView.printGameExecutionResultHeader();
        while (!racingGame.isEnd()) {
            racingGame.race();
            List<CarDto> racingCarDtos = racingGame.getCarDtos();
            OutputView.printRacingTryResult(racingCarDtos);
        }
        List<String> winnerCarNames = racingGame.getWinnerCarNames();
        OutputView.printWinnerCarNames(winnerCarNames);
    }
}
```

RacingGameController 객체를 생성하는데 필요한 객체들을 main 메서드에서 전부 생성해준다. 이후 RacingGameController의 ``run()`` 메소드를 호출하여 게임을 진행하며 결과를 출력한다.

이렇게 코드를 짜면서 "이게 맞는건지" 의문이 들었다.

* InputView 객체를 통해 사용자로부터 값을 입력받는 행위는 어플리케이션에 사용될 객체를 생성하고 의존성을 연결하는 **준비 과정**인가?
* 아니면 **런타임 로직**인가?

만약 사용자로부터 값을 입력받는 행위가 준비 과정이라면 현재의 코드가 맞을 것이다. 자동차 경주 게임이라는 런타임 로직이 실행되기 위해 필요한 객체들을 생성하고 의존성을 주입하는 작업은 main에 위치하면 된다.

하지만 값을 입력받는 행위가 런타임 로직이라면, 위 코드의 main에 위치한 작업들이 아래와 같이 어플리케이션 실행 로직 안으로 이동해야 한다.

> Application.java

```java
public class Application {
    public static void main(String[] args) {
        final Scanner scanner = new Scanner(System.in);
        InputView inputView = new InputView(scanner);
        RacingGameController racingGameController = new RacingGameController(inputView);
        racingGameController.run();
        scanner.close();
    }
}
```

> RacingGameController.java

```java
public class RacingGameController {

    //중략

    public void run() {
        List<String> carNames = this.inputView.inputCarNames();
        int racingTryCounts = this.inputView.inputRacingTryCounts();
        MovingStrategy movingStrategy = new RandomMovingStrategy();
        Cars racingCars = Cars.createCars(carNames, movingStrategy);
        GameState gameState = GameState.initiate(racingTryCounts);
        RacingGame racingGame = new RacingGame(racingCars, gameState);
        startRacingGame(racingGame);
    }

    private void startRacingGame(RacingGame racingGame) {
        //게임 실행 및 결과 출력 로직
    }
}
```

무엇이 나은 선택일지 잘 모르겠다. 책 내용도 은근히 어렵고, 콘솔 게임의 준비 과정 및 런타임 로직의 경계도 애매하다.

오랜 시간의 고민 끝에, InputView로부터 사용자의 값을 입력받는 행위는 **준비 과정**이 아닌 **런타임 로직**으로 보는 것이 자연스럽다는 결론을 내렸다. 입력값에 따라 로직 진행 혹은 에러 메시지 출력 등 지속적으로 프로그램과 유저가 상호작용을 하기 때문에...? 이에 따라 후자의 코드를 채택하게 되었다.

확실하게 아는 내용이 아니다보니, 지속적으로 관심을 가지면서 공부해야겠다.

<br>

## 4. 코드를 까보는 습관 : String

n대의 자동차 이름 입력값을 쉼표를 기준으로 분리하고, 각각의 이름에 대해 유효성 검사를 진행하는 로직이 있다.

split을 통해 얻은 이름 String 배열에 대해 유효성 검사를 진행하는데, 일부 예외 케이스들은 쉼표 패턴이 잘못 되었음에도 split이 정상적인 형태의 배열을 리턴하기 때문에 예외 검사를 통과하고 있었다.

* "," -> 크기가 0인 배열 []이 반환.
* "1,2,3," -> ["1", "2"]가 반환.
* "1,2,,,,," -> ["1", "2"]가 반환.
* "1,2,,,,,3" -> ["1", "2", "", "", "", "3"]가 반환.
* ",1,2,3" -> ["", "1", "2", "3"]가 반환.

split을 통해 문자열을 분리할 때, 구분자의 앞 뒤 빈 문자열을 인식하고 이를 함께 분리해 리턴할 때가 있고 아닐 때가 있었다.

고민하다가 그냥 정규식 패턴을 사용해서 비교하는 것이 빠르겠다는 생각이 들었다.

> InputView.java

```java
public class InputView {
    private static final String REGULAR_EXPRESSION = "^([^,])(,[^,])*$";

    //중략

    public String[] inputCarNames() {
        String inputCarNames = this.scanner.nextLine();
        if (inputCarNames.matches(REGULAR_EXPRESSION)) {
          return inputCarNames.split(",");
        }
        return inputCarNames(); //패턴이 불일치면 재입력
    }
}
```

n대의 자동차 이름 입력값의 형태가 정해져있기 때문에, 간단하게 정규식 ``^([^,])(,[^,])*$`` 을 짤 수 있었다. String의 ``matches()`` 메서드 및 정규식을 활용하면 쉼표 앞 뒤로 정상적인 이름이 없는 예외 케이스(즉, 쉼표가 연속된 경우)를 간단하게 검사할 수 있었다.

그런데 과거 Pattern 및 Matcher 클래스를 통해서도 정규식을 검사했던 기억이 났다. 정리해보니 정규식 검사를 총 세 가지 방식으로 진행할 수 있었다.

> RegularExpressionTest.java

```java
String inputCarNames = this.scanner.nextLine();
Pattern pattern = Pattern.compile("^([^,])(,[^,])*$");
Matcher matcher = pattern.matcher(inputCarNames);

boolean isMatch = inputCarNames.matches("^([^,])(,[^,])*$");
boolean isMatch2 = Pattern.matches("^([^,])(,[^,])*$", inputCarNames);
boolean isMatch3 = matcher.matches();
```

1. String 객체의 ``matches()`` 메서드.
2. Pattern 클래스의 static ``matches()`` 메서드.
3. Pattern 객체를 컴파일 한 뒤 생성한 Matcher 객체의 ``matches()`` 메서드.

이 세 가지 방식에는 어떤 차이가 있을까? 갑자기 궁금해져서 코드를 까보게되었다.

> String.java

![image](https://user-images.githubusercontent.com/56240505/101377557-51799e80-38f5-11eb-9968-99eef24d6c73.png)

먼저 String의 ``matches()`` 메서드는 내부적으로 ``Pattern.matches()``를 호출한다.

> Pattern.java

![image](https://user-images.githubusercontent.com/56240505/101376748-5722b480-38f4-11eb-91f8-18131d2de540.png)

Pattern의 static ``matches()`` 메서드는 내부적으로 Pattern 객체를 컴파일하고 Matcher 객체를 생성한다. 이후 Matcher의 ``matches()``를 통해 정규식과 input 값을 비교한다.

결론은 String 및 Pattern 클래스 모두 내부적으로 Pattern 객체를 컴파일하고 Matcher 객체를 생성한 다음 정규식을 검사하고 있었다.

```java
public String[] inputCarNames() {
    String inputCarNames = this.scanner.nextLine();
    if (inputCarNames.matches(REGULAR_EXPRESSION)) {
      return inputCarNames.split(",");
    }
    return inputCarNames(); //패턴이 불일치면 재입력
}
```

문제는 ``Pattern.compile()`` 메서드인데, Pattern 객체의 생성 비용이 비싸다고 한다. String의 ``matches()``를 사용하고 있는 나의 코드는, 정규식과 input을 비교할 때 마다 값비싼 Pattern 객체를 계속 생성하고 있는 셈.

따라서 효율적인 정규식 검사를 위해 코드를 다음과 같이 개선했다.

> InputView.java

```java
public class InputView {
    private static final Pattern PATTERN = Pattern.compile("^([^,])(,[^,])*$");

    //중략

    public String[] inputCarNames() {
        String inputCarNames = this.scanner.nextLine();
        Matcher matcher = PATTERN.matcher(inputCarNames);
        if (matcher.matches()) {
          return inputCarNames.split(",");
        }
        return inputCarNames(); //패턴이 불일치면 재입력
    }
}
```

객체 생성 비용이 비싼 Pattern 객체는 정규식 패턴도 한 가지로 고정되어 있으니 전역변수로 캐싱해둔다. 이 후, 메서드 내부에서 Matcher 객체를 생성하고 이를 통해 input 값의 정규식 검사를 진행한다.

이렇게 코드를 까다보니, "문제가 됬던 split 메서드도 까보면 단서가 있지 않을까?" 하는 생각이 들었다.

> String.java

![image](https://user-images.githubusercontent.com/56240505/101381166-c5b64100-38f9-11eb-8fad-3aedf5eb2a0f.png)
![image](https://user-images.githubusercontent.com/56240505/101379950-3fe5c600-38f8-11eb-8ee4-100e8c9d3e54.png)

아니나 다를까... 인자로 limit라는 int 값을 받는 오버라이드 메서드가 하나 있었다. :(

* limit가 0이면 일반적인 ``split()`` 메서드와 동일하게 동작한다.
  * "1,2,,3,,,,," -> {"1", "2", "", "3"}
  * 배열 중간 중간에 껴있는 빈 문자열은 토큰으로 인식한다.
  * 마지막이 빈 문자열인 것들은 모두 무시하고 반환하지 않는다.
* limit가 양수면 모든 Zero-Length String을 포함하지만 배열의 최대 갯수는 limit을 넘지 못한다.
  * limit이 3일 때, "1,2,,,7,," -> {"1", "2", ""}
* limit이 음수면 모든 Zero-Length String을 포함한다.
  * 찾는 패턴이 가능한 많이 적용된다.
  * "1,2,,,7,," -> {"1", "2", "", "", "7", "", ""}

> InputView.java

```java
String[] carNames = this.scanner.nextLine().split(",", -1);
```

허무하게도 이렇게 코드를 변경해주니, 잘못된 쉼표 패턴을 가진 입력값도 제대로 분리해서 모든 예외를 꼼꼼하게 처리할 수 있었다.

결론 : 막힐 때는 코드를 까보자. 많은 것을 배울 수 있다. 또한 빨리 까볼수록 시간이 절약된다!

<br>

## 5. 팩토리 메서드 패턴

정적 팩토리 메서드를 사용하면서 문득 ["팩토리 메서드 패턴"](https://gmlwjd9405.github.io/2018/08/07/factory-method-pattern.html)이 궁금해졌다.

팩토리 메서드 패턴이란 객체를 생성하기 위해 인터페이스를 정의하는데, 어떤 클래스의 인스턴스를 만들지는 서브클래스에서 결정하도록 한다. 즉, 팩토리 메소드 패턴을 이용하면 클래스의 인스턴스 만드는 일을 서브클래스에게 맡길 수 있다.

만약 자동차 경주 게임의 요구사항이 다양한 종류(스포츠카, SUV, 세단 등)의 자동차를 사용한다면, 다음과 같이 추상화를 할 수 있을 것이다.

* Car 인터페이스
  * RacingCar 클래스
  * SUV 클래스
  * ...
* Factory 인터페이스
  * HyundaiFactory
  * KiaFactory
  * ...

**new** 키워드는 추상화(Abstract)와 반대되는 구상(Concrete) 클래스의 인스턴스 제작을 의미한다. 구상 클래스를 바탕으로 코딩을 하면 새로운 구상 클래스가 추가될 때 마다 코드를 수정해야 할 가능성이 높아진다. 객체간의 강결합이 발생하면, 변화에 대해 닫혀 있는 코드가 되며 이는 유연성이 떨어진다.

인터페이스 및 다형성을 사용하여 코딩하면, 시스템의 변화에 대해 유연하게 대처할 수 있다. 특정 인터페이스만 구현하면 되기 때문이다.

팩토리 메서드 패턴이란 바뀔 수 있는 부분을 찾아내서 바뀌지 않는 부분과 분리하는 것이 원칙이다.

> Cars.java

```java
public class Cars {

    private final List<Car> cars;

    //중략

    public static Cars createCars(List<String> carNames, MovingStrategy movingStrategy) {
        validateDuplication(carNames);
        List<Car> cars = carNames.stream()
                .map(carName -> new Car(carName, movingStrategy))
                .collect(Collectors.toList());
        return new Cars(cars);
    }
}
```

Cars 객체는 List\<Car\>를 가지고 있는 일급 컬렉션 객체이며, 현재 정적 팩토리 메서드를 통해 객체를 생성한다. 이를 팩토리 메서드로 개선한다면?

* Car는 클래스가 아닌 인터페이스로 변경한다.
* CarFactory 인터페이스를 생성하고, 이를 통해 Cars를 생성하게 한다.

> CarFactory.java

```java
public interface CarFactory {

    public Cars createCars(List<String> name);
}
```

> RacingCarFactory.java

```java
public class RacingCarFactory implements CarFactory {

    @Override
    public Cars createCars(List<String> name) {
        List<Car> cars = name.stream()
                .map(RacingCar::new, new RandomMovingStrategy())
                .collect(Collectors.toList());
        return new Cars(cars);
    }
}
```

현재 과제의 요구사항에서는 정적 팩토리 메서드로 충분하지만, 요구사항이 확대된다면 팩토리 메서드 패턴을 고려해볼만 하다.

하지만 객체의 생성을 서브 클래스에게 위임하는 것은 단점도 분명하리라 생각된다. 객체를 생성하는데 필요한 정보들(파라미터, 내부 비즈니스 로직)이 Factory 등 외부로 분산 및 노출되기 때문이다. 상황에 맞게 잘 쓰자.

<br>

## 6. 객체의 책임과 역할 : MovingStrategy

Car 객체를 구현할 때, 두 가지 방식 사이에서 고민을 했다.

> Car.java

```java
public class Car {

    private final String name;
    private final MovingStrategy movingStrategy;
    private int position = 0;

    //중략

    public void move() {
        if (this.movingStrategy.isMovable()) {
            this.position++;
        }
    }
}
```

> Car.java

```java
public class Car {

    private final String name;
    private int position = 0;

    //중략

    public void move(MovingStrategy movingStrategy) {
        if (movingStrategy.isMovable()) {
            this.position++;
        }
    }
}
```

큰 차이는 없다. 전자는 인스턴스 변수로 이동 전략 인터페이스를 주입받으며, 후자는 메서드 파라미터로 주입받는다.

is-a 및 has-a 관계를 생각해보니, Car 객체가 인스턴스 변수로 이동 전략 인터페이스를 가지고 있는 것이 더 자연스럽다는 생각이 들었다. Car 객체가 이동할 때 마다 이동 규칙을 외부에서 파라미터로 주입받는 것은 다소 부자연스럽다. Car 객체가 생성될 때 이동 규칙을 위임하여, 객체 스스로가 움직일지 여부를 판단하는 것이 객체의 자율성을 위해 더 나은 선택인듯 싶다.

<br>

## 7. DTO 사용

Cars 객체가 지닌 List\<Car\>를 순회하며 각 객체의 이름과 위치를 출력해야 한다.

> OutputView.java

```java
public static void printRacingTryResult(Cars cars) {
    List<Car> carList = cars.getCars();
    carList.foreach(OutputView::printCarInformation);
}
```

처음에 사용한 방식은 Cars 객체 내부 컬렉션을 받아와 순회하며 객체의 정보를 프린트하는 것이었다. View가 Cars의 내부 리스트에 직접적으로 접근하는 점이 다소 걸렸다.

> Cars.java

```java
public List<Car> getCars() {
    return Collections.unmodifiableList(this.cars);
}
```

``Collections.unmodifiableList()``를 통해 리스트를 READ-ONLY로 변경시켜 수정을 막을 수 있다.

그러나 Car 객체의 ``move()`` 메서드가 View에서 원치 않게 사용되는 경우 결과에 큰 영향을 주기 때문에, View와 Car 객체간의 접점을 없애고 싶었다.

> Cars.java

```java
public List<CarDto> getCarDtos() {
    return this.cars.stream()
            .map(CarDto::from)
            .collect(Collectors.toList());
}
```

> OutputView.java

```java
public static void printRacingTryResult(List<CarDto> carDtos) {
    carDtos.forEach(OutputView::printEachCarRacingResult);
}
```

Car 객체의 정보를 담은 CarDto 클래스를 만들고, Dto 클래스만 View에 접근할 수 있도록 변경했다.

<br>

## 8. 예외 중복 제거

> CarNameLengthException.java

```java
public class CarNameLengthException extends RuntimeException {

    private CarNameLengthException(String errorMessage) {
        super(errorMessage);
    }
}
```

> CarNameDuplicationException.java

```java
public class CarNameDuplicationException extends RuntimeException {

    private CarNameDuplicationException(String errorMessage) {
        super(errorMessage);
    }
}
```

Custom Exception을 정의하다보니, 관리해야 할 예외 클래스의 개수가 많아졌다. 비슷한 성격으로 중복되는 예외가 거슬렸다.

> CarNameException.java

```java
public class CarNameException extends RuntimeException {
    private static final String DUPLICATION_ERROR_MESSAGE = "[ERROR] 자동차 이름들 중 중복이 존재해서는 안됩니다.";
    private static final String INVALID_LENGTH_ERROR_MESSAGE = "[ERROR] 자동차 이름의 길이는 1자 이상 5자 이하여야 합니다.";

    private CarNameException(String errorMessage) {
        super(errorMessage);
    }

    public static CarNameException ofDuplicatedNames() {
        return new CarNameException(DUPLICATION_ERROR_MESSAGE);
    }

    public static CarNameException ofInvalidNameLength() {
        return new CarNameException(INVALID_LENGTH_ERROR_MESSAGE);
    }
}
```

따라서 성격이 비슷한 예외의 경우, 공통되는 제너럴 클래스에서 정적 팩토리 메서드를 통해 호출하도록 했으며 서로 다른 에러 메시지를 갖는다.

<br>


## 9. Validator

InputView 클래스 코드 라인이 100줄을 초과했다. 입력값에 대한 유효성 검사를 진행하면서 코드가 길어졌기 때문에, 유효성 검사를 별도의 Validator 유틸 클래스에 구현하는 대안을 고민했었다.

유효성 체크 역할을 Validator에 위임한다면, InputView는 확실히 코드 라인이 줄고 책임도 덜 할 것이다. 그러나 InputView의 데이터에 대한 내부 검증 규칙이 외부 클래스에게 노출된다는 점이 걸린다.

고민 끝에 코드가 조금 길어지더라도, InputView에서 유효성 검사를 계속 진행하도록 했다. 그러나 만약 코드 라인이 이보다 더 길어진다면, 그 때는 Util 클래스 사용에 대해 적극 고려해봐야겠다.

<br>

## Reference

* [클린코드 Part 2 : 적용과 개선 - 깨끗한 코드를 만들기 위한 방법 -](http://kosta.or.kr/mail/2015/download/CleanCode_Part2.pdf)
* [custom exception을 언제 써야 할까?](https://woowacourse.github.io/javable/2020-08-17/custom-exception)
* [클린코드 7장 - 오류 처리](http://amazingguni.github.io/blog/2016/05/Clean-Code-7-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)
* [Clean Code 11. System](https://devstarsj.github.io/study/2018/12/11/study.cleanCode.11/)
* [팩토리 패턴(Factory Pattern) 정리](https://thefif19wlsvy.tistory.com/35)
