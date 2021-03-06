---
title: "《코딩을 지탱하는 기술》을 읽고"
excerpt: "프로그래밍 언어는 편리함을 위한 도구다."
categories:
  - Book
tags:
  - 도서
date: 2021-01-16
last_modified_at: 2021-01-16
---

## 1. 왜 이렇게 다를까?

![image](https://user-images.githubusercontent.com/56240505/104808772-c3fe6800-582b-11eb-830f-82cbb778a460.png)

Java를 하다가 C를 접하면서 큰 충격을 먹었던 기억이 난다. ``boolean``과 ``try-catch`` 예외 처리가 없다니? int 반환값에 따라서 예외 처리하는 코드를 짜다가, "Java는 정말 편한 언어구나"를 느꼈다. 이후에는 어셈블리어를 통해 C 표준 함수들을 리코딩할 기회가 있었다. 이 때는 "C는 정말 편한 언어구나"를 느꼈다. 당연하게 여겼던 개념인 조건문 및 반복문 없이 함수를 작성하려고 하다보니 어려움이 이만저만이 아니었다.

Python, Ruby, C#, Java, C++, C 등 내가 사용해본 언어들은 서로가 비슷하면서도 사뭇 달랐다. 왜 이렇게 다를까? 궁금하지만 ''각 언어들의 패러다임(객체지향, 절차지향, 함수형 등) 차이로 인해서 그러겠거려니" 하며 깊게 고민해보지 못한 주제였다.

<br>

## 2. 프로그래밍 언어는 편리함을 위한 도구다

컴퓨터(Computer)는 인간이 하기 귀찮은 연산 작업을 대신 해주는 도구로서 고안되었다. 프로그램(Program)은 어떤 문제를 해결하기 위해 컴퓨터에게 주어지는 일련의 명령문이다. 컴퓨터와 프로그램의 골자는 인간을 편리하게 만들기 위한 도구라는 점이다.

프로그래밍 언어 역시 인간이 컴퓨터 프로그램을 **편리**하게 만들도록 돕기 위해 고안된 도구이다. 현재 수 많은 프로그래밍 언어들이 있으며 언어들간에는 공통점과 차이점이 있다. 프로그래밍 언어들은 태초에 **편리함**이라는 공통된 목적을 위해 고안되었지만, **편리함이란 무엇인가**에 대한 철학의 차이로 인해 각 언어들은 다양한 방식과 형태로 발전되어 왔다.

이 책은 역사를 통해 여러 프로그래밍 언어들이 가지고 있는 다양한 개념들이 **왜** 탄생했는지 설명한다. 그리고 언어들간의 차이 비교를 통해 특정 언어가 어떤 철학과 기준을 가지고 설계 및 변화되었는지를 보여준다. **편리함**에 대한 철학의 차이로 인해 각 언어별로 Type, Scope, Thread, Exception, OOP 등의 개념이 서로 다르게 구현되어 있는 점이 흥미롭다.

<br>

## 3. 어떻게 학습해야 할까?

이 책을 통해 너무나 당연하게 여겼던 개념들이 왜 탄생했고 어떻게 변화했는지를 이해하면서, 끊임없이 견지해야 할 학습 태도를 다시금 고찰해볼 수 있었다. IT 기술 변화는 무척 빠르며 새로운 개념들은 지금도 계속 생겨나고 있다. 현존 프로그래밍 언어와 프레임워크들은 여전히 한계가 있다. 내가 학습 중인 Java와 Spring 역시 언젠가는 더욱 우수한 패러다임과 철학을 가진 Counterpart에 의해 대체될 것이다. 나는 어떻게 학습해야 할까?

> 기술 변화 속도가 너무 빨라서 특정 언어나 툴에 관한 지식은 금방 쓸모 없게 된다. ‘변화하고 있는 지식’을 꾸준히 습득하지 않으면, 이미 학습한 지식도 점점 가치를 잃게 된다.

Comfort Zone에서 벗어나자. 낯설고 새로운 개념(언어, 프레임워크, 기술 등)이 두렵고 불편해도 적극적으로 배우자. 그리고 어떠한 새로운 개념이 **왜** 탄생했는지를 먼저 잘 이해하자. Why를 알면 언제, 어떻게 해당 개념을 사용해야 할지 판단하는 통찰력을 가질 수 있다.

또한 개발에 있어서 완벽이란 존재하지 않음을 인정하자. 내가 사용하고 있는 기술 스택이 완벽하다는 폐쇄적인 자세를 경계해야 한다. 언어, 프레임워크, 기술 등은 결국 도구이다. 어떤 도구가 우월하고 열등한지 따지는 것은 소모적인 논쟁이다. 책에서도 형 추론 및 예외 처리 등의 기능을 구현하는 방식에 대해서는 여전히 의견이 분분하다고 지적한다. 각 언어들은 그저 비용, 호환성, 이점 등을 종합적으로 고려하여 자신의 철학에 맞는 방식을 채택할 뿐이다. 여러 도구들의 Trade-Off를 다각도로 분석하고, 상황과 목적에 맞게 선택할 수 있는 열린 자세를 갖자.

개발자는 죽을 때까지 공부해야 하는 직업이라고 한다. 아무리 타고난 마라토너도 계속 오르막길을 마주하다 보면 지치는 것처럼, 개발자 또한 새로운 기술의 파도에 계속 휩쓸리다 보면 지치게 될 것이다. 혹은 새로운 기술 개념의 방대함에 압도당해 자존감이 떨어질 지도 모른다. 나도 공부를 하다 보면 그런 슬럼프가 종종 찾아오곤 하는데, 처음 시작 단계부터 모든 부분을 완벽하게 이해하려고 에너지를 쏟아 부었기 때문이었다. 이와 관련해 책에 좋은 구절이 있어 인용해본다.

> 첫 번째 단계, 필요한 부분부터 흡수한다.<br>
완벽주의가 배우고자 하는 동기를 짓누르고 있다면 버려버리는 것이 낫다. 좌절하고 포기하는 것보다는 낫다.<br><br>
두 번째 단계, 대략적인 부분을 잡아서 조금씩 상세화한다.<br>
문서의 대략적인 구조 및 처리 흐름을 따라가고, 이후 깊이를 더해 상세한 내용을 이해해나간다.<br><br>
세 번째 단계, 끝에서부터 차례대로 베껴간다.<br>
명확한 동기가 없이 대충 읽으면 지식은 내를 그냥 스쳐 지나갈 뿐이다. 지식의 밑바탕을 만들기 위해 교과서를 그대로 베껴 쓴다. 지식이 없는 상태에서 고민하는 것은 무의미하기 때문에 지식을 복사하는 것이다.

프로그래밍을 입문할 때 이 책을 읽었으면 좋았겠지만... 여러 언어의 철학 및 차이점 등을 경험한 상태에서 읽으니 더욱 재미있게 몰입하며 읽을 수 있었다. 😄

<br>

---

## Reference

* 코딩을 지탱하는 기술(니시오 히로카즈 저)
