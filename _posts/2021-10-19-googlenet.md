---
title:  "Going Deeper With Convolutions"

categories:
  - Paper Review
last_modified_at: 2021-10-12T08:06:00-05:00
---


해당 글은 
<br/>
[Going Deeper With Convolutions](https://arxiv.org/abs/1409.4842)
<br/>
라는 논문을 소개하는 글이다.

<br/>
<br/>
<br/>
<br/>

**ABSTRACT**
<br/>
*우리는 인셉션이라는 이름의 심층 컨볼루션 신경망 아키텍처를 제안하며, 
이는 ImageNet Large-Scale Visual Recognition Challenge 2014(ILSVRC14)에서 분류 및 탐지를 위한 새로운 기술 상태를 설정하는 역할을 담당했습니다. 
이 아키텍처의 주요 특징은 네트워크 내부의 컴퓨팅 리소스 활용도 향상입니다. 
이는 계산 예산을 일정하게 유지하면서 네트워크의 깊이와 폭을 늘릴 수 있도록 세심하게 조작된 설계에 의해 만들어졌습니다. 
품질을 최적화하기 위해, 아키텍처 결정은 Hebbian 원리와 다중 스케일 프로세싱의 직관성에 기초했습니다. 
ILSVRC14에서 사용된 우리의 특별한 신경망은 22층의 심층 네트워크인 GoogLeNet이라고 불리며, 
해당 신경망의 장점은 분류와 탐지의 맥락에서 평가됩니다.*

<br/>
<br/>
<br/>
<br/>

GoogLeNet은 2014년도 ILSVRC14 우승 모델이다. 
당시 딥러닝 모델들의 정확도 한계를 레이어의 크기와 개수를 늘림으로서 해결하려 했으나, 오버피팅 또는 너무 많은 파라미터로 인한 리소스 낭비가 많았다. 
GoogLeNet은 파라미터를 줄이고 정확도를 높이는 것을 핵심적인 목표로 삼았다.
또한 Network In Network, NIN 구조에서 영향을 받았는데, 
이름 그대로 네트워크 안에 또다른 네트워크가 있는 구조이다. 
NIN에서 제안된 개념 중 하나로 MLP Convolution Layer 라는 것이 제안되었다. 
아래 그림을 통해 설명하겠다. 

![](/assets/image/mlp_conv_layer.png)



