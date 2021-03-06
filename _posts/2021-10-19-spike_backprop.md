---
title:  "Enabling Spike-Based Backpropagation For Training Deep Neural Network Architectures"

categories:

  - Paper Review
last_modified_at: 2021-10-12T08:06:00-05:00
---

해당 글은
<br/>
[Enabling Spike-Based Backpropagation For Training Deep Neural Network Architectures](https://arxiv.org/abs/1903.06379) 
<br/>
라는 논문을 소개하는 글이다.
<br/>
<br/>
<br/>
<br/>
**ABSTRACT**
<br/>
*"스파이킹 신경망(SNN)는 최근 눈에 띄는 신경 컴퓨팅 패러다임으로 부상하고 있다.
일반적인 얕은 SNN 아키텍처는 복잡한 표현을 표현할 수 있는 양이 제한적이지만, 입력 스파이크를 사용하여 심층 SNN을 학습시키는 것은 지금까지 성공하지 못했다. 
이 문제를 해결하기 위해 기성 훈련된 심층 인공 신경망을 SNN으로 전환하는 것과 같은 다양한 방법이 제안되었다. 
그러나 ANN-SNN 변환 체계는 스파이킹 시스템의 시간의 역학적 특성을 포착하지 못한다. 
한편, 스파이크 생성 함수의 불연속적이고 차별화 불가능한 특성 때문에 입력 스파이크 이벤트를 사용하여 심층 SNN을 직접 학습시키는 것은 여전히 어려운 문제이다. 
이 문제를 극복하기 위해 LIF 뉴런의 Leaky Behavior를 설명하는 대략적인 파생 방법을 제안한다. 
이 방법은 스파이크 기반 역전파를 사용하여(입력 스파이크 이벤트를 통해) 심층 컨볼루션 SNN을 직접 학습시킬 수 있다. 
우리의 실험은 스파이크 기반 학습으로 훈련된 다른 SNN에 비해 MNIST, SVHN 및 CIFAR-10 데이터 세트에서 최고의 분류 정확도를 달성함으로써 심층 네트워크(VGG 및 잔류 아키텍처)에 대한 제안된 스파이크 기반 학습의 효과를 보여준다. 
또한 스파이킹 도메인에서 추론 연산을 위해 제안된 SNN 훈련 방법의 효과를 입증하기 위해 희소 이벤트 기반 계산을 분석한다."*
<br/>
<br/>
<br/>
<br/>

스파이킹 신경망이란 인간 뇌의 실제 뉴런 구조를 모방함으로서 생물학적인 신경망을 최대한 모사하는 신경망 모델이다.
인간의 뇌는 각각의 뉴런들이 주고받는 '스파이크'에 의해 신호들을 주고받는다.
'스파이크'란, 이름처럼 급격하게 증가하는 전기적 신호를 일컫는 말이다.
뉴런을 관찰한 결과, 뉴런의 전위가 특정 상태에서 급격하게 치솟는 양상을 띈다. 
뉴런이 또다른 뉴런으로부터 전기적를 받으면, 
그리고 전기신호를 받음으로서 뉴런이 활성화되면,
그 뉴런또한 스파이크를 발생시켜 전기신호를 다른 뉴런으로 보낸다.
이러한 과정들의 연속으로 뇌 속의 신경들의 상호작용이 진행된다.

뉴런의 전위가 서서히 증가하다가 임계값을 넘으면 스파이크를 발생시키는데,
이러한 뉴런을 IF(Integrate and Fire) 뉴런이라고 한다.
IF 뉴런은 입력이 들어오면 입력에 따라 뉴런의 막전위가 증가한다.
그렇게 계속해서 증가하다가 입계값을 넘음으로서 스파이크를 발생시키고 막전위는 초기화된다.
이렇게 전위가 쌓이고, 스파이크한 후에 전위가 급격히 떨어져 휴식기간을 가진다.
그리고 IF 뉴런에서 Leaky한 효과가 추가된 것을 LIF(Leaky Integrate and Fire) 뉴런이라고 한다.
Leaky란 누수, 누출을 말하는데, 입력이 없을 때 막전위가 점점 감소(누출)한다는 것이다.

LIF 뉴런이 발화하는 과정을 그림으로 나타내면 아래와 같다.

![](/assets/image/LIF_graph.png)
Figure 1: Leaky integrate and fire

pre-spikes는 이전 뉴런이 발생시킨 스파이크이며, 가중치 $w$가 곱해져 입력으로 들어온다.
입력이 들어오면 LIF 뉴런의 전위도 높아진다.
가운데는 입력을 받은 LIF의 막전위 그래프인데, $V_{mem}$는 Membrane potential(막전위)을 나타내는 변수이다.
time은 이름 그대로 시간의 흐름을 나타내며, $V_{th}$는 Threshold(임계치)를 의미한다.
막전위($V_{mem}$)가 임계 전위($V_{th}$)를 넘으면 스파이크를 발생시켜 다음 뉴런으로 보낸다.
post-spikes는 현재의 LIF 뉴런이 일으킨 스파이크로, 다음 뉴런으로 전달된다.

LIF 뉴런을 수식으로 나타내면 아래와 같다.

$$
\begin{align} 
\tag{1}
\tau_m \frac{dV_{mem}}{dt} = -V_{mem} + I(t)
\end{align}
$$

좌변은 막전위에 대한 시간의 미분이다. 
즉, 시간의 변화에 따른 막전위의 변화인데, 막전위의 변화 그래프에서의 기울기로 볼 수 있다.

미분한 값이 기울기이므로 막전위가 증가/감소하는 경사도를 나타낸다.
미분한 값이 양의 방향으로 크다면 가파르게 막전위가 증가하는 것이고 
미분한 값이 음의 방향으로 크다면 가파르게 막전위가 감소하는 것이다.

우변은 막전위와 입력의 차이이다.
입력이 현재 막전위보다 더 크다면, 즉 $V_{mem} < I(t)$ 이라면,
$V_{mem}$을 이항하면
$0 < -V_{mem} + I(t)$ 이다.
우변이 양수이므로 좌변의 미분한 값도 양수인데, 
기울기가 양수라는 것은 증가하고 있다는 말이다.

직관적으로 생각해봐도 입력이 현재 전위보다 높으면 입력이 들어왔으니 전위는 증가한다.
따라서 위 식이 나타내는 의미는 "입력과 현재 막전위의 차이만큼 기울기가 책정된다" 가 된다.
막전위와 입력의 차이(부호 상관 없이 절대값을 말한다)가 크다면 기울기의 절대값이 크므로 가파르게 증감하는 것이다.
막전위와 입력의 차이가 크면 빠르며 그 차이를 좁히는 방향으로, 차이가 적으면 천천히 그 차이를 좁히는 방향으로 막전위가 변화한다.
만약 입력이 현재 막전위보다 작아서(혹은 입력이 없어서) $V_{mem} > I(t)$ 이라면  $0 > -V_{mem} + I(t)$으로 음수이다.
우변(차이)이 음수이므로 좌변(기울기)도 음수이며, 입력이 적거나 없을 때 감소한다는 뜻이다. Leaky의 의미가 이 감소를 말한다.

그리고 좌변의 미분값에 곱해진 타우($\tau_m$)는 감가율인데, 이 값이 클수록 값이 감소하는 Leaky한 효과가 커진다.
그리고 LIF 뉴런으로 들어오는 입력 $I(t)$는 아래 식으로 표현된다.

$$
\begin{align} 
\tag{2}
I(t) = \sum_{i=1}^{n^l} \big( w_i \sum_{K}^{} \theta_i(t - t_k)   \big)
\end{align}
$$

i는 이전 뉴런의 번호를 나타낸다. 즉 i번째 이전 뉴런(i-th pre-neuron)을 말한다.
$n^l$은 l번째 레이어(층)의 뉴런 개수를 말한다. i번째 뉴런부터 $n^l$번째(마지막) 뉴런까지, 모든 이전 뉴런에 대하여 괄호 안의 값을 다 더하라는 것이다.
괄호 안의 $w_i \sum_{k}^{} \theta_i(t - t_k)$ 에서 세타함수 $\theta_i(t-t_k)$는 i번째 이전 뉴런에서 입력으로 들어오는 스파이크에 관한 함수이다. 
이 스파이크들을 모두 더해서 i번째 가중치 $w_i$와 곱한다.
이것을 모든 뉴런에 대해 다 더한다는 말이다.
세타함수가 스파이크에 관한 함수라고 했는데, 아래처럼 표현된다.

$$
\begin{align} 
\tag{3}

\theta_i(t-t_k) = \begin{cases}
      1 \; (t = t_k)\\
      0 \; (t \neq t_k)
    \end{cases}

\end{align}
$$

$t_k$는 이전 뉴런에서 스파이크가 일어난 시간이다.
현재 시간 t가 스파이크가 일어난 시간 $t_k$와 같으면 1이 된다는 뜻인데, 스파이크가 하나 들어온다는 것은 1만큼의 입력이 들어온다는 것이다.

pre-neuron(이전 뉴런)에서의 입력과 가중치가 곱해진 최종적인 현재 뉴런의 입력을 $net_j^l(t)$라고 하자.
l번째 레이어의 j번째 뉴런이며, 이것은 현재 뉴런이다.(j-th post-neuron)
이것을 아래와 같이 구할 수 있다.

$$
\begin{align} 
\tag{4}
net_j^l(t) = \sum_{i = 1}^{n^{l - 1}} w_{ij}^{l - 1} x_i^{l - 1}(t)
\end{align}
$$

$$
\begin{align} 
\tag{5}
x_i^{l - 1}(t) = \sum_{t}^{} \sum_{k}^{} \theta_i^{l - 1}(t-t_k)
\end{align}
$$

위 식을 설명하면 $x_i^{l - 1}(t)$는 i번째 이전 뉴런(l-1번째 레이어이므로 이전 뉴런이다)에서 들어온 입력의 합이다.
$w_{ij}^{l-1}$는 i번째 이전 뉴런에서 j번째 현재 뉴런으로의 가중치이다.
i번째 이전 뉴런의 입력 $x$와 $j$번째 현재 뉴런으로의 입력 $net$은 $i$에서 $j$로의 가중치 $w$와 곱해져 더해진다.
그리고 현재 뉴런의 활성화정도(출력)를 $a$로 나타내는데, 아래처럼 표현된다.

$$
\begin{align} 
\tag{6}
a_j^{l}(t) = \sum_{t}^{} \sum_{k}^{} \theta_j^{l}(t-t_k)
\end{align}
$$

$j$번째 현재 뉴런의 출력을 $a_j^{l}(t)$로 나타냈다.
그리고 마지막 레이어인 $L$번째 레이어에서 최종적 신경망의 출력은 LIF 뉴런에서처럼 스파이크를 일으키지 않는다.
물론 마지막 레이어에서 입력은 스파이크로 들어오지만, 마지막 레이어의 출력은 스파이크가 차곡차곡 쌓이는 형태를 띈다. 
스파이크가 쌓인다고 표현했지만 실제로 뉴런이 발화하지 않으므로 막전위가 될 것이다.
시간이 T개의 time step으로 이루어져 있다고 할 때, 아래와 같이 출력이 나온다.

$$
\begin{align} 
\tag{7}
output = \frac{V_{mem}^L(T)}{T}
\end{align}
$$

이렇게 스파이크가 아닌 막전위를 시간의 스텝 수로 나눈 것이 최종 레이어의 출력이다.
여기까지가 해당 논문에서 스파이킹 신경망의 순전파(Feedforward)로서 다룬 부분이다.
이제 역전파(Backpropagation)를 통해 가중치를 업데이트할 편미분계수를 찾는 과정을 다룰 것이다.

우선 역전파의 기본인 연쇄법칙을 통한 각 레이어에 대한 편미분으로의 분해를 구하도록 할 것이다. 
E는 에러이고, a, net, w 는 앞에서 설명한 것과 같다.
$a_{LIF}$는 LIF 뉴런의 출력을 말하는 것이고, 후에 IF 뉴런의 출력과 혼동하지 않기 위함이다.

$$
\begin{align} 
\tag{8}
\frac{\partial E}{\partial w^l} = \frac{\partial E}{\partial a_{LIF}} \frac{\partial a_{LIF}}{\partial net} \frac{\partial net}{\partial w_l}
\end{align}
$$

위처럼 오차에 대한 가중치의 편미분을 3개로 분리했다.
손실함수로 평균 제곱 오차를 사용하면 오차에 대한 출력의 미분은 정답에서 출력을 뺀 것으로 결정된다.

$$
\begin{align} 
\tag{9}
\frac{\partial E}{\partial output} &= \frac{\partial \frac{1}{2} (output - target)^2}{\partial output} \\ 
                                    &= output - target \\ 
                                    &= e
\end{align}
$$

(9)와 같이 정답과 출력의 차이가 미분한 값이 되며, 이를 e라고 칭하자.
그리고 출력에 대한 입력의 미분은 아래처럼 계산된다.

$$
\begin{align} 
\tag{10}
\frac{\partial output}{\partial net} &= \frac{\partial \frac{1}{T}V_{mem}^L(T)}{\partial net} \\ 
                                      &= \frac{\partial \frac{1}{T}net^L(T)}{\partial net} \\ 
                                      &= \frac{1}{T}
\end{align}
$$

(10)에 따라 $\frac{1}{T}$로 미분이 된다.
 $net = wx$ 이므로,

$$
\begin{align} 
\tag{11}
\frac{\partial net}{\partial w} = x
\end{align}
$$

이제 은닉층에서의 오차 역전파를 보겠다. 
은닉층에서는 IF 뉴런을 통해 LIF 뉴런의 미분을 근사한다.
IF 뉴런은 값이 Leak 되는 것 없이 입력에 따라 증가하기 때문에, 이것을 미분하고, LIF와의 관계를 통해 LIF의 미분의 근사치를 얻어내겠다는 것이다.

IF 뉴런과 LIF 뉴런의 막전위 그래프를 그려보면 아래와 같다.

![](/assets/image/IF_LIF.png)
Figure 2: IF neuron & LIF neuron의 막전위 양상

(a)는 IF 뉴런, (b)는 LIF 뉴런이다.
검은색은 뉴런의 막전위를 나타내고 빨간색은 뉴런의 활성화(출력)을 나타낸다.
하지만 실제로 뉴런의 출력이 저 빨간 선을 따라 증가하지 않는다. 
뉴런의 출력은 0 또는 1로 이루어지는 스파이크로서, 이산적인 형태를 띈다. (세타함수를 보면 알 수 있다)
이런 스파이크를 함수로 나타내면 미분 불가능한 함수이지만, 이것을 근사하게 미분이 가능하도록 유사미분을 진행한다. 
위 그래프의 빨간 선에서 점선은 실제로 존재하지 않지만, 존재한다고 가정한 것이다. 
실제로는 저렇게 일정 기울기를 가지는 직선이 아니라 미분 불가능한 이산적 스파이크다.

우리는 IF 뉴런에서 뉴런의 활성화 정도를 빨간 점선으로 가정했는데, 이것의 기울기는 $\frac{1}{V_{th}}$이다.
그리고 LIF 뉴런의 경우, Leaky한 효과 때문에 값이 약간씩 감소한다. 
즉, 더 많은 양이 입력이 되어야 임계치를 넘길 수 있는 것이다. 
따라서 LIF 뉴런의 활성화 정도를 빨간 점선으로 가정했을 때, 기울기는 IF에 비해 조금 더 작은 $\frac{1}{V_{th} + \epsilon}$로 결정된다. 
엡실론은 어떤 양수 값이며, 엡실론 시간만큼 입력이 들어오는 시간이 증가해야 임계치를 LIF 뉴런이 넘길 수 있다는 것이다.

$V_{th} + \epsilon$ 만큼의 시간이 흘렀을 때, 뉴런의 총 막전위를 IF와 LIF에 대해 각각 $V_{mem}^{total, IF}, \; V_{mem}^{total, LIF}$ 라고 한다. 
실제로는 LIF에서는 이제 막 임계치를 넘겼고, IF에서는 이미 임계치를 넘겨 스파이크가 일어났기 때문에 IF total 값은 존재하지 않는다. 
그리고 엡실론을 더한 만큼 IF가 더 크다고 했는데, 이것을 비율로서 1보다 큰 베타를 곱한 만큼 IF가 크다고 말할 수 있다. (그림 c 참조)
이 관계를 식으로 나타내면 아래가 된다.

$$
\begin{align} 
\tag{12}
V_{th} + \epsilon = \beta V_{th}
\end{align}
$$

그리고 IF 뉴런의 막전위는 아래처럼 표현된다.

$$
\begin{align} 
\tag{13}
V_{mem}(t) \approx \sum_{i=1}^{n} \big( w_i x_i(t) \big) - V_{th}a_{IF}(t)
\end{align}
$$

가중치와 입력을 곱해서 더한 후, 뉴런의 출력 합계와 임계값을 곱해서 빼준다. 
n은 이전 뉴런의 개수, $i$는 $i$번째 이전 뉴런을 지칭하는 수이다. 
$V_{th} a_{IF}(t)$는 스파이크/초기화 활동을 계산한다. 
막전위 $V_{mem}$를 0으로 취하면 아래와 같이 정리된다.

$$
\begin{align} 
\tag{14}
a_{IF} \approx \frac{1}{V_{th}} \sum_{i=1}^{n} \big( w_i x_i(t) \big) = \frac{1}{V_{th}} net(t)
\end{align}
$$ 

$V_{th}$로 나누면 위의 식처럼 정리가 된다. 또한 $wx$는 입력($net$)이다. 

$$
\begin{align} 
\tag{15}
\frac{\partial a_{IF}}{\partial net} \approx \frac{1}{V_{th}} \cdot 1 = \frac{1}{V_{th}}
\end{align}
$$

$a_{IF}$는 $net$에 대한 식으로 정리가 가능하므로, 그에 따라 위의 식처럼 미분한 값이 $\frac{1}{V_{th}}$가 된다.

$$
\begin{align} 
\tag{16}
a_{IF} \approx \frac{1}{V_{th}} net(t) \approx \frac{1}{V_{th}} V_{mem}^{total, IF}
\end{align}
$$ 

또한 IF 뉴런의 경우, 총 막전위가 총 입력($net$)과 거의 근사치이다. 입력이 들어온 만큼 막전위가 증가하는 단조로운 증가 양상을 띈다.

$$
\begin{align} 
\tag{17}
\frac{\partial a_{IF}(t)}{\partial t} \approx \frac{1}{V_{th}} \frac{\partial V_{mem}^{total, IF}(t)}{\partial t}
\end{align}
$$

위의 식은 IF 뉴런의 출력에 대해 시간으로 미분한 것이 IF 뉴런의 총 막전위를 시간으로 미분한 것에 1/threshold 를 곱해준 것과 같다는 말이다. 
IF 뉴런의 출력 스파이크에 대한 미분을 총 막전위에 대한 미분으로 표현했다. 
뉴런의 출력은 이산적 스파이크이므로 원래는 미분이 불가능하지만, 근사치를 구해 역전파를 수행하기 위함이다.

그리고 이산적인 스파이크에 대해 시간으로 미분한 것은 스파이크의 빈도를 나타내는 함수 $rate(t)$로 나타낼 수 있다. 
$rate(t)$ 함수는 $\frac{a(t)}{t}$이며, $a(t)$가 스파이크의 횟수일 때, $t$로 나눔으로서 빈도를 구한다.
이것은 상수 스파이크 시간일 때, $a(t)$를 $t$로 미분한 것과 같다. 
따라서 IF 뉴런의 총 막전위와 출력에 대한 미분으로 LIF에서의 값도 근사할 수 있다.

$$
\begin{align} 
\tag{18}
\frac{\partial V_{mem}^{total, IF}(t)}{\partial t} \approx V_{th} \frac{\partial a_{IF}(t)}{\partial t} \approx V_{th} rate(t)
\end{align}
$$

위처럼 rate 함수로서 나타낼 수 있다.
그리고 LIF의 Leaky 효과를 나타내기 위해 $f(t)$를 이용해 아래처럼 LIF의 총 막전위에 대한 미분을 나타낸다.

$$
\begin{align} 
\tag{19}
\frac{\partial V_{mem}^{total, LIF}(t)}{\partial t} \approx V_{th} rate(t) + V_{th}\frac{\partial f(t)}{\partial t}
\end{align}
$$

$f(t)$는 아래와 같이 Leaky한 뉴런을 나타내는데 쓰일 수 있다.

$$
\begin{align} 
\tag{20}
f(t) = \sum_{k}^{} exp(- \frac{t-t_k}{\tau_m})
\end{align}
$$

위처럼 미분으로서 지수적인 Leaky decay effect를 낼 수 있는 것이다. 
그리고 $f(t)$는 미분할 때 우극한을 이용해 유사미분을 한다. ($t \rightarrow t_k^{+} $)
그리고 위에서 말한 LIF와 IF의 비율 관계에 따라 아래처럼 정리된 식이 있다.

$$
\begin{align} 
\tag{21}
\frac{\partial a_{IF}(t)}{\partial t} \approx \frac{1}{V_{th}} \frac{\partial V_{mem}^{total, IF}(t)}{\partial t} = \beta \frac{1}{V_{th}} \frac{\partial V_{mem}^{total, LIF}(t)}{\partial t}
\end{align}
$$

이제 위의 몇개의 식들을 풀면 아래처럼 비율 $\beta$와 관련된 식을 도출할 수 있다. 

$$
\begin{align} 
\tag{22}
\frac{1}{\beta} = 1 + \frac{1}{rate(t)} \frac{\partial f(t)}{\partial t}
\end{align}
$$

위 식에서 IF의 평균적 입력에 LIF의 Leaky한 효과를 추가하기 위한 두번째 항을 더하여 $\frac{1}{\beta}$를 구한다. IF가 LIF의 $\beta$배 이므로, 그 역수는 IF에서 LIF로의 비율이다. 이 비율이 IF의 입력에 Leaky를 더한 것과 같다. 
그리고 역전파 과정에서 기울기 소실 현상을 방지하기 위해 Leaky 항을 T로 나누어주게 되는데, f의 미분에 감마로 나눈 값으로 미분이 된다.
감마는 총 순전파 시간동안 특정 뉴런에 대한 스파이크 출력의 수를 나타낸다.
그리고 아래처럼 정리가 가능하다.

$$
\begin{align} 
\tag{23}
\frac{\partial a_{LIF}}{\partial net} = \frac{1}{V_{th} + \epsilon} = \frac{1}{\beta V_{th}} \approx \frac{1}{V_{th}} \big(1 + \frac{1}{\gamma} f'(t) \big) = \frac{1}{V_{th}} \big(1 + \frac{1}{\gamma} \sum_{k}{}-\frac{1}{\tau_m} e^{- \frac{t-t_k}{\tau_m}} \big)
\end{align}
$$

이제 정리를 해보겠다. 
마지막 층 $L$에서의 오차 기울기 $\delta^L$는 우리가 이미 구했듯이 아래와 같다.

$
\begin{align} 
\tag{24}
\delta^L = \frac{\partial E}{\partial output} \frac{\partial output}{\partial net^L} = e \frac{1}{T} = \frac{e}{T}
\end{align}
$$

그리고 은닉층에서의 오차 기울기 $\delta^l$는 아래처럼 쓸 수 있다.

$$
\begin{align} 
\tag{25}
\delta^l = \big( (w^l)^{Tr} \cdot \delta^{l+1} \big) * a'_{LIF}(net^l)
\end{align}
$$

**note** $\cdot$는 행렬곱이며, $\ast$은 원소별 곱이다.
이제 아래처럼 경사하강을 통한 업데이트를 할 수 있다.

$$
\begin{align} 
\tag{26}
\frac{\partial net}{\partial w^l} = \frac{\partial w^l x^l(t)}{\partial w^l} = x^l(t)
\end{align}
$$

$$
\begin{align} 
\tag{27}
\frac{\partial E}{\partial w^l} = x^l(t) * (\delta^{l+1})^{Tr}
\end{align}
$$

$$
\begin{align} 
\tag{28}
w^l ; \colon= w^l - \alpha \frac{\partial E}{\partial w^l}
\end{align}
$$

<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>
<br/>

[구현 코드 GitHub](https://github.com/Intipy/spike-based-backpropagation)










