---
title:  "오차 역전파법(Backpropagation)"

categories:
  - Machine Learning 
last_modified_at: 2021-10-12T08:06:00-05:00

---

이전 글에서 신경망의 학습은 2가지 파트로 나뉜다고 했다. 
손실함수에 대해 기울기를 구하는 부분과 그렇게 구한 기울기를 가지고 업데이트(최적화, Optimization)하는 부분으로 말이다.
오늘은 기울기를 구하는 방법 중 하나인 오차 역전파에 대해 알아보겠다. 

먼저 오차 역전파의 핵심이라고 할 수 있는 연쇄법칙(Chain Rule)을 알아보자.
연쇄법칙이란 합성함수의 미분에서 보았을 것인데, 

$y = f(g(x))$ 와 같은 합성함수가 있을 때, 그 도함수가 다음과 같이 구해진다는 것이다.

$y = f(u), \; u = g(x)$ 라고 하면,

$$
\begin{align} 
\tag{1}
\frac{dy}{dx} = \frac{dy}{du} \cdot \frac{du}{dx}
\end{align}
$$

$$
\begin{align} 
\tag{2}
\frac{df(g(x))}{dx} = \frac{df(g(x))}{dg(x)} \cdot \frac{dg(x)}{dx}
\end{align}
$$

