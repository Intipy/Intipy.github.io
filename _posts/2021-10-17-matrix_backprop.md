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

$\frac{\partial Error}{\partial W_2} = \frac{\partial Error}{\partial Z_2}  \cdot \frac{\partial Z_2}{\partial A_2} \cdot \frac{\partial A_2}{\partial W_2}$

$\frac{\partial Error}{\partial B_2} = \frac{\partial Error}{\partial Z_2}  \cdot \frac{\partial Z_2}{\partial A_2} \cdot \frac{\partial A_2}{\partial B_2}$











