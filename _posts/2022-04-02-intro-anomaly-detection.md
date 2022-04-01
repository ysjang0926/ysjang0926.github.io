---
layout: post
title:  "Introduction to Anomaly Detection"
subtitle:   "Introduction to Anomaly Detection"
categories: data
tags: dl
comments: true
use_math: true
---

- 이번 글은 제가 요즘 제일 관심을 가지고 있는 Anomaly Detection(이상 탐지)의 개념과 관련 용어에 대해 정리해보았습니다. 사내 스터디 주제로 발표하기로 한 내용이라 재미있게 진행하였습니다.😊
- [Deep Learning for Anomaly Detection: A Survey](https://arxiv.org/abs/1901.03407) 논문을 메인으로 정리하고 조금의 추가적인 내용을 덧붙인 글입니다.

----

## 1. Anomaly Detection란?
![image](https://user-images.githubusercontent.com/54492747/149662959-a1d9dfcd-e5c9-4fe6-8333-9ccb123b4196.png)

**Anomaly Detection(이상 탐지)**란, Normal(정상)  데이터만의 분포와 특징을 파악하여 Abnormal(비정상) 데이터를 구별해내는 문제를 의미합니다. 대부분의 데이터와 본질적인 특성이 다른 관측치를 찾아내는 Anomaly Detection 알고리즘은 오랜 시간 동안 연구되어온 분야이며, Deep Learning 방법을 적용하는 등 다양한 방향으로 발전하고 있습니다. <br>
또한 제조업뿐만 아니라 의료 영상, Social Network, 금융 사기  등 다양한 분야에서 응용이 되고 있으며 주요 사례는 다음과 같습니다.
* Industrial Anomaly Detection
    - 산업 속 제조업 데이터에 대한 이상치를 탐지하는 사례
    - 각종 제조업 도메인 이미지 데이터에 대한 외관 검사, 장비로부터 측정된 시계열 데이터를 기반으로 한 고장 예측 등 다양한 적용 사례가 있음
    - 외관상에 발생하는 결함과 장비의 고장 등의 Abnormal 관측치가 굉장히 적은 수로 발생하지만 정확하게 예측하지 못하면 큰 손실을 유발하게 됨
* Medical Anomaly Detection
    - 의료 영상, 임상 뇌파 기록 등의 의학 데이터에 대한 희귀 현상을 탐지하는 사례
    - 주로 신호 데이터와 이미지 데이터를 다루며 X-ray, CT, MRI, PET 등 다양한 장비로부터 취득된 이미지를 다룸
* Social Networks Anomaly Detection
    - 스팸, 가짜 사용자, 악성 유저와 같이 Social Network 상의 불법 행동을 탐지하는 사례
    - 주로 Text 데이터를 다루며 Text를 통해 스팸 메일, 비매너 이용자, 허위 정보 유포자 등을 검출함
* Fraud Detection
    - 은행 거래, 보험, 신용, 금융 관련 데이터에서 불법 행위를 검출하는 사례
    - Kaggle의 [Credit Card Fraud Detection](https://www.kaggle.com/mlg-ulb/creditcardfraud)과 같은 공개된 challenge도 진행된 바 있음

<br>

여기서 많은 사람들은 **이상치**라고 하는 것을 다양하게 받아들이는데 크게 1) Anomaly, 2) Novelty, 3) Outlier 로 구분할 수 있습니다. 이 3가지는 용어가 가지는 뉘앙스 차이가 조금 있다고 생각하여서 다음과 같이 정리해보았습니다.

