---
layout: post
title:  "오늘의집은 이렇게 한다: 피드 콘텐츠 순위와 다양성의 균형 맞추기"
subtitle:   "Let's not be a fool"
categories: etc
tags: etc_til
comments: true
---

- 공부한 내용에서 개인적인 생각을 조금 담아 작성하였습니다✍🏻
- 더 풍부한 내용을 원하면 원본 콘텐츠를 꼭 읽어보시길 권장 드립니다. 원문을 통해 더욱 깊이 있는 정보를 얻으실 수 있을 거에요😉

---------

# ✍🏻 오늘의 주제
* 오늘의집 추천시스템: **Multi-Stage Recommender System**
	* 디스커버리팀 - 복잡한 고객 니즈에 맞는 추천서비스를 가능하게 해주는 범용 추천시스템 구축
	* 다양한 제품들의 요구사항에 대응할 수 있도록 설계되었고, 대부분의 추천 피처에 적용되어 대용량 트래픽을 받는 지면들에 활용되고 있음
* 관심있는 부분
	* 유저의 선호도를 기반으로 한 개인화된 취향 맞춤형 콘텐츠 추천 시스템
		* 선호도: 클릭, 구매, 스크랩 등의 행동 분석
	*  추천시스템 component: ranker, twiddler - 어떻게 추천 결과를 최적화했는지
		* Ranker 모델링 방법 & Twiddler 모듈의 역할과 기능
		* Ranker: 콘텐츠를 클릭할 확률을 계산하여 콘텐츠의 순위 결정
		* Twiddler: 콘텐츠 다양성을 높이기 위해 결정된 순위를 재조정하는 과정

<br>

