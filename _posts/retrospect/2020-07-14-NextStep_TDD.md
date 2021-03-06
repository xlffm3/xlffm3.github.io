---
title: "[후기] NextStep - TDD, Refactoring, Clean Code With Java 8기 과정"
excerpt: "교육에서 배운 Java는 내가 알던 Java가 아니었다."
categories:
  - Retrospect
tags:
  - 회고
toc: true
toc_sticky: true
last_modified_at: 2020-07-14T08:30:00-05:00
---

## 1. OOP와 Clean Code

Java와 Spring을 공부하다가 우연히 본 Guru 개발자의 아티클에 다음과 같은 문구가 있었다.

> "좋은 코드의 핵심은 품질이다."

해당 문구를 보고, 나의 학습 방향에 대해 회의감이 들기 시작했다. Java + Spring에서의 고품질의 좋은 코드란 아마 OOP 기반의 클린 코드일 것이다. 나는 클린 코드를 위해 객체 지향적으로 사고하며 코드를 설계할 수 있는가? 해당 물음에 대한 답변은 "아니오"였다.

Inflearn을 보고 Spring을 실습할 때에도 관습적으로 레이어를 구분하여 클래스를 만들 뿐, "이 부분은 별도의 객체를 사용하고, 이 부분은 인터페이스로 추상화해야겠다."와 같은 의식적인 설계 고민을 해본 경험이 전무했다.

객체, 인터페이스, 다형성, 의존성 등 OOP와 관련된 여러 개념들의 사전적인 정의는 알고 있지만, 정작 개발할 때 이를 언제 또는 어떻게 잘 활용할 수 있는지를 몰랐기 때문이었다.

당장 Framework 기능들을 좀 다룬다고 운 좋게 취업했을 때, 기본 설계 능력이 부족한 내가 개발 업계에서 오래 살아남을 수 있을까 하는 두려움이 엄습했다.

나도 좋은 코드를 짜고 싶었다. 다시 한 번 기본기를 탄탄하게 다져야할 필요성을 느끼며 클린 코드 관련 글들을 읽고 공부하고 있던 때, 같이 개발 공부하는 친구 한 명이 **NextStep의 TDD, Refactoring, Clean Code With Java** 수업을 알려주었다. 현재 내가 가진 니즈를 충족시켜줄 것이라 추천을 해서 후기를 살펴 보았는데, 바로 "이거다!" 하는 생각이 들어 수강을 결정하게 되었다.

<br>

## 2. 8기부터 달라진 점

다음은 클린코드를 위한 TDD, 리팩토링 with Java 과정을 운영하고 있는 NextStep의 박재성(자바지기) 님의 메일이다. 직장인 수강생들에게는 희소식일듯 하다.

> 이전에 진행했던 7기까지 교육 과정이 오프라인 강의(매주 3시간) + 온라인 코드리뷰로 진행했다면 8기 과정부터 오프라인 없이 온라인으로만 진행합니다. 7기까지 진행했던 오프라인 강의는 온라인 동영상 강의로 대체합니다.<br><br>5주의 교육 기간이 너무 짧다는 피드백이 있어 교육 기간을 5주에서 8주로 늘렸습니다. 각 기수별 수강생 수를 늘리고, 오프라인 강의장 비용이 들지 않아 강의료를 88만원에서 70만원으로 낮추었습니다.

나는 공부하는 백수라서 시간적인 여유가 충분했기 때문에 고민없이 수강을 결정할 수 있었다.

<br>

## 3. 수업 수강 신청 유의 사항

강의 개설 알람 신청을 꼭 하고, 대학교 수강 신청이나 콘서트 티켓팅처럼 긴장을 해야할 듯 싶다. 인기가 많아서 빨리 마감된다고 한다.

