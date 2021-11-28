---
title:  "Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift"

categories:

  - Paper Review
last_modified_at: 2021-10-12T08:06:00-05:00

---



해당 글은
<br/>
[Batch Normalization: Accelerating Deep Network Training by Reducing Internal Covariate Shift](https://arxiv.org/abs/1502.03167) 
<br/>
라는 논문을 소개하는 글이다.
<br/>
<br/>
<br/>
<br/>
**ABSTRACT**
<br/>
*"심층 신경망의 학습은 이전 계층의 매개변수가 변경됨에 따라 학습 중에 각 계층의 입력 분포가 변경된다는 사실로 인해 복잡하다. 이는 낮은 학습률과 신중한 매개변수 초기화를 요구하여 학습을 느리게 하며, 포화 비선형성으로 모델을 학습시키는 것이 어렵다. 우리는 이 현상을 내부 공변량 이동이라고 하며 계층 입력을 정규화하여 문제를 해결한다. 우리의 방법은 정규화를 모델 아키텍처의 일부로 만들고 각 학습 미니 배치에 대한 정규화를 수행하는 것에서 강점을 얻는다. 배치 정규화를 사용하면 훨씬 더 높은 학습 속도를 사용하고 초기화에 덜 주의를 기울일 수 있다. 또한 정규화의 역할을 수행하며, 경우에 따라 중퇴할 필요가 없어진다. 최첨단 이미지 분류 모델을 적용한 배치 정규화는 14배 적은 학습 단계로 동일한 정확도를 달성하고 원래 모델을 상당한 차이로 능가한다. 일괄 표준화 네트워크의 앙상블을 사용하여 ImageNet 분류에 대해 가장 잘 발표된 결과를 개선한다. 즉, 4.9%의 유효성 검사 오류(및 4.8%)에 도달하여 인간의 정확도를 초과한다."*
<br/>
<br/>
<br/>
<br/>

배치 정규화(Batch Normalization)는 신경망 학습의 효율적인 동작을 위한 일종의 정규화이다. 

배치 정규화는 신경망의 학습에서 발생하는 대표적인 문제인 과적합(Overfitting), 기울기 소실(Vanishing Gradient), 초기 매개 변수 설정(Initial Parameter Setting) 등을 해결할 수 있다. 
배치 정규화는 높은 학습률(Learning Rate)를 적용 가능하게 하여 학습 속도를 증가시키며, 신경망의 학습이 매개 변수 초기화에 민감하게 반응하지 않도록 만들어준다. 
또한 Regularizer의 역할로서 과적합을 방지하여 Drop Out의 필요성을 감소시킨다.

해당 논문에서 신경망의 학습이 느려지는 이유로 내부 공변량 변화(Internal Covariate Shift, ICS)를 제시했다.
먼저, 공변량 변화(Covariate Shift)란 학습 데이터와 테스트 데이터의 분포가 달라서 예측에 문제가 생기는 것이다. 
신경망이 학습 데이터의 분포에 맞춰 학습을 했으니 이와 크게 상반되는 분포의 테스트 데이터를 예측하려면 문제가 발생하는 것이다.

내부 공변량 변화(ICS)는 이러한 공변량 변화가 신경망의 레이어 사이에서 발생하는 것이다. 
앞 레이어의 매개 변수가 변하면서 각 레이어에 들어오는 입력의 분포가 변하게 되며, 각 레이어들이 이러한 분포의 변화에 적응해야 하므로 학습이 잘 되지 않는다는 것이다.
따라서 각 레이어로 들어가는 입력의 분포를 정규화하여 일정하게 만들어준다면, 내부 공변량 변화가 감소하고 학습이 빨라질 것이라고 제안하였다.

해당 논문에서는 ICS를 감소시키기 위한 방법 중 하나로 Whitening 기법을 제안한다. 
Whitening의 과정으로, 데이터의 평균을 0으로 만든 후, 주성분 분석(Principal Component Analysis, PCA)을 이용하여 데이터의 상관관계를 감소시킨다. 
그리고 최종적으로 분산이 1이 되도록 하는데, 이렇게 데이터가 같은 scale을 가질 수 있도록 한다.

![](/assets/image/whitening.png)
   
이러한 Whitening을 각 레이어에 적용하면 ICS를 감소시킬 수 있다고 말한다. 
하지만 문제가 몇가지 있는데, 해당 전처리 과정에서는 공분산 행렬의 계산을 비롯해 굉장히 큰 계산 비용이 든다. 
또한 일부 매개 변수가 출력에 미치는 영향이 제거되어 경사하강법의 효과를 감소시킨다. 

따라서 논문에서는 다음과 같은 정규화 방안을 제시한다.

![](/assets/image/bn.png)

$\mu_{B} \leftarrow \frac{1}{m} \sum_{i=1}^{m} x_i$

위 식은 m개의 미니 배치에 대하여 데이터의 평균을 구한 것이다. 

$\sigma_B^2 \leftarrow \frac{1}{m} \sum_{i=1}^{m} (x_i-\mu_B)^2$

$\sigma_B^2$은 미니 배치에 대하여 구한 분산(variance)이다. 

이제 아래와 같이 정규화를 할 수 있다. 

$\hat{x_i} \leftarrow \frac{x_i-\mu_B}{\sqrt{\sigma_B^2} + \epsilon}$

엡실론을 더해준 것은 분산이 0으로 수렴할 때 무한대로 발산하는 것을 막기 위함이다.

그런데, 이렇게 정규화를 했을 때 단점이 하나 있다. 
우선, 정규화는 활성화 함수(Activation Function) 이전에 시행된다. 
이렇게 정규화를 수행 했을 때 활성화 함수의 입력, 즉 정의역이 좁아지면서 비선형성(Non-Linearity)을 잃게 된다. 
Sigmoid 함수를 예로 들면 아래의 빨간 구역처럼 선형에 가까운 함수가 나타난다. 

![](/assets/image/sigmoid_linear.png)

이것을 벗어나기 위해 Scale과 Shift를 하는데, 이것을 담당하는 것이 Learnable Parameter,  $\gamma, \; \beta$ 이다. 
이것을 이용하면 최종적으로 배치 정규화 수식은 아래와 같다. 

$y_i = \gamma \hat{x_i} + \beta$

$\gamma$는 Scale을,  $\beta$는 Shift를 해준다. 
이것이 논문에 나온 배치 정규화 방식이다. 

그리고 $\gamma, \; \beta$는 앞서 말했듯이 Learnable Parameter, 즉 학습 가능한 매개 변수이므로 손실 함수에 대해 미분하여 경사하강을 진행해야 할 것이다.
그 과정은 아래와 같이 나타낼 수 있다. 

$\frac{\partial L}{\partial \hat{x_i}} = \frac{\partial L}{\partial y_i} \cdot \gamma$

$\frac{\partial L}{\partial \sigma_B^2} = \sum_{i=1}^{m} \frac{\partial L}{\partial \hat{x_i}} \cdot (x_i - \mu_B) \cdot \frac{-1}{2}( \sigma_B^2 + \epsilon)^{-3/2}$

$\frac{\partial L}{\partial \mu_B} = (\sum_{i=1}^{m} \frac{\partial L}{\partial \hat{x_i}} \cdot \frac{-1}{\sqrt{\sigma_B^2+\epsilon}}) + \frac{\partial L}{\partial \sigma_B^2} \cdot \frac{\sum_{i=1}^{m} -2(x_i - \mu_B)}{m}$

 




 








 


 







