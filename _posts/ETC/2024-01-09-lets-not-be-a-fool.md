![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/2e5743e4-da86-4404-9016-21744db9f809)![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/766a7435-1ff4-400d-9489-c319f914160d)---
layout: post
title:  "더이상 멍청해지는 것을 방지하기 위한 개념 정리글"
subtitle:   "Let's not be a fool"
categories: etc
tags: 
comments: true
---

- 최근 논문을 읽으려고 하는데, 오랜만의 공부라 그런지 어려워하는 저를 보며 충격을 먹었습니다.
- 기본 개념조차 까먹고 있어서, 다시 공부 시작하는 겸 정리해보려고 합니다.
- 이번 글은 제가 다시 보기 위해 쓰는 글이다보니 다소 러프할 수 있습니다😢
- 멍청이 탈출 기원😱

---------

# 💡 통계분석 개념
### ⬛ 가설검정
1. **단측 검정(one-sided test)**
  * 표본 분포의 한쪽에 관심을 가지고 시행하는 검정방법
2. **양측 검정(two-sided test)**
  * 검정랴
3. **유의수준(significant level)**
  * 가설 예측을 100% 옳게 할 수 없으므로 오차를 고려하기 위한 것
  * 잘못해서 귀무가설을 기각하는 제1종 오류를 범할 확률의 상한 (제 1종 오류가 발생할 확률)
    * 1종 오류의 상한선
    * 검정결과 p-value가 유의수준보다 낮으면 상한선을 벗어나지 않으므로 귀무가설을 기각하고, p-value가 높으면 귀무가설을 채택 
  * 유의수준을 통해 귀무가설 채택여부를 결정 (일반적으로 0.05로 설정)
  * 검정 통계량 기반으로 p-value가 산출되면, 0.05보다 낮으면 귀무가설을 기각하고 대립가설을 채택함
4. **검정 통계량**
  * 귀무가설이 참이라는 가정 하에, 확률표본을 이용하여 구한 모수에 대한 추정량
  * 모수 추정을 위해 구하는 표본 통계량과 같은 의미
  * 표본을 통해 가설 검정에 사용하는 확률 변수
5. **1종 오류/2종 오류**
  * 1종 오류(type 1 error, alpha) : 귀무가설이 참인데 기각한 경우
    * p-value = 1종 오류를 얼마나 범할 확률
    * 즉, p-value가 5%라면 100번 검정하면 5번 정도 1종 오류가 발생하는 것
    * 유의수준 = 1종 오류의 상한선
  * 2종 오류(type 2 error, beta) : 대립가설이 사실임에도 불구하고 귀무가설을 기각하지 못하는 경우
6. **검정력(statistical power)**
  * 대립가설이 사실일 때 귀무가설을 기각할 확률(1-beta)
  * alpha를 고정시키고, 이를 만족시키는 기각역 중에 beta를 최소화하는 기각역을 선택

![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/b44c7918-07e5-4d1e-a45e-dde5bc5d15bf)


### ⬛ 중심극한정리
무작위로 추출된 표본의 크기가 커질수록 표본 평균의 분포는 모집단의 분포 모양과는 관계없이 정규분포에 가까워진다는 정리
* 표본 평균의 평균은 모집단의 모평균과 같고, 표본 평균의 표준 편차는 모집단의 모표준 편차를 표본 크기의 제곱근으로 나눈 것과 같음
* 중요한 이유
  * 각각의 표본은 모집단의 특성을 나타내기에 부족하지만, 표본 평균의 분포가 n이 커지면 모집단의 특성을 나타낼 수 있게 됨
  * 즉, 통계량인 표본평균을 통해서 모집단의 모수인 모평균과 모표준편차를 추정할 수 있는 확률적 근거를 제서해주는 것임
  * 많은 통계적 방법이 정규성 가정에 의존하기 때문

<br>