* [NextStep 바로 가기](https://edu.nextstep.camp/)

<br>

## 4. 학습 방식

1. 수강 기간 동안 총 4개의 과제를 수행한다.

2. 매 과제 별로 개발자 역량에 대한 교양 영상과, TDD, Refactoring, Clean Code의 개념 및 방법을 설명하는 영상과 텍스트가 제공된다.

3. 과제는 여러 단계로 구성되어 있으며, 배운 개념을 바탕으로 각 단계에서 제시하는 요구 사항을 구현한다.

4. github에서 Pull Request를 보내고, 현업 개발자인 담당 리뷰어님에게 피드백을 받는다.

5. 피드백을 반영하면 다음 단계로 넘어가며, 모든 단계의 피드백을 반영하면 하나의 과제가 마무리 된다.

리뷰어는 직접적인 정답을 제시해주기 보다는, 방향성에 대한 힌트를 제시해주신다. 띄어쓰기 같은 사소한 컨벤션 등 코드에 대해 정말 섬세하게 지적해주셔서 놀랐다. 퇴근하고 미션을 수행하는 직장인 수강생들도 힘들었겠지만, 퇴근하고 새벽까지 피드백을 남겨주시는 리뷰어님들의 체력에 다시 한번 감탄.

<br>

## 5. 실무 경력이 없더라도

NextStep의 TDD, Refactoring, Clean Code With Java 과정에 대한 후기는 구글에 치면 심심치 않게 찾아볼 수 있다. 대부분이 현직 개발자의 입장에서 작성된 후기이다.

NextStep 홈페이지에는 수강 대상이 **당신이 실무 경력 최소 1~3년 이상의 자바 개발자이며** 라고 명시되어있다. 실제로 Slack에 초대된 수강생들 중에 현직 백엔드 개발자가 많았다.

막상 등록하니 실무 경력 1도 없고, Java 조금 쓸 줄 아는 학생이 들어도 되나 겁났다. 하지만 시간적인 여유가 충분하다면 실무 경력이 없더라도 Spring을 공부하는 학생이 듣기에 좋은 수업이라고 생각한다.

오히려 본격적으로 웹 어플리케이션을 제작해보기 전에 OOP, TDD, Refactoring, Clean Code, Legacy Code, 유지보수 등을 깊게 생각해볼 수 있는 기회를 가짐으로써, 더 나은 코딩 및 설계 습관을 가질 수 있을 것 같다.

물론 Legacy Code나 유지 보수 이슈 등을 많이 접해본 현직 개발자가 이 수업을 수강한다면, 같은 내용을 학생 신분인 나보다 다양한 관점에서 심도있게 학습하여 더 많은 것을 배울 것이다.

<br>

## 6. 나는 어떤 것을 배웠나

* OOP

처음 과제를 하는데 되게 스트레스를 받았다. 기능 구현이 어려워서가 아니라, 내 코드가 OOP스럽지 않다는 것을 느꼈기 때문이다. 툭하면 코드들이 절차지향적으로 변신해버렸다.

과제를 하다가 서점으로 뛰어가 **객체지향의 사실과 오해** 라는 책을 구매했다. 역할과 책임 및 협력을 통해 객체를 설명하는 책이었는데, 역할과 책임이 명확하면 메시지를 통해 객체끼리 협력할 수 있다는 문구가 마음에 와닿았다.

항상 클래스란 객체를 찍어내는 틀이라고 배웠었고, 객체를 정의할 때 상태(인스턴스 변수)를 먼저 정의하곤 했다. 하지만 책에서는 객체가 다른 객체와 어떤 메시지를 주고 받을지 행위(역할과 책임)를 먼저 정하고, 그 것을 토대로 상태 및 속성을 결정할 것을 제시한다.

리뷰어님들도 객체의 책임을 분명하게 나누는 연습에 대해 지속적으로 피드백을 주셨다. 처음에 짠 코드들은 객체들간의 의존 결합도가 높아 단위 테스트하기 까다로웠다. 피드백을 반영하며 코드를 개선하다 보니, 점차 객체들이 분리되면서 단위 테스트를 진행하는데 수월해졌을 뿐만 아니라 요구사항 변경에 좀 더 유연하게 대응할 수 있었다.

또한 까다로운 코드 컨벤션을 준수하면서 과제를 수행하는데, 이 과정에서 평소에 잘 활용하지 않았던 Lambda, Stream, Enum, Interface 등을 학습하고 많이 사용할 수 있었다. 8주간의 의식적인 연습은 Java 및 OOP와 친해질 수 있었던 좋은 기회였다. 👍

* TDD, Refactoring

나는 도메인을 구현할 때, 처음부터 완벽하게 설계하려고 노력을 많이 했었다. 도메인을 가장 작은 단위의 객체로 나누어 아래서부터 구현을 시작했다. 요구사항이 간단한 과제는 가능했지만, 도메인 지식이 부족한 과제에서는 큰 어려움으로 다가왔다. 객체 추상화도 어렵고, 어찌저찌 객체 설계를 완료하고 기능을 구현하다 보면 코드가 엉켜버린다.

첫 술에 배부를 수 없는 것 처럼, 도메인 지식이 부족하면 객체 설계가 처음부터 완벽할 수 없다. 이번 과정에서 아쉬웠던 점은, 처음부터 완벽한 설계에 너무 집착하여 혼자 지쳤다는 사실이다.

도메인 지식이 부족했던 사다리나 볼링 과제는 처음 도메인의 요구사항을 충족할 수 있을 정도로만 빠르게 구현한 다음 객체를 나누어가는 리팩토링이 적합했던 듯 싶다. 그렇게 코드를 엎고 또 엎다보면, 도메인 지식이 조금씩 쌓이면서 그 때 부터는 작은 단위의 객체부터 개발이 가능해지기 때문이다.

완벽한 설계에 집착하고, 코드를 여러번 갈아 엎으면서 나 또한 도메인 지식이 쌓이기 시작하고 작은 단위의 객체 개발이 가능해졌다. 하지만 결정적인 순간에 지친 나머지 객체 분리 리팩토링 힌트를 참고했던 점들이 아쉬움으로 남는다.

그래도 이러한 시행착오 과정이 객체 설계 역량에 큰 도움이 될 것이다. :)

