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
    //...
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

정답은 없다. 다만 비용, 클래스 복잡도, 편의성 등 Trade-Off를 고려하여 상황에 맞게 잘 선택해야 한다.

2주차 미션은 1주차 미션보다 예외 상황이 많았고, 그만큼 사용자 정의 예외 클래스를 많이 생성하게 되었다. 관리 측면에서 확실히 표준 예외보다 불편한 점이 있으니, 상황을 고려하면서 표준 예외와 함께 적절하게 사용해야겠다.

### 3.3. Application 클래스 구현

프로그래밍 요구사항 중에 "Application의 구현은 변경하지 않는다."는 내용이 있다. main 메서드밖에 없는 클래스인데, 나는 1주차 과제 때 메인 메서드의 코드가 길어져서 해당 클래스에 private 메소드를 추가해서 사용했다.

그 때는 이게 구현을 변경하지 않는 것이라고 생각했다. 그러나 돌이켜보니 메인 메서드 외에 메서드를 추가한 것 자체가 구현을 변경한 것이라는 생각이 들었다. 확실하진 않지만 찜찜하다. :(:(:(

생각해보니 App 클래스 자체는 private 메서드를 가지면 역할이 애매해지는 것 같다. 별개로 실행의 역할만 해야할 것 같다. 그렇다면 main 메서드를 어떻게 사용해야 할까?

> 소프트웨어 시스템은 애플리케이션 객체를 제작하고 의존성을 서로 연결하는 준비과정과 준비과정 이후에 이어지는 런타임 로직을 분리해야 한다.<br>
Clean Code - Robert C. Martin

생성과 관련된 코드를 main이나 main이 호출하는 모듈로 옮기고, 나머지 시스템은 모든 객체가 생성되었고 모든 의존성이 연결되었다고 가정한다. 즉 main 메서드는 다음의 작업을 수행한다.

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

    private final InputView inputView;

    public RacingGameController(InputView inputView) {
        this.inputView = inputView;
    }

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

---

## Reference

* [클린코드 Part 2 : 적용과 개선 - 깨끗한 코드를 만들기 위한 방법 -](http://kosta.or.kr/mail/2015/download/CleanCode_Part2.pdf)
* [custom exception을 언제 써야 할까?](https://woowacourse.github.io/javable/2020-08-17/custom-exception)
* [클린코드 7장 - 오류 처리](http://amazingguni.github.io/blog/2016/05/Clean-Code-7-%EC%98%A4%EB%A5%98-%EC%B2%98%EB%A6%AC)