# 💡 데이터 전처리
### ⬛ Unbalanced Data
불균형 데이터란 데이터 내 **각각의 클래스들이 차지하는 데이터의 비율이 균일하지 않고 한쪽으로 치우친 데이터**를 말한다. 일반적으로 불균형 데이터를 통해 모델을 학습할 시에는 다음과 같은 문제점을 가진다.  <br>
* 적은 수의 이상치에 편향된 분류 경계선이 학습됨에 따라 예측 단계에서의 오분류율이 높음
* 높은 정확도에도 이상 클래스에 대해서는 잘 분류하지 못해 모델 성능에 대한 왜곡을 불러일으킴

범주형/연속형 불균형 데이터 중 불균형 데이터에 대한 해결법은 보통 범주형 데이터에 대한 것들이 많다. (현실의 데이터들은 대체로 연속형 데이터가 많음에도 불구) <br>
불균형 연속형 데이터의 경우, 기존 불균형 범주형 데이터와 다른 특징을 보인다.
* 클래스 경계가 존재하지 않음 : resampling, reweighting 방법을 적용하기 어려움
* 타겟값끼리 연속성 및 유사성: 주변값의 분포에 따라 다른 수준의 불균형을 겪음
  * 이웃한 범위 내의 데이터가 많고 적을수록 불균형의 정도가 다름
* 특정 대상값에 대한 데이터가 아예 없을 수 있음
  * 주변 데이터를 통해 interpolation 또는 extrapolation 가능

그렇기 때문에 인접 데이터간 유사성과 커널 함수를 활용하여 불균형 문제를 해소하고자 하는 방법들이 많이 발생한다. (분포를 기반으로 소수 클래스에 해당하는 데이터를 증강시킴)<br>
* 대표 방법론
  * Label Distribution Smoothing(LDS): 레이블 공간 관점
  * Feature Distribution Smoothing(FDS): 특징 공간 관점
  * SMOGN(Synthetic Minority Over-Sampling Technique for Regression with Gaussian Noise)
    * Gaussian Noise의 기본 원리에 따라 기존 데이터를 훼손시키지 않으면서, 과대 추정될 수 있는 범위의 데이터는 축소시키고, 과소 추정될 수 있는 소수 데이터 범위의 데이터는 증폭시켜주는 오버샘플링 기법
    * python 라이브러리 형태 : smoter함수의 advanced mode를 사용하여 샘플링 해줄 것인지에 대한 세부인자 값들을 수동으로 설정할 수 있음
    * 이때 세부인자 설정 과정에서, 오버샘플링 데이터의 왜도 값을 비교하며 낮은 왜도 값을 가질 때의 세부인자를 선정할 수 있음



<br>

# 💡 모델링 개념
### ⬛ 머신러닝 종류
1. **지도학습(supervised learning)**
  * 데이터에 대한 정답(Y)을 주고 학습시키는 방법
  * 분류, 회귀
2. **비지도학습(unsupervised learning)**
  * 데이터에 대한 정답(Y)을 주지 않고 학습시키는 방법
  * 클러스터링, 오토인코더
3. **강화학습(reinforcement learning)**
  * 에이전트(Agent)가 주어진 환경(State)에 대해 어떤 행동(Action)을 취하고, 이로부터 보상(Reward)을 얻으면서 학습을 진행하는 방식
  * 에이전트가 보상을 최대화하는 방식으로 학습이 진행

### ⬛ 모델 설명
1. **DNN**
  * 신경망 모형 중 정형데이터 처리에 적합
  * 다층 퍼셉트론(Multi-Layer Perceptron)으로, 인공 신경망의 한 종류
  * 사람의 신경망과 비슷한 형태로 입력층, 은닉층, 출력층으로 구성됨
    * input layer : X값 / hidden layer : X값 데이터 특징 추출 / output layer : Y값
    * ex) 1개의 input layer & 각 5개, 5개 뉴런으로 이루어진 2개의 hidden layer & 1개의 output layer
  * 각 층의 노드들은 가중치와 활성화 함수를 통해 입력 신호를 변환하고 처리함
  * 파라미터
    * 파라미터 선택 방법 : 각 파라미터는 MAE 값이 가장 낮은 지점의 형태 선택
    * ex) activation function : relu, linear / Loss function : MAE / Optimizer : Adam / Learning Rate : 0.001 / Epoch : 500 / Batch size : 6