<br>

## 7. 마치며

NextStep의 TDD, Refactoring, Clean Code With Java 과정을 완료하면서, 내가 개발 공부를 시작한지 얼추 1년 정도가 되었다는 사실을 문득 깨달았다.

2019년 상반기, 대기업 영업/마케팅 직무 최종 면접 3개 정도를 자발적으로 포기했다. 면접에 갔었더라도 합격했을 것이라는 보장은 없지만, 청년 취업난이 극심한 때에 면접 기회를 제 발로 차는 문과 학생은 흔하지 않았다.

자기소개서와 인적성 시험을 준비할 때는 취업에 대한 열망이 충만했다. 그런데 서류와 인적성 및 1차 실무진 면접 등 취업 관문을 한 단계씩 통과할 수록 취업 의욕이 떨어져갔다.

번아웃 때문은 아니고, 취업 준비를 통해 '나'라는 사람에 대해 많은 성찰을 할 수 있었기 때문이었다. 사실 그 이전까지는 '나' 혹은 '어떻게 살아가야 하는가'에 대해 큰 고민을 하지 않았었다.

고등학생 때도 좋은 대학에 가면 다 잘 풀릴 것이라는 부모님과 선생님의 말을 상기하며 사춘기 없이 열심히 공부했다. 대학생 때도 좋은 대기업에 가면 다 잘 풀릴 것이라는 생각으로 적당히 스펙을 쌓았다. 내가 어떤 사람이고, 무엇을 좋아하는 지에 대해 크게 고민하지 않았다. 진로 관련 멘토링을 들어보기도 했지만 법, 경제, 금융, 회계 등에 크게 끌리지도 않았다. 적당한 대기업의 영업 직무로 가야겠다는 생각만 들었다.

군인 신분으로 복학해서 수업을 듣는 등, 휴학 없이 대학 생활을 끝맞치고 취업 준비를 시작했다. 자기소개서와 인적성에서 끊임없이 자기 최면을 걸었다. 영업 및 마케팅 직무에 적합한 분석적이고 사교적이고, 하여튼 좋은 역량을 보유한 인재라고. 면접에서도 그런 사람인 척 했다.

취업을 준비하면서 좋았던 점은, 내가 살아온 인생을 비롯해서 '나'라는 사람이 어떤 사람인지 의식적으로 생각할 수 있었다는 것이다. 그런 고민을 많이 하면 할 수록, 취업에 가까워지더라도 썩 기쁘지가 않았다. 나는 면접 스터디에서 만난 다른 지원자들과 다르게 해당 직무에 대해 열정과 흥미가 없었다.

대학교와 과를 선택할 때 내 취향과 관심사보다는 현재 성적으로 갈 수 있는 가장 좋은 대학 간판을 선택했다. 이제는 직무 또한 비슷한 방식으로 선택하고 있었다. 내가 흥미를 가진 직무가 아닌, 현재 스펙으로 갈 수 있는 대기업들 중 가장 입사 확률이 현실적으로 높은(TO가 많은) 직무.

나는 이런 선택이 나쁘다고 말하는 것이 아니다. 21세기 대한민국 사회에서, 특히 문과 학생에게는 가장 현실적인 방법이기 때문이다. 항상 내가 하고 싶은 일만 하면서 살 수는 없는 노릇이며, 내가 배부른 소리를 하는 것일 수도 있다. 하지만 나는 조금 더 시간이 걸리더라도, 내가 하고 싶은 일을 찾고 싶었다.

그리고 개발 공부를 시작했다. 예전에 빅데이터 분석 수업을 들으면서 배운 컴퓨터 언어가 즐거웠던 기억으로 부터 시작한 도전이었다. 개발 공부가 항상 즐거울 수만은 없다. 업으로 삼게 되면, 내가 좋아하는 개발보다 귀찮고 어렵고 짜증나는 개발을 많이 하게 될 것이다. 그럼에도 불구하고 내가 열정과 흥미를 가질 수 있는 분야의 직무를 찾아 선택했다는 점에서 항상 즐겁다.

개발 공부를 시작한지 1년이 되었고, 아직 부족한 점이 많다. 항상 어제보다 더 나은 사람으로 발전하기 위해 노력해야겠다. 해당 과정의 마지막 수업 내용 중 가장 인상깊었던 구절을 인용하며 이 글을 마친다.

> 동의되지 않는 권위에 굴복하지 않으며, 자신의 색깔을 유지하고 자신이 하고 싶은 일을 찾았으면 한다.

<br>

---