# 📝 내용 정리
*서버 구성 및 통신 부분은 생략하였습니다. 자세한 내용은 [해당 글](https://www.bucketplace.com/post/2024-03-26-%EA%B0%9C%EC%9D%B8%ED%99%94-%EC%B6%94%EC%B2%9C-%EC%8B%9C%EC%8A%A4%ED%85%9C-1-multi-stage-recommender-system/)을 통해 확인해주세요.*

## 1. 오늘의집 추천 피드
* 다양한 타입의 콘텐츠들을 하나의 피드로 구성하여 추천 진행
	* 오늘의집 유저들이 올린 사진, 영상들뿐만 아니라 집들이 / 노하우 등의 콘텐츠 존재
	* 다양한 콘텐츠를 실시간으로 추천한 후 하나의 개인화 랭킹으로 유저들에게 제공 중

## 2. AS-IS & TO-BE
* AS-IS
	* 각 지면별로 별도의 추천 서비스 API를 사용
	* 즉, 비슷한 공간 또는 비슷한 상품을 추천해주는 각각의 시스템을 활용
* TO-BE
	*  Multi-stage recommendation system을 통해 통합된 시스템을 구축하여 사용
	* 하나의 시스템을 활용하여 복잡한 개인 니즈에 맞는 추천서비스(=개인화서비스)가 가능하도록 함

## 3. Multi-Stage Recommender System 개념
Multi-Stage Recommender System은 인더스트리에서 종종 사용되는 기법입니다. 
(참고 : [Deep Neural Networks for Youtube Recommendations, ACM Conference on Recommender Systems 2016](https://research.google/pubs/deep-neural-networks-for-youtube-recommendations/))
* Multi-Stage Recommender System은 두 단계로 나누어 추천 후보군을 생성하고 순위를 매김 (최적화)
	* 2개 stage : 후보군 생성, 랭킹
	* 백만개가 넘는 추천 후보군에서, 각 유저마다 수백 개가 넘는 문서 또는 콘텐츠를 추천하기 위함
![▲ Multi-Stage Recommender System](https://res.cloudinary.com/bucketplace-co-kr/image/upload/v1711934779/%EB%8C%80%EC%B2%B41.jpg)

* 두 단계 Stage
	* **첫 번째 Stage**: Candidate Generation 알고리즘을 활용하여 수백만 개에 달하는 문서들 중 실제로 유저에게 추천할 만한 몇 백 개 정도의 후보군을 추출하는 작업을 함
	* **두 번째 Stage**: Ranking에 앞서 추출된 후보군들을 유저가 좋아할 만한 순서로 랭킹을 적용하는 작업을 함
* 평가지표 - 검색, 추천 등의 ML Problem에선 Precision, Recall 지표로 성능을 평가
	* **첫 번째 Stage**: 유저가 좋아할만한 문서를 최대한 많이 골라내는 Recall을 최적화
	* **두 번째 Stage**: 추려진 문서들을 유저가 좋아할만한 순서대로 랭킹하며 Precision을 최적화

## 4. 추천시스템 구조
![](https://res.cloudinary.com/bucketplace-co-kr/image/upload/v1711431794/Architecture_Overview.jpg)
* 추천시스템은 Nominator, Producer, Mixer, Ranker, Twiddler의 단계로 세분화하여 구성됨
	* 각 단계는 추천 후보군을 생성하고, 믹싱하고, 순위를 매기는 역할을 함
	* 실시간으로 구현을 목표로 함
	* 각 단계마다 여러 방식의 구현을 교체할 수 있도록 확장성을 고려하여 설계됨

### 1️⃣ Nominator & Producer
#### 🧞 Nominator
각 Nominator들은 Candidate Generation에 해당하는 알고리즘을 통해 Nomination들을 생성합니다. <br>
➡️ **추천 가능한 후보군 전체 중에서 유저가 좋아할 만한 후보군들을 생성하는 역할**

추천 후보군은 Online Nomination(실시간성)과 Offline Nomination(준실시간성)으로 나뉩니다.
*  Online nomination
	* 팔로잉 등을 통해 유저가 직접 요청을 하는 콘텐츠
	* 실시간 인기 혹은 큐레이션 콘텐츠 등 최소한의 검증 후 노출하고자 하는 콘텐츠
* Offline nomination: 많은 연산이 필요하기 때문에 online 대신 사용됨
	* SignalProcessor : 콘텐츠의 다양한 시그널을 추출
		* 콘텐츠의 이미지 퀄리티(고품질, 저품질), 콘텐츠의 토픽들 (#데스크테리어, #강아지, ..), 콘텐츠 내의 물체들 (소파, 테이블, 텐트, ..) 등을 추출하는 알고리즘들이 실행됨
	* CDoc(CompositeDoc) : 콘텐츠 자체에 대한 정보들이 담김
		* 추출된 정보들이 각 콘텐츠마다 하나의 문서에 조인된 CDoc에 저장되게 됨
	* 알고리즘 실행
		* 추출된 정보들을 사용하여 유저에게 적합한 콘텐츠를 추천하는 알고리즘들이 Nominator 안에서 실행됨
	* 추천 항목
		* Nominator들은 collaborative filtering, graph-based recommender 등의 알고리즘을 통해 콘텐츠를 추천
		* 유저가 관심도를 보인 토픽에 해당하는 콘텐츠를 추천

이러한 nomination들은 연산량 혹은 실시간성에 따라 batch pipeline 혹은 streaming pipeline으로 실행되고, 그 결과값인 nomination들은 서빙 가능한 저장소에 저장됩니다. 
* 서빙 가능한 저장소에 Redis, DynamoDB 등의 NoSQL을 활용하여 Key-Value lookup 을 지원
	* 각 Nominator 마다 리트리벌(retrieval)을 하는 방법이 상이함
	* 그렇기 때문에 그에 맞는 Key-Value schema를 세팅하여 사용 중
![](https://res.cloudinary.com/bucketplace-co-kr/image/upload/v1711432342/Key-Value_schema.jpg)

#### 🚚 Producer
이렇게 저장된 Nomination들을 서빙 타임에 리트리벌해 오는 컴포넌트를 Producer라 지칭합니다.
* Producer: 저장된 후보군을 서빙하는 역할
	* 각 Nominator 마다 별도의 저장소에 후보군들이 저장됨
	* 그렇기 때문에 서빙 타임에 리트리벌하는 Producer들은 각 Nominator마다 하나씩 존재하게 됨

### 2️⃣ Mixer
Mixer는 여러 Producer에서 가져온 후보군을 하나의 리스트로 믹싱하는 작업을 합니다. Mixer에선 다양한 알고리즘들을 적용할 수 있는데, 간단한 예로 아래와 같은 stage들을 거칩니다.
-   **Deduplication**: 중복된 후보군 제거
	- 여러 Producer 들에서 가져온 Document들은 여러 알고리즘을 통해 추천했기 때문에, 서로 겹치는 후보군들을 포함하고 있음
	- 일반적으로 피드 등에 노출될 때, 이러한 중복 아이템들을 피하기 위해 dedup 오퍼레이션을 진행함
-   **CDoc Retrieval**: CDoc bulk retrieval 진행
	- CDoc 안에는 각 Document들마다 여러 메타정보를 미리 연산해놓음
	- CDoc들은 각 Nominator에서도 쓰일 뿐 아니라, 실시간 서빙을 할 때에도 유용하게 쓰이기 때문에, Producer들이 가져온 Document ID들에 대한 CDoc bulk retrieval을 진행함
-   **Filter**: 메타정보를 활용한 필터링
	- 앞서 가져온 CDoc 안에 있는 메타정보를 활용하여 필터 처리 진행
	- 메타정보 예시 : image quality score, abusing content score 등

### 3️⃣ Ranker
Ranker는 1차적으로 구성된 후보군의 순위를 매기는 역할을 합니다. 랭킹 알고리즘은 해당 추천이 나가는 지면의 목적성에 따라 다른 알고리즘을 활용할 수 있도록 구성되었습니다. <br>
➡️ 예시: RRF (Reciprocal rank fusion), pCTR (CTR prediction), Two tower model, Sequence model

각 후보군에 대한 정보(CDoc 내 정보들)와 유저 정보 등을 Feature Store에 저장한 후, 이를 활용하여 모델 예측을 진행합니다.

#### 🔢 predicted Click-Through Rate (pCTR) Ranking model 개발
유저 개인화 추천을 제공하기 위해서는 콘텐츠를 유저가 선호하는, 즉 클릭할만한 순서로 보여주어야합니다. <br>
이때 개인화 랭킹 모델이 최적해야할 대상은 바로 **유저의 콘텐츠 "선호도" **입니다. 
* 유저 행동 시그널
	* 앱 또는 홈페이지를 방문하여 클릭, 구매, 스트랩, 팔로우 등의 다양한 interaction signal
	* 흥미로운 콘텐츠나 상품을 발견했을 때 더 자세히 보기 위해서 click 액션을 통해 상세 페이지 접근

이때 유저의 선호도를 "유저가 콘텐츠를 클릭할 확률"로 정의한다면, "클릭할 확률"을 예측하는 모델을 설계할 수 있습니다.
* CTR prediction: next prediction item에 대한 click 확률을 예측하는 문제
	* binary regression/classification
		* 유저가 클릭했을 때를 1, 클릭하지 않았을 때를 0으로 레이블링하여 학습
		* 클릭 확률을 예측하는 모델 생성
	* 다양한 학습 접근 방식
		* pointwise: binary classification으로 각각의 item에 대한 CTR을 독립적으로 예측
			* 장점: 연산량과 복잡도가 낮음
			* 해당 포스팅에서는 pointwise 방식에 대해 설명
		* pairwise: 두 아이템 사이의 선호도를 예측
		* listwise: top N개의 아이템을 모두 고려

#### 🤹🏻‍♀️ Feature 선택
추천시스템은 크게 협업 필터링(Collaborative Filtering)과 콘텐츠 기반 접근 방식로 나뉘며, 일반적으로 협업 필터링이 더 효과적입니다. <br>
➡️ (협업 필터링) user-item 사이의 상호작용을 학습해서 추천 제공 > (콘텐츠 기반) 콘텐츠 자체 데이터 사용

* **user history feature**
	* feature list
		* 유저가 어떤 콘텐츠에서 어느 위치(position)에서 노출(impression) 되었는지
		* 어떤 콘텐츠를 클릭(click) 하였는지
	* 주의할 점
		* history feature는 label event와 연관관계가 없는 것만 사용해야함 -> 데이터의 label 또한 click 여부로 정의되기 때문
		* 예: 클릭 전 최신 impression 데이터 전부 사용 -> 클릭은 반드시 최근 impression에서 일어남 -> 모델이 최신 impression이 일어난 콘텐츠가 positive가 아님에도 불구하고 positive에 가깝게 학습할 수 있음
		* 그렇기 때문에 가장 최신의 feed pageview를 기준점으로 삼아 기준점 이전의 event만 feature로 사용하는 등 제약이 필요함
![Fig. 2. 가장 최신의 feed pageview를 기준점으로 삼아 기준점 이전의 event만 feature로 사용](https://res.cloudinary.com/bucketplace-co-kr/image/upload/v1720578047/2.png)

* **user feature**
	* 유저의 관심사, 최근 조회한 콘텐츠와 커머스
* **contents feature**
	* 이미지와 텍스트에서 추출한 벡터

최종적인 feature 선택은 ablation study를 통해 진행되었습니다. <br>
➡️ ablation study: feature를 제거해봄으로써 feature가 어떤 영향을 주는지 확인하는 실험

#### 💻 모델 선정
유저와 콘텐츠의 side information(예: 유저의 성별, 나이, 콘텐츠의 토픽, 조회수 등)을 좀 더 원활하게 사용할 수 있는 Logistic regression(LR) 기반의 모델을 사용하였습니다. <br>
DNN, Field-weighted Factorization Machines (FwFM) , DeepFwFM 등의 모델에 대한 offline test를 통해 어떤 모델이 잘 작동할지 테스트 진행하였습니다.
* 모델 후보
	* Logistic regression(LR)
	* Matrix factorization(MF)
	* Graph neural Network(GNN)
	* two tower model
* 장단점
	* 유저-아이템 간 상호작용을 학습 : MF, GNN과 같은 구조가 유용
		* 하지만 LR기반의 모델과 달리 직접적인 side information 활용이 어려움
		* 유저-아이템 간 히스토리가 없는 경우에 예측이 어려운(cold start problem) 단점 존재

오프라인 테스트는 유저 클릭 로그를 사용하여 모델이 출력한 랭킹이 유저의 실제 선호도와 어느 정도 일치하는지 확인하였고, hit ratio를 기준으로 하여 FwFM를 최종 모델로 선정하였습니다. 

#### 🤯 모델 경량화
위의 내용을 통해 최적의 모델 구조, hyper-parameter, feature set, sampling method를 찾았지만, 모델의 크기와 학습 데이터 셋이 커지면서 실시간 서비스에 적용하기 어려운 문제가 발생했습니다.
* 문제 예시
	* 이미지와 텍스트 특성 추출 : Multimodal representation model - KELIP(주석 추가) 사용
		* pretrain된 모델을 그대로 사용 -> 이미지, 텍스트 embedding vector 각각의 크기가 1*512
	* 모델은 정교하게 학습되었지만, 실시간 서비스에 바로 적용하기에는 높은 Latency 존재

이러한 문제를 해결하기 위해 시도하였던 모델 경량화 방법들은 다음과 같습니다.
1. **Dimensionality Reduction**
	* as-is: 하나의 콘텐츠에 1024 feature dimension
		* KELIP에서 추출한 콘텐츠의 이미지, 텍스트 embedding vector를 concat
		* dimension이 클 경우
			* 장점 : 클수록 많은 정보를 담을 수 있음
			* 단점 : 모델 학습, 추론 시 연산량이 많아지고 차원의 저주가 발생할 가능성이 커짐
	* dimension 줄이는 방식 (조건: feature가 가지고 있는 정보를 충분히 보존)
		* mean/max pooling
		* Auto Encoder
		* UMAP(Uniform Manifold Approximation and Projection)
	* 모델 성능과 hyper-parameter 최적화의 난이도를 고려해서 Auto Encoder를 최종적으로 선택
		* 1024 dimension -> 64 dimension까지 줄임
	* 다른 방법
		* pooling : 가장 간단한 방식이지만 모델 성능 하락 폭이 컸음
		* UMAP : 최적화 시 dimension을 큰 폭으로 줄일 수 있지만, 빠르게 변화하는 데이터에 대한 hyper-parameter 최적화 부담 존재

2. **Share Embedding Space**
	* LR 기반의 모델 : 각 feature에 대해 정해진 길이의 embedding vector를 학습함으로써 feature간의 interaction을 모델링 진행
		* FwFM model equation 설명
			* i, j : feature(f_i, f_j)의 index
			* w : weight
			* x : feature의 active 여부
			* v : feature의 embedding vector,
			* r : field 간 interaction strength
![Fig.3. FwFM model equation. i, j는 feature(f_i, f_j)의 index, w는 weight, x는 feature의 active 여부, v는 feature의 embedding vector, r은 field 간 interaction strength를 의미함.](https://res.cloudinary.com/bucketplace-co-kr/image/upload/v1720578353/3.png)
	* 파라미터 숫자
		* 가정: (categorical) feature 개수가 m개, 필드 개수가 n개, embedding space의 dimension이 K
		* 학습해야 하는 파라미터의 숫자는 다음과 같이 증가함
![](https://res.cloudinary.com/bucketplace-co-kr/image/upload/v1720578452/4.png)
	* share embedding space - m, n의 크기를 줄일 수 있음
		* 정의 ![Fig.4. share embedding space 정의](https://res.cloudinary.com/bucketplace-co-kr/image/upload/v1720578529/5.png)
		* 가정 : 비슷한 특성을 지닌 필드끼리 비슷한 embedding 방식으로 mapping될 것이다.
		* 예시 : historical field는 모두 콘텐츠 id로 이루어져 있음
			* 같은 embedding layer를 공유함으로써 학습 파라미터의 숫자를 크게 줄일 수 있음
		* 주의할 점
			* 완전히 같은 필드가 아님
			* embedding space 공유로 인해 학습 시 feature가 담고 있는 정보의 손실이 일어날 수 있음
		* 각 hash 값에 field 별로 shift를 준 후 오프라인 테스트 성능이 크게 떨어지지 않는 선에서 embedding layer를 공유함

3. Hash 값의 범위 줄이기
	* distinct value
		* gender, 공간 타입 등 낮은 cardinality를 가지고 있는 경우
		* 콘텐츠 id처럼 distinct value가 n천만이 넘어가는 경우
	* hash 값 범위 조절
		* 각 distinct value에 대해 충돌을 피하기 위해서는 최대한 큰 해시테이블에 hash를 저장하는 것이 좋음
		* 하지만 너무 많은 feature 개수로 인해 학습 파라미터의 양이 증가할 수 있음
		* 모델 성능이 크게 저하되지 않는 선에서, 해시 값의 범위를 조절하여 적절한 값을 선택할 수 있음

#### 🆎 온라인 A/B 테스트
pCTR 모델을 적용하기 위해 기존의 휴리스틱 랭킹과 비교하는 AB 테스트를 진행했습니다.
* 성능 비교 지표
	* Click-conversion-rate: 유저가 콘텐츠 피드를 더 많이 조회하게 했는지 확인
	* CTR과 Clicks-per-user : 유저에게서 더 많은 클릭을 이끌어냈는지 확인
	* Centrality Ratio : 추천이 유저의 선호도에 따라 잘 개인화되었는지 확인 (다양성 지표)
* 테스트 결과
	* 모든 지표에서 통계적으로 유의미한 성과 향상을 보임
	* 이후 총 클릭수와 CTR이 지속적으로 증가하는 추세

### 4️⃣ Twiddler
Twiddler는 랭킹이 끝난 후보군 리스트를 추천 피드의 목적성에 맞게 최적화하여, 콘텐츠의 다양성을 높이는 역할을 합니다.  복잡한 요구사항들은 하나의 모델로 해결하기 어렵기 때문에, 이러한 비즈니스 룰들은 랭킹이 끝마친 후보군 리스트를 Twiddler라는 컴포넌트를 통하여 처리합니다. 이때 이 과정은 실시간으로 유저의 행동을 반영하여 진행됩니다. <br>
➡️ Multi-Objective를 고려하여 추천의 품질을 높이고, 유저가 이미 클릭한 콘텐츠는 낮은 순위로 조정하여 반복 노출을 방지

이때 유의할 점은 Twiddler들이 너무 많아져서 트래킹 하기 어려워지거나, 랭킹 모델이 Twiddler 처리가 된 아웃풋을 활용하여 학습이 되어 exposure bias되지 않아야 한다는 것입니다. (이 과정은 마치..art와 magic의 영역..😉🪄)

#### 👀 Twiddler 필요한 이유
각 지면의 복잡도 또는 비즈니스 룰에 따라 해당 피드의 목적성을 objective function으로 표현하기 어렵습니다. 
* 문제점: **Ranking 결과를 후처리 없이 노출 = 콘텐츠 다양성이 떨어짐**
	* 유사한 콘텐츠가 반복 노출되는 문제 발생
		* 새로고침 후에도 기존 콘텐츠가 거의 그대로 유지됨
		* 단기간에 유저와 콘텐츠의 상호작용이 ranker의 스코어를 큰 폭으로 바꾸기 힘들기 때문
* 요구사항 예시
	* 전체 랭킹은 CTR(Click-through rate; 클릭률)을 최적화하되, 상위 30개의 포지션엔 최신 콘텐츠가 50% 이상으로 노출되어야함
	-   피드에 동일 유저가 (혹은 판매자가) 올린 사진이 연속으로 노출되지 않도록 해야함
	-   명절 관련 콘텐츠가 명절 시즌 1달부터는 상위 노출되었으면 좋겠는데, 명절이 끝난 직후엔 더 이상 노출되지 않도록 해야함
	-   유저가 1번 이상 클릭이나 좋아요를 눌렀거나, 5번 이상 노출된 콘텐츠는 아래로 downranking 하도록 해야함
- Ranking 단계에서의 고려
	- 이런 문제를 Ranking 단계에서 함께 고려할 수도 있긴 함
		* 유저가 본 콘텐츠를 feature로 함께 넣어서, 한번 본 콘텐츠는 클릭할 확률을 낮게 계산하도록 학습을 유도
	* 하지만 이를 다른 랭킹과 동일한 선에서 처리하기에는 더 높은 복잡도를 지닌 모델과 보다 많은 학습량이 필요함
		*  또다른 현실적인 해결책이 필요

해결책으로 사용한 방법은 Twiddler는 **Ranker가 만들어준 랭킹 결과를 사용자에게 전달하기 전에 조정해 주는 모듈**입니다. <br>
Twiddler가 전체 시스템에서 어느 부분에 위치하고 있고, 어떤 식으로 동작하는지에 대한 내용은 다음과 같습니다.

#### ↕️ Twiddler를 활용한 순위 조정 - 전체 구조
1. **Ohouse Log(공통구조)** : 오늘의집 애플리케이션에서 발생하는 모든 로그는 이곳에서 관리됨
2. **Kafka** : 유저 로그는 로그 관리 프로세스를 통해 실시간 전송됨
3. **Stream-Pipeline** : 유저가 소비하는 콘텐츠를 실시간으로 가공하여 Redis에 적재
	* 로그를 실시간으로 모니터링하고 필요한 정보만 뽑아서 바로 사용 가능한 형태로 가공하기 위한 프로세스
		* API 서버에서 직접 Kafka로 들어오는 모든 로그를 일일이 모니터링할 수 없음
		* Stream-Pipeline을 통해 실시간으로 가공하여 Twiddler가 사용할 수 있도록 해줌
4. **Twiddler** : 적대된 데이터를 활용하여 Ranker가 만들어준 순위를 조정
	* 단, Ranker가 만들어준 결과를 최대한 활용하기 위해 한 가지 원칙 존재
		* 동일한 조건을 지닌 콘텐츠 사이에서는 Ranker의 결과를 우선시한다는 원칙

![](https://res.cloudinary.com/bucketplace-co-kr/image/upload/v1720579623/%EB%8B%A4%EC%9A%B4%EB%9E%AD%ED%81%AC_%ED%8A%B8%EC%9C%84%EB%93%A4%EB%9F%AC_%EA%B7%B8%EB%A6%BC.png)


#### 💽 실시간 유저-콘텐츠 액션 정보 저장
사용자에게 노출되었던 콘텐츠를 낮은 순위로 내려주는 과정(downrank)인 Twiddler를 작동하기 위해서는 **최근 유저에게 노출되거나 유저가 클릭한 콘텐츠의 정보**가 필요합니다. 이를 위해 준실시간으로 유저의 행동을 트래킹할 수 있는 구조가 필요하여, Kafka와 Stream-Pipeline 시스템을 사용하였습니다.

* Stream-Pipeline
	* 유저가 애플리케이션에서 발생시킨 로그를 Kafka를 통해 실시간으로 전달받아 개발자가 활용 가능한 형태로 구성
		* 이때 유저와 콘텐츠 사이의 액션 정보만을 필터링하여 활용
	* 필수적인 정보만을 저장하는 형태로 파이프라인을 구성
		* 필터링 정보를 모두 저장하는 것이 이상적이지만, 네트워크 통신량과 Redis 자원을 고려해야하기 때문
		* 필수 정보 : 유저 식별자, 콘텐츠 식별자, 액션 타입, 액션 발생 시간 등

*관련된 자세한 부분은 생략하였습니다. 자세한 내용은 [해당 글](https://www.bucketplace.com/post/2024-07-10-%EA%B0%9C%EC%9D%B8%ED%99%94-%EC%B6%94%EC%B2%9C-%EC%8B%9C%EC%8A%A4%ED%85%9C-2-personalized-content-ranking/)을 통해 확인해주세요.*

#### 🛤️ Twiddler 종류
Twiddler는 다양한 목적에 따라 여러 종류로 나뉘어 사용됩니다. 하나의 추천 로직 상 여러 개의 Twiddler가 존재하며, 물론 모든 Twiddler가 항상 동일하게 사용되는 것은 아닙니다. <br>
➡️ 각 피드의 특성에 따라 특정 Twiddler가 추가되거나 사라질 수도 있음 <br>
➡️ 동일한 Twiddler를 사용하되 피드의 특성에 따라 파라미터가 서로 다르게 들어가기도 함

##### ✅ **Downrank Twiddler**: 클릭 가능성이 높은 콘텐츠를 조정하여 다양성을 높임
* 필요성 - Ranker로만 해결하기에는 변수가 너무 많이 존재
	* 한 콘텐츠가 반복해서 노출될수록 클릭할 가능성은 점점 낮아질 가능성이 높음
	* 이미 최근에 클릭한 콘텐츠라 관심도가 떨어지거나, 자기와 맞지 않다고 생각해서 클릭하지 않은 콘텐츠도 다시 노출될 수 있기 때문
	* 이러한 이유로 유저에게 한번 노출되거나 클릭한 콘텐츠는 해당 유저에게 노출되지 않게 하거나 최소한 피드의 상위에서는 제외하는 로직이 필요함
* 역할 - Ranker에 비해 가벼운 연산량을 지니며, 유저의 행동을 실시간으로 반영하여 콘텐츠의 노출 순서를 조정
	* 유저가 이미 클릭했거나 여러 번 반복 노출된 콘텐츠는 노출 순위를 기존보다 일정 이상 낮추게 됨
	* 이 로직을 기존에 비해 Rank를 낮춘다는 의미에서 Downrank라고 부름
	* 유저의 클릭 확률이 낮아진 콘텐츠를 Downrank 시킴으로서 유저에게 좀 더 다양한 콘텐츠를 노출해줌
* 입력 파라미터 구성 - 결정이 필요한 변수 부분은 상황에 맞게 활용하기 위함
	* 실제로는 어떤 경우에 Downrank가 일어날지, 어느 정도 Downrank가 필요할지, 어느 기간의 데이터를 활용할지도 고려가 필요
	* 파라미터 (통제 가능한 요소)
		* Downrank 시에 고려하기 위한 행동 로그의 기간
		* 각 액션별 Downrank가 일어나기 위한 기준
		* 각 액션별 Downrank 정도
	* 장점: Twiddler의 동작에 필요한 파라미터를 입력으로 제어 가능하게 해주면 상황에 따라 코드 수정 없이 유연한 동작 제어가 가능
		* 실 서비스 적용 전 AB Test를 통해 최적의 파라미터를 탐색하는 과정을 쉽게 진행할 수 있음
		* 실 서비스 적용 후에도 정책 변화에 따라 파라미터를 유연하게 조정하는 과정을 진행하고 있음

![](https://res.cloudinary.com/bucketplace-co-kr/image/upload/v1720581206/%EB%8B%A4%EC%9A%B4%EB%9E%AD%ED%81%AC_%ED%8A%B8%EC%9C%84%EB%93%A4%EB%9F%AC_%EA%B7%B8%EB%A6%BC8.png)

##### ✅  **Interval Twiddler** : 유사한 콘텐츠의 노출 간격을 조정하여 새로운 경험을 제공
* 필요성 - 유사한 콘텐츠의 순위 조정
	* 어떤 유저에게 추천된 콘텐츠 중 같은 카테고리의 콘텐츠나 동일한 유저가 올린 콘텐츠가 존재할 수 있음
	* 목적 : 추천 피드에서 사용자들이 최대한 라이프 스타일에 대한 새로운 경험을 접하게 하는 것
	* 그렇기 때문에 유사한 콘텐츠가 연속해서 노출되는 것은 저희의 서비스 목적에 적합하지 않아 최대한 지양하고자 함
* 역할 - 유사한 콘텐츠를 탐색하고 유사한 콘텐츠가 최소 간격을 두고 구성될 수 있도록 순위를 조정
	* "유사하다" 기준 :  미리 입력해둔 콘텐츠의 카테고리, 작성자 등의 메타 정보를 활용하며 최소 간격은 별도 파라미터로 결정 가능하도록 구성
		* 파라미터로 입력되는 최소 간격에 코드상 제한은 존재하지 않음
		* 내부적으로 유저가 일정 이상 스크롤 하는 동안 유사한 콘텐츠가 노출되지 않도록 자체적으로 최소 범위를 주고 있음

![](https://res.cloudinary.com/bucketplace-co-kr/image/upload/v1720581178/%EB%8B%A4%EC%9A%B4%EB%9E%AD%ED%81%AC_%ED%8A%B8%EC%9C%84%EB%93%A4%EB%9F%AC_%EA%B7%B8%EB%A6%BC7.png)


## 5. 향후 연구 방향성
추천 시스템 성능을 높이기 위해 sampling, feature, algorithm, serving 관점에서 다양한 접근 방식을 연구하고 있으며, 앞으로 하고자 하는 업무 리스트는 다음과 같습니다.

#### Ranker
1. **Multi-Objective Optimization**
	* as-is : 유저의 선호도를 예측하기 위해 최적화하는 objective는 CTR로 제한됨
	* 하지만 유저의 선호도는 단순 CTR 외에도 유저의 다양한 interaction과도 관련이 있음
		* 예1: A라는 유저와 B라는 유저가 똑같은 콘텐츠를 클릭=> A가 콘텐츠 페이지에서 보낸 시간 > B가 보낸 시간
		* 예2: A가 B보다 더 많은 scrolling 함 => A가 B보다 콘텐츠를 더 선호한다는 근거로 사용
	* CTR뿐만 아니라 time, scroll on page 등을 추가적인 objective로 사용하여 유저의 선호를 더 정확하게 예측할 수 있음

2. **신규 콘텐츠 예측을 위한 Feature 추가**
	* 신규로 업로드된 콘텐츠 : 유저가 콘텐츠에 대해 생성한 선호도 피드백이 부족 -> 예측 성능이 저하
	* CTR 예측 시 신규 콘텐츠에 대해서는 유저 피드백 대신 콘텐츠 기반의 feature에 더 의존해야 함
	* 신규 콘텐츠의 속성 관련 feature를 실시간으로 추출하는 것이 중요

3. **User/Item Representation**
	* as-is : user demography feature, content meta feature
	* to-be : 유저, 아이템을 잘 설명하는 representation
	* 예: 유저-아이템 간 상호작용 학습에 효과적인 MF, GNN을 이용
		* user, item vector를 추출 후, pCTR 모델에 feature로 사용하거나 분석에 활용

4. **Model Architecture 변경**
	* 새로운 모델 아키텍처로 지표 및 latency 개선을 시도
	* 예: two tower model = vector search를 기반으로 dot product 연산을 사용
		* 추천 후보군을 가져오는 retrieval에 이점이 있어 ranking inference 시간을 줄일 수 있음
		* tower마다 specific한 task에 fit 하도록 학습할 수 있음 => 랭킹 결과 분석 및 콜드스타트 문제에 활용

#### Twiddler
1. **파라미터 자동 결정**
	* as-is : 자체적인 실험을 통해 Twiddler의 파라미터를 결정하고 있음
		* 실험에는 한계가 존재
		* 시간이 지나면서 데이터나 사용자의 특성이 변화 => 완벽한 해결책 X
	* to-be : 최근 데이터 동향에 맞는 최적의 파라미터 결정이 필요
		* 이를 위해 online learning 혹은 주기적인 배치를 통한 최적의 파라미터 탐색 등을 추가하여 지속적인 성능 유지가 가능

2. **Twiddler 동작의 유연화**
	* as-is : 비즈니스 로직이 추가되고 신규 기능이 필요해질 때마다 Twiddler의 코드 작업이 필요함
		* 이로 인해 코드의 복잡도가 점점 올라가고, 비즈니스 로직이 반영되는데 시간이 오래 소모됨
	* to-be : 간단히 로직을 구성할 수 있는 스크립트 코드를 삽입하는 형태로 구성
		* Twiddler 동작을 유연하게 구성할 수 있음
		* 신규 로직이 필요해질 경우 코드 수정 없이 빠르게 대응할 수 있음
	* 다만 이러한 로직은 머신 환경에 영향을 받을 수 있음 + 전체 프로세스의 Latency를 증가시킬 가능성이 있음
		* 이러한 점을 고려하여 부작용을 최소화하는 방향의 개발이 필요


<br>

#### Reference
[1]  [Youtube Deep Learning Recommendation Paper](https://research.google/pubs/deep-neural-networks-for-youtube-recommendations/) <br>
[2] [Twitter Recommendation Server](https://blog.x.com/engineering/en_us/topics/open-source/2023/twitter-recommendation-algorithm) [[code](https://github.com/twitter/the-algorithm)] <br>
[3]  [Instagram Recommendation System](https://engineering.fb.com/2023/08/09/ml-applications/scaling-instagram-explore-recommendations-system/) <br>
[4]  [TikTok Recommendation System](https://support.tiktok.com/en/using-tiktok/exploring-videos/how-tiktok-recommends-content) <br>
[5]  [Field-weighted Factorization Machines for Click-Through Rate Prediction in Display Advertising](https://arxiv.org/abs/1806.03514) <br>
[6]  [DeepLight: Deep Lightweight Feature Interactions for Accelerating CTR Predictions in Ad Serving](https://arxiv.org/abs/2002.06987) <br>
[7]  [UMAP: Uniform Manifold Approximation and Projection for Dimension Reduction](https://arxiv.org/abs/1802.03426)

-------