2. **1D-CNN (1 Dimensional Convolution Neural Network)**
  * 시간에 따라 데이터가 구성되는 시계열 데이터에 적합함
  * 1차원 Convolutional Neural Network으로, 인공 신경망의 한 종류
  * 주로 시계열 데이터나 순차적인 데이터에서 패턴을 감지하고 학습하는데 좋은 성능을 보임 (변수 간의 지엽적인 특징을 추출)
  * 각각의 층에서 입력 데이터의 부분적인 패턴을 파악하는 일종의 filter 작용을 거침
  * Layer
    * convolution layer(Conv1D) : 데이터 특성을 추출하고 패턴을 파악
    * pooling layer :
    * fully connected layer :
    * dropcout layer : 과적합 방지
    * ex) conv1d : filters=32, kernel_size=100 / Maxpooling1D : pool_size : 2 / Dense : units : 10 / Dropout : 0.1

### ⬛ 1D-CNN, 2D-CNN, 3D-CNN 차이점
CNN 모델은 1D, 2D, 3D로 나뉘는데, 일반적인 CNN은 보통 이미지 분류에 사용되는 2D를 통칭한다. <br>
여기서 D는 차원을 뜻하는 dimensional의 약자로, **인풋 데이터 형태에 따라 1D, 2D, 3D 형태의 CNN 모델이 사용**된다.
* 즉, **입력 데이터의 차원**에 따라 Conv1D, Conv2D, Conv3D를 사용함
* **합성곱을 진행할 입력 데이터의 차원**을 의미 → 합성곱 진행 방향을 고려해야함

![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/ae26cb3d-ccc9-4a00-8017-e49951a0bf5f)
(출처: Understanding 1D and 3D Convolution Neural Network | Keras)


### ⬛ 모델 최적화
* Hyperparameter Tuning
  * Hyperparameter : 모델을 생성할 때, 설정할 수 있는 모델 변수
  * Grid Search : 가장 좋은 성능을 보이는 파라미터들의 조합을 선택하는 방법
* K-Fold Cross Validation
  * 과적합을 막기 위한 모델 선택 방법
  * 전체 데이터셋을 k개로 나누고 한뭉치씩 돌아가며 테스트셋으로 지정하여 모델을 훈련시키는 방식
  * 데이터를 k개의 서로 다른 부분으로 나누고, 각각의 부분을 교차 검증(cross-validation)에 사용
  * 모든 데이터를 활용하여 k개의 학습과 검증 과정을 거치며 신뢰도 높은 모델을 만들어냄
  * k-fold ensemble : k개의 예측을 평균하여 최종 결과를 도출함



### ⬛ Regularization
과적합을 막기 위해 특정 가중치가 너무 커지지 않도록 제한하는 방식으로 모델 복잡도를 줄이며, L1 규제와 L2 규제가 있다. <br>
L1에 비해 L2는 이상치나 노이즈가 있는 데이터에 대한 학습에 좋으며, 특히 선형 모델의 일반화에 좋다.
1. L1 규제 : cost function 식에서 가중치의 절대값을 더해줌
2. L2 규제 : 가중치의 제곱값을 이용

### ⬛ Activation function
노드로 들어오는 신호에 대해 신호를 전달할만큼 의미가 있는지 없는지(가중치가 큰지 안큰지) 판단해주는 함수이다. <br>
대표적으로 시그모이드 함수, ReLU 함수, tanh 함수 등이 있다.

### ⬛ Holdout method
데이터셋을 train, test, eval set으로 분할하여 사용하는 모델 선택 방법이다. <br>
train set으로 모델을 훈련하고, eval set은 모델 선택에 사용하여, test set으로 모델 훈련 뒤 성능 평가에 사용된다.



### ⬛ 성능평가지표
모델의 성능을 평가할 수 있는 지표로, 정확도/정밀도/재현율/F-score 등이 있다.
1. Accuracy
  * True를 True라고, False를 False라고 옳게 예측한 비율
  * 모든 데이터 중에서 모든 'True'들의 비율
2. Precision
  * 모델이 True라고 분류한 것 중에서 실제 True인 것의 비율
