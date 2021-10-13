---
title:  "오차 역전파법(Backpropagation)"

categories:
  - AI
last_modified_at: 2021-10-12T08:06:00-05:00

---



이전 글에서 신경망의 학습은 2가지 파트로 나뉜다고 했다. 

손실함수에 대해 기울기를 구하는 부분과 그렇게 구한 기울기를 가지고 업데이트(최적화, Optimization)하는 부분으로 말이다.

오늘은 기울기를 구하는 방법 중 하나인 오차 역전파에 대해 알아보겠다. 

먼저 오차 역전파의 핵심이라고 할 수 있는 연쇄법칙(Chain Rule)을 알아보자.

연쇄법칙이란 합성함수의 미분에서 보았을 것인데, 

$y = f(g(x)$ 와 같은 합성함수가 있을 때, 그 도함수가 다음과 같이 구해진다는 것이다.

$y = f(u), \; u = g(x)$ 라고 하면

$\frac{dy}{dx} = \frac{dy}{du} /cdot \frac{du}{dx}$

$\frac{f(g(x))}{x} = \frac{df(g(x))}{dg(x)} /cdot \frac{dg(x)}{dx}$

${f(g(x))}$








