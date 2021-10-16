---
title:  "Enabling Spike-Based Backpropagation For Training Deep Neural Network Architectures"

categories:

  - Paper Review
last_modified_at: 2021-10-12T08:06:00-05:00

---



해당 글은
<br/>
Enabling Spike-Based Backpropagation For Training Deep Neural Network Architectures 
<br/>
(https://arxiv.org/abs/1903.06379) 
<br/>
라는 논문을 소개하는 글이다.
<br/>
<br/>
<br/>
<br/>
**ABSTRACT**
<br/>
*스파이킹 신경망(SNN)는 최근 눈에 띄는 신경 컴퓨팅 패러다임으로 부상하고 있다.
일반적인 얕은 SNN 아키텍처는 복잡한 표현을 표현할 수 있는 양이 제한적이지만, 입력 스파이크를 사용하여 심층 SNN을 학습시키는 것은 지금까지 성공하지 못했다. 
이 문제를 해결하기 위해 기성 훈련된 심층 인공 신경망을 SNN으로 전환하는 것과 같은 다양한 방법이 제안되었다. 
그러나 ANN-SNN 변환 체계는 스파이킹 시스템의 시간의 역학적 특성을 포착하지 못한다. 
한편, 스파이크 생성 함수의 불연속적이고 차별화 불가능한 특성 때문에 입력 스파이크 이벤트를 사용하여 심층 SNN을 직접 학습시키는 것은 여전히 어려운 문제이다. 
이 문제를 극복하기 위해 LIF 뉴런의 Leaky Behavior를 설명하는 대략적인 파생 방법을 제안한다. 
이 방법은 스파이크 기반 역전파를 사용하여(입력 스파이크 이벤트를 통해) 심층 컨볼루션 SNN을 직접 학습시킬 수 있다. 
우리의 실험은 스파이크 기반 학습으로 훈련된 다른 SNN에 비해 MNIST, SVHN 및 CIFAR-10 데이터 세트에서 최고의 분류 정확도를 달성함으로써 심층 네트워크(VGG 및 잔류 아키텍처)에 대한 제안된 스파이크 기반 학습의 효과를 보여준다. 
또한 스파이킹 도메인에서 추론 연산을 위해 제안된 SNN 훈련 방법의 효과를 입증하기 위해 희소 이벤트 기반 계산을 분석한다.*






