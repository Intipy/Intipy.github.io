---
title:  "행렬 표현에서의 오차 역전파법"

categories:

  - AI
last_modified_at: 2021-10-12T08:06:00-05:00

---





이전 글에서 오차 역전파에 대한 설명을 단순화된 신경망 예제를 통하여 하였다.

이번에는 유의미한 크기의 신경망에서의 역전파를 보겠다.

여기서는 행렬과 관련하여 역전파를 다룰 것이다.

먼저 신경망의 구조를 아래와 같이 모델링했다.

<br/>

$[입력 뉴런 10개] \; - \; [은닉 뉴런 15개] \; - \; [활성화함수] \; - \; [출력 뉴런 5개] \; -  \; [활성화함수]$

<br/>

이렇게 모델링한 신경망을 그래프 형태로 나타내면 아래와 같다.

![](/assets/image/10-15-5-nn.png)

<br/>

<br/>

그리고 행렬로 표현하면 아래와 같다. (대문자는 모두 행렬)

$A_1 = X \cdot W_1 + B_1$

$Z_1 = \sigma(A_1)$

<br/>

$A_2 = Z_1 \cdot W_2 + B_2$

$Z_2 = \sigma(A_2)$

<br/>

각 행렬의 형상은 이전 글에서 설명한 행렬 표현의 특징에 따라 아래처럼 결정된다.

$X: 1 \times 10,$

$W_1: 10 \times 15$

$B_1: 1 \times 15$

$A_1: 1 \times 15$

<br/>

$Z_1: 1 \times 15$

$W_2: 15 \times 5$

$B_2: 1 \times 5$

$A_2: 1 \times 5$

<br/>

$Z_2: 1 \times 5$

<br/>

이렇게 신경망에서 최종 출력 $Z_2$가 $1 \times 5$의 형상으로 나온다. (출력 뉴런 5개에 대응되는 행렬)

순전파(Feedforwrd)를 한줄로 정리하면 $output = \sigma[\{\sigma(X \cdot W_1 + B_1) \} \cdot W_2 + B_2]$

<br/>

이제 역전파를 진행하도록 하겠다.

정답 5개와 출력 5개에 대하여, 마찬가지로 손실함수를 이용해 오차를 구한다.

$Error = \frax{1}{2} (Z_2 - T)^2$

T는 정답을 행렬로 나타낸 것이며, 오차 또한 행렬로 표현된다.

오차 행렬에 대해 가중치와 편향 행렬에 대한 미분을 구하면 아래로 표현된다.

$\frac{\partial Error}{\partial W_2} = \frac{\partial A_2}{\partial W_2} \cdot \{\frac{\partial Error}{\partial Z_2}  \cdot \frac{\partial Z_2}{\partial A_2} \}$

$\frac{\partial Error}{\partial B_2} = \frac{\partial Error}{\partial Z_2}  \cdot \frac{\partial Z_2}{\partial A_2} \cdot \frac{\partial A_2}{\partial B_2}$

$\frac{\partial A_2}{\partial W_2}$가 앞으로 온 것이 눈에 띄는데, 이것은 밑에서 설명한다.

이렇게 각 계층에 대한 편미분으로 분해했으니 층마다 편미분을 해보겠다.

<br/>

$\frac{\partial Error}{\partial Z_2} = \frac{\partial \frax{1}{2} (Z_2 - T)^2}{\partial Z_2} = Z_2 - T$

위 식은 오차에 대해 출력을 미분한 값이다.

<br/>

$\frac{\partial Z_2}{\partial A_2} = \frac{\partial \sigma(A_2)}{\partial A_2} = \sigma'(A_2) = \sigma(A_2) \cdot (1 - \sigma(A_2)) = Z_2(1 - Z_2)$

위 식은 출력에 대해 가중합($A_2$)을 미분한 것이다.

<br/>

$\frac{\partial A_2}{\partial W_2} = \frac{\partial Z_1 \cdot W_2 + B_2}{\partial W_2} = Z_1^T$

위 식은 가중합($A_2$) 대해 가중치를 미분한 값이다.

여기서 $Z_1^T$는 행렬 $Z_1$의 전치행렬(Transposed Matrix)을 나타낸다.

어떤 스칼라값을 가지는 변수에 대해 미분했다면 전치행렬같은 개념 없이 미분이 쉽게 된다.

예를 들어 위 식에서 Z와 W가 행렬이 아니었다면, $\frac{\partial a_2}{\partial w_2} = \frac{\partial z_1 \cdot w_2 + b_2}{\partial w_2} = z_1$

이렇게 $z_1$로 미분이 되지만 행렬일 경우에 $Z_1^T$로 미분이 된다.

미분한 값이 어째서 전치행렬이 되는가에 대한 답은 자코비안 미분에 있다.

자코비안 미분에 대한 자세한 것은 나중에 설명할 것이고, 지금은 전치행렬이 나온다는 것만 알도록 하자.

<br/>

$\frac{\partial A_2}{\partial B_2} = \frac{\partial Z_1 \cdot W_2 + B_2}{\partial B_2} = 1$

위 식은 가중합($A_2$) 대해 편향을 미분한 값이다.

<br/>

이제 각 층에 대해서 미분이 끝났으므로 손실에 대한 가중치와 편향의 기울기를 구할 수 있다.

$\frac{\partial Error}{\partial W_2} = \frac{\partial A_2}{\partial W_2} \cdot \{ \frac{\partial Error}{\partial Z_2}  \cdot \frac{\partial Z_2}{\partial A_2} \}=Z_1^T \{(Z_2 - T)Z_2(1-Z_2)\}$

$\frac{\partial Error}{\partial B_2} = \frac{\partial Error}{\partial Z_2}  \cdot \frac{\partial Z_2}{\partial A_2} \cdot \frac{\partial A_2}{\partial B_2} = (Z_2 - T)Z_2(1-Z_2)$

이전에 했던 스칼라 연산과 달리 행렬의 곱이므로 순서가 매우 중요한데,

자코비안 미분을 했을 때 $Z_1^T$은 앞에서 곱해져야 한다.

즉, $Z_1^T \cdot [다음 층에서 역전파된 기울기]$ 와 같은 순서를 유지해야 한다.

다른 층의 경우에 $[다음 층에서 역전파된 기울기] \cdot [현재 층 미분]$ 이렇게 역전파가 되는 것과 순서가 정반대이다. 

<br/>

이제 2층의 가중치와 편향은 편미분계수를 구했으나, 1층의 가중치와 편향은 아직 역전파가 도달하지 않아서 구해지지 않았다.

입력에 대해 편미분을 하여 1층까지도 전파를 해보자.

손실에 대하여 2층의 입력이자 1층의 출력, $Z_1$를 미분하면 아래와 같다.

$\frac{\partial Error}{\partial Z_1} = \frac{\partial Error}{\partial Z_2} \cdot \frac{\partial Z_2}{\partial A_2} \cdot \frac{\partial A_2}{\partial Z_1} = (Z_2 - T)Z_2(1-Z_2)W_2^T$








