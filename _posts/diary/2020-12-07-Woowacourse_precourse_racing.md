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
    BaseballGameMachine baseballGameMachine =
            new BaseballGameMachine(BaseballNumbers.generateRandomBaseballNumbers());
    GameState gameState = GameState.initiate();
    while (gameState.isAbleToPlay()) {
        baseballGameMachine = baseballGameMachine.prepareNextTry(gameState);
        BaseballNumbers userBaseballNumbers =
                BaseballNumbers.generateInputBaseballNumbers(inputView.inputBaseballNumbers());
        GameResult gameResult = baseballGameMachine.calculateGameResult(userBaseballNumbers);
        OutputView.outputGameResult(gameResult);
        gameState = GameState.findGameState(inputView.inputGameState(gameResult));
    }
}
```

1주차 미션 과제 내용을 살펴보면, while보다는 do-while을 쓰는 것이 편할 것 같은 부분이 종종 있다. 숫자 야구 게임은 1회차에는 무조건 실행되는 규칙을 가지고 있다. 즉, 1회차 플레이할 때는 gameState 변수의 상태를 확인할 필요가 없다. 그러나 while을 쓰면 1회차 플레이도 gameState 조건을 검사하기 때문에, while 이전에 gameState 변수를 초기화하는 ``GameState.initiate()`` 메서드가 필요하다.


> Application.java

```java
private static void runBaseballGame(InputView inputView) {
    BaseballGameMachine baseballGameMachine =
            new BaseballGameMachine(BaseballNumbers.generateRandomBaseballNumbers());
    GameState gameState;
    do {
      baseballGameMachine = baseballGameMachine.prepareNextTry(gameState);
      BaseballNumbers userBaseballNumbers =
              BaseballNumbers.generateInputBaseballNumbers(inputView.inputBaseballNumbers());
      GameResult gameResult = baseballGameMachine.calculateGameResult(userBaseballNumbers);
      OutputView.outputGameResult(gameResult);
      gameState = GameState.findGameState(inputView.inputGameState(gameResult));
    } while (gameState.isAbleToPlay())
}
```

만약 do-while을 썼다면 해당 메서드는 불필요할 것이다. 이런 부분을 보고, do-while이 프로그램 로직에 잘 부합하다고 생각하여 사용을 고려했다.

그러나 클린 코드와 관련된 글들을 검색해보면서, do-while은 삼항연산자 및 goto 구문과 같이 가독성을 떨어뜨리기 때문에 되도록 지양할 것을 권고하고 있다.

확실히 do-while은 코드를 아래에서 위로 역순으로 읽어야 하는 부자연스러운 흐름이 존재하기 때문에, 조건을 미리 확인할 수 있는 while을 사용하는 것이 낫다고 생각한다.


<br>

---

## Reference
