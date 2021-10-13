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

$\frac{dy}{dx} = \frac{dy}{du} \cdot \frac{du}{dx}$

$\frac{df(g(x))}{dx} = \frac{df(g(x))}{dg(x)} \cdot \frac{dg(x)}{dx}$

$\{ f(g(x)) \}' = f'(g(x)) \cdot f(g'(x)) $

g에 대한 미분과 f에 대한 미분을 합성했다고 볼 수도 있지만 다르게 말하면 f•g의 미분을 f와 g의 미분으로 분해한 것이다.

신경망의 연산은 아주 복잡하고 층이 늘어날수록 계산이 길어지기 때문에, 

손실함수에 대한 미분을 바로 구하는 것이 아니라 각 층에 대한 미분으로 분해한 후 계산하여 다시 합성해준다는 것이 오차 역전파의 핵심이다.

이 과정이 뒤 층에서부터 거꾸로 미분한 결과가 전파되는 양상을 띄기 때문에 오차 역전파라고 한다.

이를 위해서는 각 층들을 모두 미분할 수 있어야 하는데, 우리는 아직 모든 층을 배우지 않았다.

그렇게 때문에 활성화함수라는 것을 짚고 넘어가겠다.
 
 $a = xw + b$ 이렇게 가중치와 곱해져 출력이 나왔다고 하자.
 
 여기에 활성화함수라는 것을 씌워 그 출력을 내보낸다.
 
 $z = \sigma (a)$
 
 이렇게 최종 출력은 z가 된다.
 
 그렇다면 이 활성화함수 $\sigma(x)$는 무엇인가?
 
 활성화함수의 종류는 여러가지인데, 오늘은 활성화함수가 핵심이 아니기 때문에 대표적인 활성화함수 시그모이드(Sigmoid) 함수를 볼 것이다.
 
 시그모이드 함수는 아래와 같이 정의한다.
 
$\sigma (x) = \frac{1}{1 + e^{-x}}$

시그모이드 함수를 그려보면 다음과 같다.

![](/assets/image/sigmoid.png)

S자 커브를 그리는 모양인데, 가장 중요한 성질은 함수값이 무조건 0 부터 1 사이의 실수를 가진다는 점이다.

x가 클수록 1로 수렴하며, 작을수록 0으로 수렴한다.

0일 때는 0과 1의 중간값인 0.5를 가진다.

활성화함수는 신경망에 비선형성(Non-Linearity)을 추가하기 위해 쓰이는 함수이다.

활성화함수에 대한 대략적 설명이 끝났으니 이제 오차 역전파에 대해 제대로 알아보도록 하겠다.

![](/assets/image/1-1perceptron.png)

다음과 같은 아주 간단한, 1개의 퍼셉트론으로 이루어진 신경망을 생각해보자.

이 신경망을 아래처럼 수식으로 나타낼 수 있다.

$a = xw + b $

$y = \sigma (a)$

$Error = \frac{1}{2} (y - t)^2$

손실함수는 평균 제곱 오차를 사용했으며 y는 신경망의 출력, t는 실제 정답이다.

우리가 구해야 하는 것은 가중치에 대한 기울기와 편향에 대한 기울기는

$\frac{\partial Error}{\partial w}$ 

$\frac{\partial Error}{\partial b}$

위 2개이다.

저 2개를 알아내면 옵티마이저를 통해 가중치를 업데이트할 수 있다.

연쇄법칙에 근거해서 뒤에서부터 구해보겠다.

$\frac{\partial Error}{\partial y} = \frac{\partial \frac{1}{2} (y-t)^2}{\partial y} = y - t$

위 식처럼 손실을 출력에 대해 편미분하면 $y-t$이다.

$\frac{\partial y}{\partial a} = \frac{\partial \sigma(a)}{\partial a} = \sigma'(a)$

그리고 출력 y를 a에 대해 편미분하면 활성화함수의 도함수가 나온다.

$\frac{\partial a}{\partial w} = \frac{\partial xw + b}{\partial w} = x$

a에 대하여 가중치 w로 편미분하면 x가 된다.

$\frac{\partial a}{\partial b} = \frac{\partial xw + b}{\partial b} = 1$

a에 대하여 편향 b로 편미분하면 1이 된다.

이제 연쇄법칙을 이용해 $\frac{\partial Error}{\partial w}$ 와 $\frac{\partial Error}{\partial b}$를 분해하면

$\frac{\partial Error}{\partial w} = \frac{\partial Error}{\partial y} \cdot \frac{\partial y}{\partial a} \cdot \frac{\partial a}{\partial w} = (y-t)\sigma'(a)x$

$\frac{\partial Error}{\partial b} = \frac{\partial Error}{\partial y} \cdot \frac{\partial y}{\partial a} \cdot \frac{\partial a}{\partial b} = (y-t)\sigma'(a)$

위 식처럼 기울기를 구할 수 있다.

기울기를 구했으면 이제 옵티마이저를 통해 업데이트 해주기만 하면 된다.

가장 간단한 SGD를 사용하여 업데이트를 하면 아래와 같다.

$w \; \colon= w - \alpha \frac{\partial Error}{\partial w}$

$b \; \colon= b - \alpha \frac{\partial Error}{\partial b}$

 








