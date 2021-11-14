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



