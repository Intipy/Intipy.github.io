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

![](/assets/image/mlp_conv_layer.jpg)

왼쪽은 일반적인 Convolution Layer이다. 
합성곱 연산을 통해 바로 Feature Map을 출력하는 구조이다. 
하지만 오른쪽은 NIN에서 제안한 Multi Layer Perceptron Convolution Layer 라는 구조이다. 
합성곱 연산 후 풀링, 신경망을 거쳐 Feature Map을 구하는 것이다. 
일반적인 하나의 합성곱 신경망 자체의 구조를 띄고 있어 Micro Network 라고도 한다. 
이는 일반적인 Convolution에 비하여 비선형성(Non-Linearity)을 더 추가하기 위함이다. 

![](/assets/image/inception_module.jpg)

그리고 위 사진처럼 1×1 Filter Convolution 층이 있는 걸 볼 수 있는데, 
이것은 GoogLeNet의 목적인 경량화된 신경망과 병목현상 제거에 필요한 차원 축소의 역할을 한다. 
그리고 1×1 Filter Convolution의 의미를 알아보기 위해 CCCP(Cascaded Cross Channel Pooling)를 알아보겠다. 

![](/assets/image/cross_channel.jpg)

CCCP(Cascaded Cross Channel Pooling)는 NIN에서의 풀링 기법으로, 
직렬적인 Channel의 묶음으로 픽셀 별 Pooling를 하는 것이다. 
Feature Map의 크기를 유지하며 Channel 개수를 줄이는 차원 축소의 효과를 얻기 위함이다. 
CCCP의 구조를 1×1 Filter Convolution으로 유사하게 표현 가능하기 때문에 이로서 차원축소를 GoogLeNet이 이용했다. 

<br/>
<br/>
<br/>
<br/>

이 신경망의 주요 아이디어는 Convolutional NN에서 Spase한, 밀집되지 않은 구조를 Dense, 밀집된 요소로서 근사하는 것이다. 
그림으로 나타낸 GoogLeNet은 아래와 같다. 

![](/assets/image/googlenet_.jpg)

보는 것처럼 모델이 매우 깊다. 
따라서 기울기 소실(Gradient Vanishing) 문제가 발생할 수 있기 때문에 Auxiliary Classifier를 중간 layer에 추가하였다. 
상대적으로 얇은 신경망의 강한 성능은 신경망의 중간 layer에서 생성된 특징이 매우 차별적이라는 것을 나타낸다. 
이 중간 계층에 Auxiliary Classfiers를 추가하여 낮은 단계에서 차별점을 이용할 수 있다는 것과 뒤로 전달되는 기울기 신호를 증가하고, 추가적인 Regularization 효과를 기대한다고 말한다.

 