3. Recall
  * 실제 True인 것 중에서 모델이 True라고 예측한 것의 비율
4. F1-score
  * precision과 recall의 조화평균
  * label의 수가 불균형적일 때, 모델의 성능을 비교적 정확히 평가할 수 있음

<br>

# 💡 분석 케이스 접근
### ⬛ 예측 모델링
* 딥러닝 모델을 활용한 제품 물성 예측
  * 목표 KPI를 잡아야함 (ex. 제품 스펙 범위의 10%)
  * 뿐만 아니라 신뢰성 높은 모델을 확보하기 위해, 다양한 지표를 사용하여 모델을 평가해야함


<br>

# 💡 기술 질문
### ⬛ 매출이 감소했다고 하면 어떻게 접근하여 분석하겠는가?
매출의 증감을 표현할 수 있는 다양한 지표(Index)를 생성(기존+신규)하여, 사업부/상품/기간 단위로 매출 감소 영역을 Sensing하고 상세 분석을 통해 매출 감소 원인을 파악하겠습니다.

### ⬛ 배치 파이프라인의 Tool인 Airflow란?
Apache Airflow는 프로그래밍 방식으로 워크플로우를 작성, 예약 및 모니터링하는 오픈 소스 플랫폼입니다. 정확한 시간에, 정확한 방법으로, 정확한 순서대로 실행하게 해주는 오케스트레이터라고 볼 수 있습니다.

### ⬛ 코로나와 같은 특수한 상황에 대해 어떻게 예측할 것인가?
과거에 코로나라는 특수한 전염병에 사례와 비슷한(메르스, 사스) 전염병의 데이터를 수집한 데이터를 활용하여 다양한 파생변수를 생성하고, Feature로 사용할 것 입니다. 크롤링을 통해 검색어 증감을 분석하여 사전 Issue Alert 전달하고, 앞선 대응을 진행할 것 입니다.

### ⬛ RFM 방법론의 효과가 좋지 않은데 어떻게 사용할 것인가?
어느 영역(R/F/M) 가중치를 더 두느냐에 따라서 모델의 가치가 결정된다고 생각합니다. 분석 목적에 맞는 가중치 설정을 통해 최고의 효과를 달성하는 방안을 고안한다면 RFM도 성능을 높일 수 있을 것이라고 생각합니다.

### ⬛ 2~3천개의 Feature가 있는데 어떻게 접근하겠는가?
모범 답안 NA 비율 및 Zero 비율, Outlier 확인후 의미없는 변수를 1차 제거하겠습니다. Correlation 분석을 통해 상관관계가 높은 변수를 제거(Y의 영향을 가장 많이 미치는 변수를 생존시킴)하고 만약 컴퓨팅 파워가 가능하다면 그리고 Tree 계열의 알고리즘을 사용한다고 가정했을 때, 최대한 많은 변수를 넣어서 진행하여 성능을 높이는 방향성으로 분석을 진행하겠습니다.

### ⬛ 데이터 분석 프로세스에서 가장 중요하다고 생각되는 단계는?
현업에 Needs를 파악하여 과제를 구체화시키는 단계와 분석된 결과를 현업에 적용시키는 단계가 가장 중요하다고 생각합니다. 과제 구체화와 현업 적용 단계를 놓치면 제대로된 분석결과를 기대할 수 없기 때문입니다.


<br>

### Reference
* [데이터 직무 면접 질문 모음집 (feat. 모범 답변, 합격 꿀팁까지)](https://zero-base.co.kr/event/media_insight_contents_DS_da_interview)
* [데이터 관련 직무 면접 대비용 개념 한 줄 정리](https://stellarway.tistory.com/8)
* [갈아먹는 통계 기초[4] 가설, 검정, p-value](https://yeomko.tistory.com/m/37?category=879901)
* [중심극한정리(CLT; central limit theorem)](https://biology-statistics-programming.tistory.com/72)
* [Handling imbalanced datasets](http://dmqm.korea.ac.kr/activity/seminar/343)
* [Conv1D, Conv2D, Conv3D 차이](https://leeejihyun.tistory.com/37)
