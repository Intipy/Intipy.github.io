---
title:  "How Does Batch Normalization Help Optimization"

categories:

  - Paper Review
last_modified_at: 2021-10-12T08:06:00-05:00

---


해당 글은
<br/>
[How Does Batch Normalization Help Optimization](https://arxiv.org/abs/1805.11604) 
<br/>
라는 논문을 소개하는 글이다.
<br/>
<br/>
<br/>
<br/>
**ABSTRACT**
<br/>
*"배치 정규화는 심층 신경망의 빠르고 안정적인 훈련을 가능케 만드는 널리 채택된 기술이다. 이런 확산에도 불구하고, 배치 정규화의 효과에 대한 정확한 이유는 여전히 잘 이해되지 않는다. 이러한 효과는 소위 "내부 공변량 이동"을 줄이기 위해 훈련 중에 층의 입력 분포의 변화를 제어하는 것에서 비롯된다는 것이 일반적인 견해이다. 이 연구에서 우리는 레이어의 입력 분포 안정성이 배치 정규화의 성공과 거의 관련이 없다는 것을 보여준다. 대신, 우리는 배치 정규화가 학습에 미치는 근본적인 영향을 파악한다. 최적화 환경을 훨씬 부드럽게 만든다는 것이다. 이러한 부드러움은 기울기의 보다 예측 가능하고 안정적인 동작을 유도하여 더 빠른 훈련을 가능하게 한다."* 
<br/>
<br/>
<br/>
<br/>

일반적으로 배치 정규화의 효과는 내부 공변량 변화(Internal Covariate Shift, ICS)를 감소시킴으로서 야기된다고 알려져 있다. 하지만 해당 논문에서는 이러한 원인이 효율적인 학습을 만들지 않는다고 주장한다.   

이러한 주장을 위해 해당 논문에서는 배치 정규화와 ICS의 상관관계, 그리고 ICS 감소와 학습 성능 향상의 상관관계, 이렇게 두가지를 파악한다. 

(Q.1) ICS의 감소가 학습 성능 향상에 도움을 주는가? 
<br/>
(Q.2) 배치 정규화가 ICS 감소에 도움을 주는가? 

먼저 Q.1의 의문에 대하여 실험을 진행한다. 

![](/assets/image/bn_ex1.png)

위의 실험 결과에서 볼 수 있듯이 배치 정규화를 사용했을 때 성능이 크게 향상되지만, 배치 정규화를 사용했을 때와 그렇지 않을 때의 분포 차이는 뚜렷하지 않다. 이로서 우리는 ICS가 학습에 악영향을 미친다는 주장의 신빙성을 의심하게 된다. 

그리고 우리는 아래와 같은 실험을 설계한다. 

1. 배치 정규화를 진행한 후 노이즈를 주입한다. 
2. 이러한 노이즈 주입은 분포를 불안정하고 가변적으로 만들어 ICS 증가에 큰 기여를 할 것이다.
3. ICS 증가에 따른 학습 성능의 변화를 관찰한다.

![](/assets/image/bn_ex2.png)

배치 정규화 이후 노이즈가 주입되었더라도 일반적인 배치 정규화 수행 후의 성능과 비교하여 큰 차이를 보이지 않으며, 둘 모두 배치 정규화를 사용하지 않은 경우에 비해 높은 성능을 보여주고 있다. 위 실험으로 우리는 ICS가 학습 성능에 거의 영향을 미치지 않는다는 알 수 있다. 

이제 우리는 Q.2의 의문에 답하기 위하여 실험을 진행한다. 

아래는 코사인 각도와 기울기의 $l_2-difference$를 기준으로 하여서, 배치 정규화를 사용했을 때와 사용하지 않았을 때의 ICS 차이를 측정한 것이다. 

![](/assets/image/bn_ex3.png)

이상적인 ICS(적은 ICS)에서, 코사인 각도는 이상적으로 1, $l_2-difference$는 이상적으로 0의 값을 가진다. 하지만 위 실험 결과로 배치 정규화를 사용했을 때 비슷하거나, 오히려 더 안좋은 ICS를 가진다는 것을 확인할 수 있다. 이로서 우리는 배치 정규화와 ICS 감소의 상관관계에 의문을 품게 된다. 

아래와 같은 ICS 측정을 제안한다. 

$L$: 손실

$W_1^{(t)}, … , W_k^{(t)}$: k개의 레이더에 대하여 각 레이더의 매개변수

$x^{(t)}, y^{(t)}$: 시간 t에서 네트워크를 훈련시키는 데 사용되는 입력 라벨 쌍의 배치

시간 t에서 activation i의 ICS를 다음과 같이 정의한다. 

$ICS \; = \; {\parallel G_{t, i} \; - \; G'_{t, i} \parallel}_2$

G는 레이어의 기울기(Gradient)를 말하며, 위 식은 레이어의 학습 전과 후의 기울기의 $l_2-norm$을 ICS로 정의하겠다는 것이다. 각 G는 아래처럼 나타낼 수 있다. 

${G}_{t,i} = {\nabla}_{W_i^{(t)}} L \big( W_1^{(t)}, … , W_k^{(t)}; x^{(t)}, y^{(t)} \big)$

${G'}_{t,i} = {\nabla}_{W_i^{(t)}} L \big( W_1^{(t+1)}, … , W_{i-1}^{(t+1)}, W_i^{(t)}, W_{i+1}^{(t)}, ... , W_k^{(t)} ; x^{(t)}, y^{(t)} \big)$


![](/assets/image/bn_ex4.png)

위 결과는 VGG 네트워크에 대한 최적화 환경 분석이다. 우리는 기울기 방향으로 이동할 때 손실 및 기울기의 $l_2$ 변화에서 변동(음영 처리된 영역)을 측정한다. $"effective" \beta-smoothness$는 해당 방향으로 이동한 거리에 대한 경사도의 최대 차이($l_2 norm$)를 의미한다. 배치 정규화를 사용하는 네트워크에서 이 모든 측정값이 확실히 개선되어 더 잘 작동하는 모습을 보여준다. 여기서, 더 큰 step의 경우 표준 네트워크의 성능이 저하되기 때문에 최대 거리를 제한한다. 그러나 배치 정규화는 더 큰 거리에서도 Smoothing를 계속 제공한다.

우리는 배치 정규화 레이어가 있거나 없는 ICS의 범위를 측정한다. gradient stochasticity뿐만 아니라 비선형성의 영향을 분리하기 위해, 우리는 전체 배치 경사하강으로 훈련된 25층 심층 선형 네트워크(DLN)에 대해 이 분석을 수행한다. 배치 정규화에 대한 기존의 이해는 네트워크에 배치 정규화 레이어를 추가하면 G와 G’ 사이의 상관관계가 증가하여 ICS를 감소시켜야 한다는 것이다.  

놀랍게도, 배치 정규화를 사용하는 네트워크가 종종 ICS의 증가를 보인다는 것을 관찰했다. 이는 DLN의 경우 특히 두드러진다. 이 경우 표준 네트워크는 훈련 전체에 대해 거의 ICS를 경험하지 않는 반면, 배치 정규화 경우 G와 G'는 거의 상관관계가 없는 것으로 보인다. 배치 정규화 네트워크가 달성하는 정확도와 손실 측면에서 지속적으로 훨씬 더 나은 성능을 발휘하더라도, 최적화 관점에서 배치 정규화가 ICS를 감소시키지 않을 수 있음을 주장한다.



ICS가 학습에 도움을 주지 않을 뿐더러, 배치 정규화는 ICS를 감소시키지조차 못한다면, 배치 정규화는 어떻게 학습 성능을 향상시킬 수 있는 것인가?

첫번째는 손실함수의 Lipschitzness 개선이다. 기울기가 적은 속도로 변하고, 기울기의 크기 또한 작아진다. 즉, 손실이 더 나은 $"effective" \beta-smoothness$를 가진다. 이러한 smoothening 효과는 학습 성능에 큰 영향을 미친다. 그 예시로 바닐라(배치 정규화가 적용되지 않음) 심층 신경망에서 손실 함수는 non-convex일 뿐만 아니라 많은 수의 평평한 영역 및 날카로운 최소값을 갖는 경향이 있다. 이는 폭발하거나 소실되는 기울기로 인해 경사하강 알고리즘을 불안정하게 만들어 학습 속도 및 매개변수 초기화에 매우 민감하게 만든다. 즉, 배치 정규화는 기울기를 보다 신뢰할 수 있고 예측 가능하게 한다는 것이다. 결국, 기울기의 개선된 Lipschitzness는 계산된 기울기의 방향으로 더 큰 step을 밟을 때, 이 기울기 방향이 해당 step을 수행한 후, 실제 기울기 방향(전체적인 기울기의 방향)에 대한 상당히 정확한 추정치라는 확신을 줄 수 있다. 따라서 점진 기반 학습 알고리즘은 평평한 영역 또는 날카로운 국소 최소값과 같은 손실 함수의 공간에 갑작스러운 변화에 부딪힐 위험 없이 더 큰 step을 밟을 수 있다. 이는 결과적으로 더 큰 학습률과 그에 따른 빠른 학습을 사용할 수 있게 해주며 일반적으로 훈련을 상당히 빠르게 하고 초기 매개변수 선택에 덜 민감하게 만든다. 
공간을 매끄럽게 만듬으로서 최적화와 학습에 도움을 주는 것이다.

![](/assets/image/bn_ex5.png)

우리는 이제 Fully-Connected Layer 뒤에 배치 정규화를 삽입 후, 최적화 공간을 이론적으로 살펴볼 것이다. 

**이론적 결과**
<br/>
activation $y_i$에 대한 최적화 환경을 고려한다. 배치 정규화는 이미 최적화 환경에서 기울기의 예측 가능성과 Lipschitz 지속성에 유리한 성질을 유도하는 것을 보였다. 우리는 activation 최적화 환경이 weight 최적화 환경의 최악의 한계가 된다는 것을 보인다. 

먼저 손실의 Lipschitzness를 나타내는 기울기 규모 $\parallel \nabla_{y_j} L \parallel$ 에 집중해보자. 손실의 Lipschitz 상수는 step이 지나갈 때마다 손실이 변경될 수 있는 양을 제어하기 때문에 최적화에 중요한 역할을 한다. 우리는 특정 가중치 또는 사용 중인 손실에 대한 어떠한 가정도 없이, 배치 정규화된 환경이 더 나은 Lipschitz 상수를 나타낸다는 것을 보여준다. 더 나아가, activation $\hat{y_j}$가 기울기 $\nabla_{\hat{y_j}} \hat{L}$와 상관관계가 있거나 기울기의 평균이 0에서 벗어날 때마다 Lipschitz 상수가 크게 감소한다. 이 감소는 부가적이며, 배치 정규화의 scaling이 원래 레이어의 scaling과 동일한 경우에도 효과가 있다는 점에 유의하라. (즉, $\sigma_j = \gamma$일 때도)

**Theorem 4.1** 배치 정규화가 Lipschitzness에 미치는 효과. 배치 정규화가 수행된 네트워크의 손실 $\hat{L}$과 표준 네트워크의 손실 $L$에 대하여. 
${\parallel \nabla_{y_j} \hat{L} \parallel}^2 \leq \frac{\gamma^2}{\sigma_j^2} \bigg( {\parallel \nabla_{y_j} L \parallel}^2 - \frac{1}{m}{\langle 1,\nabla_{y_j} L \rangle}^2 - \frac{1}{m} {\langle \nabla_{y_j} L, \hat{y_j}\rangle}^2 \bigg)$ 

먼저, ${\langle 1,\nabla_{y_j} L \rangle}^2$는 차원 안에서 quadratically 하게 성장하므로 유의하다. 또한 변수에 대한 기울기가 변수 자체와 거의 상관관계가 없기 때문에 최종 내적 항 ${\langle \nabla_{y_j} L, \hat{y_j}\rangle}^2$은 0에서 멀리 떨어질 것으로 예상된다. 또한 $\sigma_j$는 커지는 경향이 있다. 따라서 $\frac{\gamma}{\sigma}$에 의한 scaling은 유효 Lipschitz 상수에서 볼 수 있는 상대적 "평탄도(Flatness)"에 기여할 수 있다. 우리는 이제 공간의 이차적 특성에 주목한다. 우리는 배치 정규화 레이어가 추가될 때, 기울기 방향의 activation에 관한 손실 헤시안(Hessian)의 2차 형태가 입력 분산에 의해 scale 되고(미니 배치 분산에 대한 복원력을 유도함) 추가 인자(Smoothness 증가)에 의해 감소함을 보여준다. 이 항은 현재 점 주위의 기울기 확장의 테일러 2차 항을 포착한다. 따라서, 이 항을 줄이면 첫 번째 순서 항(기울기)이 더 예측 가능하다는 것을 의미한다. 

**Theorem 4.2** 배치 정규화가 Smoothness에 미치는 효과. $\hat{g_j} = \nabla_{y_j} L$, 그리고 $H_{jj} = \frac{\partial L}{\partial y_j \partial y_j}$ , 이 둘을 레이어 출력에 대한 손실의 기울기와 헤시안이라고 하자.

그렇다면,

$ {\big( \nabla_{y_j} \hat{L} \big)}^\top \frac{\partial \hat{L}}{\partial y_j \partial y_j} \big( \nabla_{y_j} \hat{L} \big) \leq \frac{\gamma^2}{\sigma^2} {\bigg( \frac{\partial \hat{L}}{\partial y_j} \bigg)}^\top H_{jj} \bigg( \frac{\partial \hat{L}}{\partial y_j} \bigg) - \frac{\gamma}{m \sigma^2} \langle \hat{g_j} , \hat{y_j} \rangle {\parallel \frac{\partial \hat{L}}{\partial y_j} \parallel}^2$

만약 $H_{jj}$가 $\hat{g_j}$와 $\nabla_{y_j} \hat{L}$의 relative norms를 보존한다면, 

$ {\big( \nabla_{y_j} \hat{L} \big)}^\top \frac{\partial \hat{L}}{\partial y_j \partial y_j} \big( \nabla_{y_j} \hat{L} \big) \leq \frac{\gamma^2}{\sigma^2} {\bigg(  {\hat{g_j}}^\top H_{jj} \hat{g_j} - \frac{1}{m \gamma} \langle \hat{g_j} , \hat{y_j} \rangle  {\parallel \frac{\partial \hat{L}}{\partial y_j} \parallel}^2 \bigg)}$

$\langle \hat{y_j} , \hat{g_j} \rangle $가 non-negative일 때, 위의 정리에서 더 예측 가능한 기울기를 가진다. 손실이 국소적으로 볼록한 경우 헤시안은 양의 반정의(Positive
Semi-Definite, PSD)이며, 이는 조각별 선형(Piecewise Linear, PL) 활성화 함수와 최종 계층에서 볼록한 손실(표준 소프트맥스 교차 엔트로피 손실 또는 기타 등등)이다. 

조건 $\langle \hat{y_j} , \hat{g_j} \rangle > 0$은 음의 기울기 $\hat{g_j}$가 손실의 최소를 가리키는 한 유지된다(정규화된 activation에 대하여). 전반적으로, 이 두 조건이 유지되는 한, 배치 정규화는 더 예측적이다(우리가 실험적으로 관찰한 것과 유사). 우리의 결과는 단순한 스케일링이 아닌 문제의 reparametrization에서 비롯된다는 점에 유의한다.

**Observation 4.3** 배치 정규화는 scaling 이상의 기능을 수행한다. 모든 입력 데이터 $X$와 네트워크 구성 $W$에 대해 동일한 활성화 $y_j$를 초래하는 배치 정규화 구성$(W, \gamma, \beta)$이 존재한다. 따라서, 일반적인 공간의 모든 최소값이 배치 정규화 공간에서 보존된다. 즉, 최적화를 도우면서도 학습 성능에 악영향을 미치는 Smoothness의 개선은 일어나지 않는다. 

지금까지의 이론적 분석은 정규화된 activation에 대한 손실의 최적화 환경을 연구했다. 이제 이러한 경계를 레이어 가중치와 관련하여 최악의 경우로 변환할 것이다. 

**Theorem 4.4** 가중치 공간에 대한 minimax bound

$ g_j = \max\limits_{\parallel X \parallel \leq \lambda} {\parallel \nabla_W L \parallel}^2 , \; \; \; \hat{g_j} = \max\limits_{\parallel X \parallel \leq \lambda} {\parallel \nabla_W \hat{L} \parallel}^2 \Rightarrow \hat{g_j} \leq \frac{\gamma^2}{\sigma_j^2} \big( g_j^2 - m \mu_{g_j}^2 - \lambda^2 {\langle \nabla_{y_j} L, \hat{y_j} \rangle}^2 \big) \cdot$

배치 정규화는 이상적인 환경 외에서도 많은 이점을 제공한다. 

**Lemma 4.5** 배치 정규화는 매개변수 초기화에 이점을 제공한다. $W^{\ast}$과 $\hat{W^{\ast}}$를 각각 일반 및 배치 정규화 네트워크의 가중치에 대한 지역 최적값 집합이라고 하자. 모든 초기화 $W_0$에 대하여, 

${\parallel W_0 - \hat{W^{\ast}} \parallel}^2 \leq {\parallel W_0 - W^{\ast} \parallel}^2 - \frac{1}{{\parallel W^{\ast} \parallel}^2} {(\big({\parallel W^{*} \parallel}^2 - \langle W^{\ast}, W_0 \rangle \big)}^2$

만약 $\langle W_0, W^{\ast} \rangle > 0$, $\hat{W^{\ast}}$과 $W^{\ast}$는 각각 배치 정규화 및 표준 네트워크에서 가장 가까운 최적값이다.