### Anomaly 
**Anomaly**는 대부분의 데이터와 본질적인 특성이 다르며, 기존 분포에서 멀리 떨어져 있어 전혀 다른 방식으로 생성되었을 것으로 추정되는 관측치를 의미합니다. 즉, 다른 매커니즘에 의해 발생된 것처럼 다른 관측치와는 매우 다른 관측치를 말합니다. <br>
아래 그림에서 $N_1$과 $N_2$는 Normal 데이터로 고려할 수 있지만, 점 $O_1$, $O_2$, $O_3$는  $N_1$과 $N_2$의 범주에 멀리 벗어나 있기에 Anomaly 데이터로 고려할 수 있습니다.
![image](https://user-images.githubusercontent.com/54492747/161285018-bacaca0d-a220-40f6-af3f-2ee26dacfdac.png)

### Novelty
**Novelty**는 본질적인 데이터는 같지만 유형이 다른 관측치를 의미합니다. 즉, 데이터에서 이전에 관찰되지 않은 새로운 패턴을 가진 관측치를 말합니다. <br>
하지만 Novelty 데이터는 Anomaly로 간주되지 않고 Normal 데이터 범주로 포함되기 때문에 차이가 있습니다.
아래 그림에서 Novel이라 표시된 백호는 Normal인 '호랑이'라는 Normal 데이터 범주로 고려되지만, 이전에 보지 못한 새로운 패턴에 해당하게 됩니다. 그에 반면, 말, 치타, 사자, 치타는 '호랑이' 범주에 속하지 않기 때문에 Anomaly 데이터로 고려할 수 있습니다.
![image](https://user-images.githubusercontent.com/54492747/161286077-8fcf93e8-804d-4db2-be63-ddb0590cf186.png)

### Outlier
**Outlier**는 데이터의 전체적인 패턴에서 벗어난 관측치를 의미합니다. Outlier의 경우 보통 통계적 맥락에서 Anomaly와 비슷하게 일컫는 말입니다. 하지만 굳이 차이를 따지자면 데이터가 시계열이냐 아니냐로 구분됩니다. (시간과의 관계 여부)
* Anomaly : **시간 또는 순서**가 있는 흐름에 따른 패턴이 보편적인 패턴들과 다른 관측치
* Outlier : **시간과 관련 없이** 대상을 표현하는 관측치들의 위치를 보고 보편적인 패턴과 벗어난 관측치 
![image](https://user-images.githubusercontent.com/54492747/161287940-0cd7cc7d-4705-40a1-ba29-657e984e7257.png)

<br>

## 2. Anomaly Detection 분류
Anomaly Detection는 여러 측면에서 분류되고,  총 3가지 분류 방법으로 이 용어들을 정리하면 다음과 같습니다.

1.  학습시 **비정상 sample의 포함 여부** 및 **label 유무**에 따른 분류
    -   Supervised, Semi-supervised, Unsupervised Anomaly Detection으로 나뉘어짐
2.  **비정상 sample 정의**에 따른 분류
    -   비정상적인 sample의 성격이 정상 sample과 어떻게 다른지
3.  **정상 sample의 class 개수**에 따른 분류
    -   정상 sample의 class가 Multi-class인지

이번 글에는 [학습시 비정상 sample의 포함 여부 및 label 유무에 따른 분류]를 중점으로 설명하도록 하겠습니다. 여기서 label이란 각 관측치가 Normal인지 Abnormal인지에 대한 정보를 의미합니다. 이런 label 유무에 따라 Supervised, Semi-supervised, Unsupervised Anomaly Detection으로 나뉘어집니다.

### 1) Supervised Anomaly Detection
주어진 학습 데이터 셋에 Normal 관측치와 Abnormal 관측치에 대한 Label이 **모두** 존재하는 경우 Supervised Learning 방식이며, 이를 Supervised Anomaly Detection이라 부릅니다.<br>
Supervised Learning 방식은 다른 방법 대비 정확도가 높은 특징이 있습니다. 그래서 높은 정확도를 요구로 하는 경우에 주로 사용되며, 비정상 sample을 다양하게 보유할수록 더 높은 성능을 달성할 수 있습니다.<br>
하지만 Anomaly Detection을 적용하는 분석 데이터에는 label이 모두 있는 경우가 드물고 Normal 관측치보다 Abnormal 관측치의 발생 빈도가 극히 적기 때문에, 일반적인 Supervised Learning으로 학습하게 되면 Class-Imbalance(클래스 불균형) 문제를 자주 겪게 되어 좋은 성능을 내지 못하게 됩니다.  또한 특정 데이터의 정상/이상 여부를 정확히 알지 못하게 되면 Supervised Learning 기반 방법론을 적용하는데 한계가 있기 때문에, Semi-supervised, Unsupervised Learning에 비해 잘 활용되지 않습니다.<br>
이러한 문제를 해결하기 위해 Data Augmentation(증강), Loss function 재설계, Batch Sampling 등 다양한 연구가 수행되고 있습니다. 
* 장점
	* 다른 방법 대비 정상/이상 판정 정확도가 높음
* 단점
	* Abnormal 관측치를 취득하는데 시간과 비용이 많이 들고, Class-Imbalance 문제 해결이 필요함

### 2) Semi-supervised Anomaly Detection
Supervised Anomaly Detection 방식의 가장 큰 문제는 Normal 관측치보다 Abnormal 관측치의 발생 빈도가 극히 적기 때문에 Class-Imbalnace 문제가 발생한다는 것입니다. 그렇기 때문에 정확도를 높이기 위해서는 Abnormal 관측치를 확보해야하는데 이것은 많은 시간이 비용이 든다는 단점이 있습니다. <br>
제조 데이터를 예로 들면, 제품의 외관 검사를 할 때 결점 이미지 데이터가 저장되는데 이때 정상품 이미지 또는 대표 결점의 이미지들이 수천장 취득되는 동안 특정 결점의 이미지는 겨우 1~2장 정도 발생하는 상황이 발생하게 됩니다.
이처럼 Class-Imbalance가 매우 심한 경우에는 Normal 관측치만 가지고 모델을 학습하기도 하는데, 이 방식을 Semi-supervised Learning이라 합니다. Semi-supervised Learning은 Supervised Learning 방식과 Unsupervised Learning 방식의 조합으로, Unsupervised Learning은 데이터의 대표적인 패턴을 학습하는 데 사용되고 Supervised Learning은 그 후 예측을 하기 위해 대표적인 패턴을 학습하는 데 사용됩니다. 즉, 이 학습을 통해 Normal 관측치들의 boundary를 만들어서, 이 boundary 밖에 있어 boundary에 속하지 않는 관측지들을 모두 Abnormal 관측치로 탐지하는 방법입니다.
* 장점
	* Normal 관측치만 이용하여 학습이 가능함
* 단점
	* Supervised Anomaly Detection 방법론과 비교했을 때 상대적으로 정상/이상 판정 정확도가 떨어짐

### 3) Unsupervised Anomaly Detection
Semi-supervised Anomaly Detection 방식은 Normal 관측치가 필요하며, 수많은 관측치들 중에 어떤 것이 Normal 관측치인지 Abnormal 관측치인지 정확히 알고 있어야 합니다. Nomal 여부를 정확하게 알기 어려운 상황을 고려하여, 대부분의 데이터가 Normal 관측치라는 가정을 하여 label 취득 없이 학습을 시키는 Unsupervised Anomaly Detection 방법론이 있습니다. <br>
Unsupervised Anomaly Detection은 정상치와는 동떨어진 representation을 찾는 방법입니다. 대표적으로 밀도를 추정하여 Abnormal 관측치를 탐지하는 Isolation Forest와 Local Outlier Factor 방법이 있으며, Principal Component Analysis(PCA, 주성분 분석)를 이용하여 차원을 축소하고 복원을 하는 과정을 통해 비정상 sample을 검출하는 방법도 있습니다. <br>
최근 수집 가능한 데이터의수가 급증하면서 기존 방법론들은 정확도에서 한계를 보이고 있어, 이에 따라 다양한 딥러닝 기반 방법론들이 활발하게 연구되고 있습니다. Deep Learning 기반으로는 대표적으로 Autoencoder 기반의 방법론이 주로 사용되고 있습니다. 
* 장점
	* 데이터의 내재적인 특성을 학습하여 데이터 내 유사성을 발견하는 작업이기에, Labeling 과정과 알고리즘 훈련이 필요하지 않음
* 단점
	* 정상/이상 판정 정확도가 높지 않고, Autoencoder의 degree of compression(차원축소를 얼만큼 해야하는지) 등과 같은 hyper-parameter에 매우 민감함 (조정 필요)

<br>

## 3. Conclusion
정상 관측치와 다른 특성이 있는 관측치를  판단하는 Anomaly Detection(이상 탐지) 알고리즘은 꾸준하게 연구되어오고 있는 분야입니다. 해당 포스팅에서는 Anomaly Detection의 개념과 관련 용어에 대해 전반적으로 살펴보았습니다. <br>
* Anomaly Detection 개념
* Anomaly, Novelty, Outlier 용어 정의
* Supervised, Semi-Supervised, Unsupervised Learning 특징 (label 유무 여부)

<br>

### Reference
* [Raghavendra Chalapathy, Sanjay Chawla. “Deep Learning for Anomaly Detection: A Survey.” arXiv, 2019](https://arxiv.org/abs/1901.03407)
* [깃헙 블로그 글 - Anomaly Detection 개요](https://hoya012.github.io/blog/anomaly-detection-overview-1/)
	* 이상치 탐지 분야에 대한 소개 및 주요 문제와 핵심 용어, 산업 현장 적용 사례를 깔끔하게 잘 정리해주셨습니다.
* [고려대학교 DMQA 연구실 세미나 내용](http://dmqm.korea.ac.kr/activity/seminar/339)

-------

### 🔜 Next
드디어 작년부터 생각만 해오던 Anomaly Detection 포스팅 시리즈의 막을 올렸습니다🥳 활발하게 연구가 진행되고 있는 분야인만큼 소개할 내용이 많아서 기분이 좋네요! <br>
제조업 특성상 장비로부터 측정된 시계열 데이터가 메인이다보니, 장비의 고장과 같은 Abnormal한 케이스를 예측하고 싶어하는 니즈가 많습니다. 특히 저는 Normal 관측치만으로 학습하여 Abnormal 관측치 탐지를 진행하는 Deep Learning 기반 알고리즘에 관심이 많아, 짬짬히 공부하던 Anomaly Detection 내용을 한번 정리하는 것이 좋겠다고 생각되어 글을 작성하게 되었습니다. <br>
관련해서 캐글 필사를 하기도 하고, 논문을 읽기도 하고, 실데이터에 접목시키기도 하고 다양하게 공부를 진행하고 있는데, 이것을 잘 정리해서 많이 부족하지만 처음 접하시는 분들이 재밌게 읽으실 수 있도록 해보겠습니다🔥 <br>
이어지는 포스팅에서는 순차적으로 Machine Learning for Anomaly Detection, Deep Learning for Anomaly Detection, Anomaly Detection for Time Series에 대해 다뤄보도록 하겠습니다.

<br>

긴 글 읽어주셔서 감사합니다. Anomaly Detection 관련해서 꾸준히 공부해서 포스팅을 연재할 수 있었으면 좋겠네요!🖖