$$
\begin{align} 
\tag{3}
\{ f(g(x)) \}' = f'(g(x)) \cdot f(g'(x))
\end{align}
$$

g에 대한 미분과 f에 대한 미분을 합성했다고 볼 수도 있지만 다르게 말하면 f•g의 미분을 f와 g의 미분으로 분해한 것이다.

신경망의 연산은 아주 복잡하고 층이 늘어날수록 계산이 길어지기 때문에, 
손실함수에 대한 미분을 바로 구하는 것이 아니라 각 층에 대한 미분으로 분해한 후 계산하여 다시 합성해준다는 것이 오차 역전파의 핵심이다.

이 과정이 뒤 층에서부터 거꾸로 미분한 결과가 전파되는 양상을 띄기 때문에 오차 역전파라고 한다.
이를 위해서는 각 층들을 모두 미분할 수 있어야 하는데, 우리는 아직 모든 층을 배우지 않았다.
그렇게 때문에 활성화함수라는 것을 짚고 넘어가겠다. 

$$
\begin{align} 
\tag{4}
a = xw + b
\end{align}
$$

(4)와 같이 출력이 나왔다고 하자.
 
여기에 활성화함수라는 것을 씌워 그 출력을 내보낸다. (5)
 
$$
\begin{align} 
\tag{5}
z = \sigma (a)
\end{align}
$$
 
이렇게 최종 출력은 $z$가 된다.
그렇다면 이 활성화함수 $\sigma(a)$는 무엇인가?

활성화함수의 종류는 여러가지인데, 오늘은 활성화함수가 핵심이 아니기 때문에 대표적인 활성화함수 시그모이드(Sigmoid) 함수를 볼 것이다. 시그모이드 함수는 (6)과 같이 정의한다.

$$
\begin{align} 
\tag{6}
\sigma (x) = \frac{1}{1 + e^{-x}}
\end{align}
$$

시그모이드 함수를 그려보면 figure 1과 같다.

![](/assets/image/sigmoid.png)
Figure 1: 시그모이드 함수

S자 커브를 그리는 모양인데, 가장 중요한 성질은 함수값이 무조건 0 부터 1 사이의 실수를 가진다는 점이다.
x가 클수록 1로 수렴하며, 작을수록 0으로 수렴한다.
0일 때는 0과 1의 중간값인 0.5를 가진다.

활성화함수는 신경망에 비선형성(Non-Linearity)을 추가하기 위해 쓰이는 함수이다.

우리가 AND 게이트를 구현할 때도 사실은 이미 활성화함수가 쓰이고 있었다.
바로 계단 함수이다.

$$
\begin{align}
\tag{7}

y = \begin{cases} 
      0\;(x_1w_1+x_2w_2+b \leq 0) \\
      1\;(x_1w_1+x_2w_2+b > 0) 
    \end{cases}

\end{align}
$$

위 식은 AND 게이트 문제를 풀기 위해 구성한 신경망의 구조를 수식으로 풀어낸 것이다.(퍼셉트론을 설명한 글에서 이미 설명하였다)
위 식에서 0 이하일 때는 0을 출력하고 0을 초과하면 1을 출력하는 것을 볼 수 있다.
여기에서 활성화함수로 쓰인 계단함수만 따로 떼어내면 아래와 같다.

$$
\begin{align}
\tag{8}

step(x) = \begin{cases} 
      0\;(x\leq0) \\
      1\;(x>0) 
    \end{cases}

\end{align}
$$

이렇게 $step(x)$는 계단함수이며, 우리가 AND 게이트 신경망을 구현할 때 활성화함수로 쓰였다.
또한 $step(x)$를 아래처럼 그릴 수 있다.

![](/assets/image/step_function.png)
Figure 2: 계단함수

이제 활성화함수라는 개념을 적용해서 AND 게이트의 신경망을 다시 표현하면 아래로 바뀐다.

$$
\begin{align} 
\tag{9}
y= step(x_1w_1+x_2w_2+b)
\end{align}
$$

활성화함수에 대한 대략적 설명이 끝났으니 이제 오차 역전파에 대해 제대로 알아보도록 하겠다.

![](/assets/image/1-1perceptron.png)
Figure 3: 퍼셉트론 1개로 이루어진 신경망. 가중치, 편향, 입력, 출력 등의 모든 매개변수를 각각 하나씩만 가진다. 

figure 3와 같은 아주 간단한, 1개의 퍼셉트론으로 이루어진 신경망을 생각해보자.
이 신경망을 아래처럼 수식으로 나타낼 수 있다.

$$
\begin{align} 
\tag{10}
a = xw + b 
\end{align}
$$

$$
\begin{align} 
\tag{11}
y = \sigma (a)
\end{align}
$$

$$
\begin{align} 
\tag{12}
Error = \frac{1}{2} (y - t)^2
\end{align}
$$

손실함수는 평균 제곱 오차를 사용했으며 $y$는 신경망의 출력, $t$는 실제 정답이다.

우리가 가중치와 편향을 최적화시키기 위해 필요한 것(기울기)은

$$
\begin{align} 
\tag{13}
G_w = \frac{\partial Error}{\partial w}
\end{align}
$$

$$
\begin{align} 
\tag{14}
G_b = \frac{\partial Error}{\partial b}
\end{align}
$$

위 (13), (14)이다. 이 2개를 알아내면 옵티마이저를 통해 가중치와 편향을 업데이트할 수 있다.

우선 신경망이라는 복잡한 함수를 미분하기 위해 이것을 분해하겠다.
각 층을 미분하기 위해 층마다 분해하면 (15), (16)과 같다.

$$
\begin{align} 
\tag{15}
\frac{\partial Error}{\partial w} = \frac{\partial Error}{\partial y} \cdot \frac{\partial y}{\partial a} \cdot \frac{\partial a}{\partial w}
\end{align}
$$

$$
\begin{align} 
\tag{16}
\frac{\partial Error}{\partial b} = \frac{\partial Error}{\partial y} \cdot \frac{\partial y}{\partial a} \cdot \frac{\partial a}{\partial b}
\end{align}
$$

연쇄법칙에 근거해서 뒤에서부터 구해보겠다. (여기서 뒤라는 것은 신경망의 연산 순서의 반대)

$$
\begin{align} 
\tag{17}
\frac{\partial Error}{\partial y} = \frac{\partial \frac{1}{2} (y-t)^2}{\partial y} = y - t
\end{align}
$$

$$
\begin{align} 
\tag{18}
\frac{\partial y}{\partial a} = \frac{\partial \sigma(a)}{\partial a} = \sigma'(a)
\end{align}
$$

$$
\begin{align} 
\tag{19}
\frac{\partial a}{\partial w} = \frac{\partial xw + b}{\partial w} = x
\end{align}
$$

$$
\begin{align} 
\tag{20}
\frac{\partial a}{\partial b} = \frac{\partial xw + b}{\partial b} = 1
\end{align}
$$

(17): 손실을 출력에 대해 편미분하면 $y-t$가 된다.
(18): 출력 $y$를 $a$에 대해 편미분하면 활성화함수의 도함수가 된다. 
(19): a에 대하여 가중치 w로 편미분하면 x가 된다.
(20): a에 대하여 편향 b로 편미분하면 1이 된다.

이제 연쇄법칙을 이용해 $G_w$(equation 13)과 $G_b$(equation 14)를 분해하면 (21), (22)와 같은 값을 얻는다.

$$
\begin{align} 
\tag{21}
\frac{\partial Error}{\partial w} &= \frac{\partial Error}{\partial y} \cdot \frac{\partial y}{\partial a} \cdot \frac{\partial a}{\partial w} \\
                                  &= (y-t)\sigma'(a)x
\end{align}
$$

$$
\begin{align} 
\tag{22}
\frac{\partial Error}{\partial b} &= \frac{\partial Error}{\partial y} \cdot \frac{\partial y}{\partial a} \cdot \frac{\partial a}{\partial b} \\ 
                                  &= (y-t)\sigma'(a)
\end{align}
$$

나중에 또 자세히 다룰 것이지만 미리 말하자면 (23)처럼 시그모이드 함수를 미분할 수 있다.

$$
\begin{align} 
\tag{23}
\frac{\partial y}{\partial a} &= \frac{\partial \sigma(a)}{\partial a} \\ 
                              &= \sigma(a)(1-\sigma(a)) \\ 
                              &= y(1-y)
\end{align}
$$

미분 과정은 나중에 자세히 설명하도록 하겠다.

기울기를 구했으면 이제 옵티마이저를 통해 업데이트 해주기만 하면 된다.
가장 간단한 SGD를 사용하여 업데이트를 하면 아래와 같다.

$$
\begin{align} 
\tag{24}
w \; \colon= w - \alpha \frac{\partial Error}{\partial w}
\end{align}
$$

$$
\begin{align} 
\tag{25}
b \; \colon= b - \alpha \frac{\partial Error}{\partial b}
\end{align}
$$

신경망의 층이 이게 전부라면 여기서 끝내도 된다.
하지만 신경망은 층을 깊게 쌓는 경우가 많으므로 가중치와 편향만 업데이트 하고 끝내면 안된다.
일단 하나의 층에 대해서 어떻게 역전파가 되는지 생각해보자.
손실함수는 출력층에만 있으므로 논외로 하고, 
해당 층의 출력 $y$에서부터 미분을 시작해서 전파한다.

신경망은 1층-2층-3층... 이런 구조로 되어있고 인접한 층의 뉴런들은 서로 연결되어있기 때문에 다음 층으로 전파(신경망의 연산 방향으로의 전파이며 역전파와 달리 순전파라고 한다.)가 된다.
즉, 1층의 출력은 2층의 입력과 연결되어 있으며, 2층의 출력은 3층의 입력과 연결되어있다.
따라서 우리는 입력에 대해서도 미분을 해주어야한다.
입력은 이전 층의 출력과 연결되어 있으므로 입력에 대해 미분을 해야지 이전 층으로 역전파가 가능하다. (이전 층 입장에서 다음 층의 입력은 자신의 출력에서 나왔기 때문이다. 말했듯이 역전파는 출력에서부터 시작한다.)

그렇기에 우리는 $\frac{\partial Error}{\partial x}$를 구해주어야 한다.

$$
\begin{align} 
\tag{26}
\frac{\partial a}{\partial x} = \frac{\partial xw + b}{\partial x} = w
\end{align}
$$

$$
\begin{align} 
\tag{27}
\therefore \frac{\partial Error}{\partial x} &= \frac{\partial Error}{\partial y} \cdot \frac{\partial y}{\partial a} \cdot \frac{\partial a}{\partial x} \\ 
                                            &= (y-t)\sigma'(a)w
\end{align}
$$

이렇게 입력에 대해 미분을 했다면 이전 층으로 역전파가 된다. 
이전 층의 입장에서는 출력의 미분부터 역전파가 다시 시작되는 것이다.
그 이후로는 같은 과정으로 역전파가 되며, 층이 아무리 깊어도 연쇄법칙에 따라 기울기를 구할 수 있다.



 








