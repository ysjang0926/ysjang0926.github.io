---
layout: post
title:  "Netflix의 추천 파운데이션 모델: 더 이상 '모델'이 아닌 '인프라' 관점으로"
subtitle:   "Let's not be a fool"
categories: etc
tags: etc_til
comments: true
---

- Netflix의 추천 시스템 아키텍처를 통합하고 확장 가능한 방식으로 재설계한 내용을 다룹니다✍🏻
- 내용 그대로 이해하고 싶으시면, 원본 콘텐츠([Foundation Model for Personalized Recommendation](https://netflixtechblog.com/foundation-model-for-personalized-recommendation-1a0bd8e02d39))를 읽어보시길 권장 드립니다. 원문을 통해 더욱 깊이 있는 정보를 얻으실 수 있을 거에요😉

---------

# 🎯 동기 및 배경
Netflix의 개인화 추천 시스템은 매우 복잡하게 구성되어 있는 시스템입니다. "Continue Watching(이어서보기), "Today’s Top Picks for You(오늘의 추천)" 등 각각의 니즈를 충족시키는 다양한 특화된 머신러닝 모델들이 따로 운영되고 있었거든요. <br>
하지만, 비즈니스 니즈가 늘어남에 따라 개인화 알고리즘 세트를 확장하면서 추천 시스템 유지보수 비용이 상당히 증가했습니다. 게다가, 대부분의 모델이 공통 데이터를 사용하긴 하지만 독립적으로 학습되기 때문에, 한 모델에서 이루어진 잘된 아이디어나 기술을 다른 모델로 이전하기가 어렵다는 문제가 있어요. <br>

이런 상황을 통해 Netflix는 "회원 선호 학습을 중앙 집중화하고, 다양한 모델 전반에서 접근성과 활용도를 높일 수 있는" 새로운 추천 시스템 아키텍처가 필요하다고 판단했습니다. <br>

특히, 기존 추천 모델들은 대부분 Netflix 플랫폼 내에서 사용자의 최근 상호작용 이력으로부터 특성(features)을 추출하는 구조였습니다. 대부분의 경우, 서비스 지연(latency)이나 학습 비용 문제 때문에 아주 짧은 기간의 이력에만 한정되는 경향이 있었습니다. <br>
이걸 바꾸기 위해 Netflix는 유저의 포괄적인 상호작용 이력과 Netflix의 콘텐츠에 대한 정보를 대규모로 통합하는 '파운데이션 모델'을 개발하기로 합니다. 이 모델은 다른 모델들에게 이러한 학습 결과를 공유 모델 가중치(for fine tuning) 또는 임베딩을 통해 직접적으로 전달할 수 있도록 설계되었습니다. <br>

이런 구조 변화는 사실 자연어 처리(NLP) 분야에서 대형 언어 모델(LLM)로의 패러다임 전환에서 이미 검증된 방식이기도 했습니다. NLP 분야에서는 수많은 소규모 특화 모델들 대신, 단일 대형 언어 모델이 다양한 작업을 직접 수행하거나 최소한의 파인튜닝으로 처리하는 방향으로 이동하고 있습니다. <br>
여기서 얻은 주요 인사이트트 두가지 입니다.
- **데이터 중심 접근(Data-Centric Approach)**: 피처 엔지니어링에 크게 의존하는 모델 중심 전략에서 벗어나, 최대한 많은 고품질 데이터를 축적하여 가능한 한 엔드투엔드 학습을 목표로 하는 접근입니다.
- **반지도 학습(Semi-Supervised Learning) 활용**: LLM에서의 next-token prediction objective는 매우 효과적임이 입증되었습니다. 이를 통해 라벨이 없는 대규모 데이터를 활용한 반지도 학습이 가능하며, 동시에 모델이 세계 지식(world knowledge)에 대한 깊은 이해를 갖추도록 도와줍니다.

Netflix는 이런 LLM 패러다임을 추천 시스템에도 적용해보기로 한거죠. 더 많은 데이터, 더 큰 모델, 더 긴 히스토리, 그리고 엔드투엔드 학습을 통해 확장 가능하고 효율적인 추천 시스템을 만드는 것이 목표였습니다. <br>
**➡️ 최종목표 : 현재의 니즈를 충족할 뿐만 아니라 앞으로 변화하는 요구사항에 동적으로 적응할 수 있는 모델을 개발하고, 지속 가능한 혁신과 자원 효율성을 보장하는 것** <br>

<br> <br>

# 📊 데이터 처리 방식
Netflix에서 사용자들의 이용 행태는 정말 다양합니다. 그냥 둘러보기만 하는 사람도 있고, 영화나 시리즈를 끝까지 보는 사람도 있습니다. <br>
2024년 말 기준, Netflix는 전 세계적으로 3억 명 이상의 사용자를 보유하고 있으며, 이들이 남긴 상호작용 데이터는 무려 수억 건에 달합니다. 이 규모는 거의 대형 언어 모델(LLM)에서 다루는 토큰 수와 비교할 만한 수준입니다.  <br>

그러나 데이터나 많다고 무조건 좋은 건 아니잖아요? 중요한건 LLM과 마찬가지로 데이터의 양보다 **품질**입니다. Netflix는 이러한 데이터를 제대로 활용하기 위해 **상호작용을 토큰화(tokenization)"라는 과정을 더입했습니다. 의미 있는 이벤트를 식별하고, 중복을 최대한 줄이는 전략입니다. <br>

### 사용자 상호작용 토큰화(Tokenizing User Interactions)
모든 사용자 행동이 선호도를 이해하는 데 동일하게 중요하게 기여하는 것은 아닙니다. 그래서 Netflix는 행동 시퀀스 안에서 진짜 의미있는 '토큰'이 무무엇인지를 정의하기 시작했어요. 이때 토큰화는 **시퀀스 내에서 의미 있는 "토큰"이 무엇인지 정의**하는 데 도움이 됩니다. <br>

NLP의 Byte Pair Encoding(BPE) 방식과 유사하게, 인접한 행동들을 합쳐서 새로운 고차원의 토큰을 만드는 방식인데, 차이점이 있다면 어떤 정보는 꼭 남겨야 한다는 것입니다. 즉, 언어 토큰화와 달리 이러한 새로운 토큰을 생성할 때 어떤 정보를 보존할지 신중히 고려해야 합니다. <br>
예를 들어, 같은 타이틀을 여러번 본 기록이 있다면, 단순히 merge하는 게 아니라 총 시청시간이나 행동 타입 같은 중요한 정보는 따로 보존하는거죠. <br>

아래 그림은 같은 타이틀에 대한 여러 행동을 하나로 묶되, 중요한 정보는 남기는 방식으로 토큰화하는 예시입니다. 

![image](https://github.com/user-attachments/assets/b816f32c-6e81-460c-ab51-8af3a1fc01a5)

여기서 중요한 건 '시퀀스 길이 vs. 정보 손실' 사이의 균형입니다. 즉, **상호작용 히스토리의 길이와 각 토큰에 보존되는 세부정보 수준 사이의 균형을 맞추는 것**입니다. 너무 손실(lossy)이 많은 토큰화는 중요한 시그널을 잃게 만들 수 있고, 반대로 지나치게 세밀한 시퀀스는 처리 시간과 메모리 측면에서 실용적인 한계를 넘을 수 있습니다. <br>

게다가 Netflix처럼 heavy user가 많은 서비스에서는 한 사람의 시퀀스가 수천 개 행동으로 늘어날 수도 있어요. 하지만 Transformer 모델이 한 번에 처리할 수 있는 context window는 한정적이기 때문에, 이런 긴 시퀀스를 그대로 넣기는 어렵죠. 특히 추천 시스템은 latency가 엄청 민감해서 millisecond 단위로 응답해야 하니까 더 제한이 많습니다. <br>

#### 긴 히스토리를 처리하기 위한 두가지 전략
그래서 Netflix는 이런 문제를 해결하기 위해 학습 단계에서는 두 가지 핵심 전략을 썼습니다.
- **Sparse Attention Mechanisms(희소 어텐션 메커니즘)**: low-rank compression 같은 희소 어텐션 기법을 활용해 context window를 수백 개 이벤트까지 확장하면서 계산 효율성을 유지합니다. 이를 통해 더 긴 상호작용 히스토리를 처리하며 장기 선호도를 더 잘 파악할 수 있습니다.
- **Sliding Window Sampling(슬라이딩 윈도우 샘플링)**: 학습 시 전체 시퀀스에서 겹치는 윈도우들을 샘플링하여, 여러 epoch에 걸쳐 사용자의 전체 히스토리의 다양한 부분을 모델이 학습할 수 있도록 합니다. 너무 큰 context window를 요구하지 않으면서도 전체 시퀀스 학습 효과를 확보할 수 있습니다.

그리고 추론 단계에서는 multi-step decoding이 필요한 경우, KV caching(키-값 캐싱)을 활용해 과거 계산 결과를 효율적으로 재활용하면서 낮은 지연 시간을 유지합니다. (latency 문제 해결) <br>

이러한 접근 방식들을 통해, 장기적이고 세밀한 상호작용 행동 패턴을 정교하게 학습하면서도 모델 학습/추론의 서비스 지연이나 메모리 한계와 같은 실용적 제약 간의 균형을 맞출 수 있게 되었습니다. 이를 통해 추천 시스템의 정밀도와 확장성을 모두 개선할 수 있었습니다. <br>

### 각 '토큰' 내 정보 설계(Information in Each 'Token')
사용자 상호작용 시퀀스를 구조화하는 첫 단계가 끝나면, 다음으로 중요한 것은 **각 토큰에 포함되는 정보 자체를 어떻게 정의**할 것인지입니다. <br>

토큰화 과정을 거치고 나면, 각 토큰 안에는 꽤 다양한 정보가 들어가게 됩니다.
- 행동 자체의 속성 (지역, 시간, 길이, 기기 종류 등)
- 콘텐츠 정보 (아이템 ID, 장르, 출시 국가 등 메타데이터)

LLM은 토큰 하나에 그냥 단일 임베딩만 넣는 경우가 많은데, 추천 쪽은 그럴 수 없어요. <br>
행동마다 이런 heterogeneous한 정보들이 많기 때문에, 이러한 특성들 중 대부분은 categorical feature로 embedding으로 처리하고, 시간 같은 건 추가 전처리를 거쳐야 했습니다.. <br>
특히 타임스탬프(time stamp)는 절대 시간 개념과 상대 시간 개념 모두를 반영할 수 있도록 별도의 처리 단계가 필요합니다. 특히 절대 시간은 시간에 민감한 행동을 이해하는 데 매우 중요해요. <br>

시퀀스 기반 추천 시스템에서 예측 정확도를 높이기 위해, Netflix는 각 토큰의 특성을 두 가지로 나눠서 관리합니다.
- **Request-Time Features(요청 시점 특성)**: 예측 시점에 이용 가능한 특성들 (로그인 시간, 디바이스, 위치 등)
- **Post-Action Features(행동 후 특성)**: 행동이 끝난 후에야 알 수 있는 정보들 (어떤 콘텐츠와 상호작용했는지, 시청 시간 등)

다음 상호작용을 예측할 때, 우리는 현재 스텝의 요청 시점 특성과 직전 스텝의 행동 후 특성을 결합합니다. 이를 통해 각 토큰은 직전 행동 패턴과 현재 상황 정보를 모두 포괄할 수 있게 되어, 모델이 다음 행동을 더 잘 예측할 수 있습니다. <br>

<br><br>

# 🏗️ 모델 목표 및 구조
Netflix가 파운데이션 모델을 학습시킬 때 기본적으로 사용하는 전략은 GPT처럼 **next-token prediction objective 방식**이에요. 쉽게 말하면, 유저 행동 시퀀스를 보고 다음 행동이 뭔지 예측하는 것입니다. 이게 좋은 이유는 라벨링되지 않은 대규모 사용자 상호작용 데이터를 효과적으로 활용할 수 있게 해준다는 점 때문이에요. 실제로 추천 시스템에서도 이미 여러 성공 사례들이 존재합니다. <br>

하지만 언어 작업(language tasks)과 추천 작업(recommendation tasks) 간의 뚜렷한 차이점을 고려하여, Netflix는 추천이라는 도메인 특성상 몇가지 중요한 튜닝 포인트가 필요했어요.<br>

#### 서로 다른 상호작용에 다른 가중치 부여 (예: 전체 영화 시청 vs 예고편 시청)
일반적인 GPT 같은 LLM들의 pretraining phase(사전학습 단계)에서는 모든 target token(목표 토큰)이 동일한 가중치를 가지고 학습됩니다. <br>
반면, Netflix 모델에서는 모든 사용자 상호작용이 동일하게 중요한 것은 아닙니다. 예를 들어, 2시간짜리 영화 전체를 본 거랑, 5분짜리 예고편 본 거랑 똑같이 취급하면 안 되는거죠. 그래서 Netflix는 행동마다 중요도를 다르게 주는 설계를 고민했습니다. <br>

#### 장기 종속성 파악을 위한 다중-토큰 예측 사용
이번 도전 과제 중 하나는 'long-term user satisfaction을 특정 상호작용 또는 추천 결과와 정렬(alignment)시키는 것'이었잖아요? 그래서 보통 next-token prediction은 바로 다음 1개만 예측하는데, Netflix는 그걸 넘어서 next-n-token을 한꺼번에 예측하게 했어요. <br>
이렇게 하면 장기적인 패턴(long-term dependencies)을 더 잘 학습할 수 있고, 너무 당장 다음 행동만 보는 근시안적 예측(myopic prediction)을 피할 수 있습니다. <br>

#### 아이템 ID 예측과 함께 보조 예측 목표(장르 등) 활용
Netflix는 아이템 ID만 예측하게 하지 않았어요. input data 내 여러 필드를 auxiliary prediction objectives로 활용한거죠.  <br>
예를 들어, 원래 시퀀스 내 아이템들로부터 장르 정보를 추출하고 이를 auxiliary target(보조 타겟)으로 사용하는 방식입니다. 이러한 접근은 여러 가지 장점이 있습니다.
- noisy한 아이템 ID 예측에 대한 regularizer(규제자) 역할
- 사용자 의도나 장기적 장르 선호에 대한 추가적 인사이트 제공
- 계층적 구조(hierarchical structure)를 활용하여 타겟 아이템 ID 예측 정확도 향상

특히 보조 타겟(예: 장르, 원어 등) 예측을 먼저 시키면, 모델이 후보군(candidate list)을 좁히는 데 도움이 되고 이후 아이템 ID 예측이 더 쉬워집니다. <br>

결국 Netflix가 설계한 추천 파운데이션 모델은,
- 유저 히스토리 전체를 보고
- 행동별 중요도 고려하고
- 여러 타겟을 동시에 예측하며
- 장기 패턴까지 학습하는 그런 구조라는 거죠.

<br><br>

# 🧩 고유한 도전 과제
물론 이런 파운데이션 모델을 만든다고 해서 끝은 아니죠. <br>
추천 시스템 파운데이션 모델을 구축하면서, LLM에서 공통적으로 나타나는 '대규모 사용자 상호작용 데이터로 큰 모델을 학습할 때 발생하는 인프라 문제' 외에도, 추천 도메인 특유의 몇 가지 고유한 도전 과제가 존재합니다. 그 중 가장 대표적인 것은 '**엔티티 콜드스타트(Entity Cold-Starting)'** 문제입니다. <br>

LLM은 어휘(vocabulary)가 안정적이라 신규 토큰 추가 이슈가 거의 없는데, 추천 시스템은 상황이 다릅니다. <br>
Netflix는 "세상의 모든 사람을 즐겁게 한다"는 미션을 가지고 있으며, 새로운 타이틀이 자주 카탈로그에 추가됩니다. 따라서 추천 파운데이션 모델은 콜드스타트 역량, 즉 아직 아무도 상호작용하지 않은 신규 타이틀에 대해서도 회원들의 선호도를 예측할 수 있는 어려운 과제가 놓여져 있는거죠. <br>

이를 가능하게 하기 위해, Netrflix는 파운데이션 모델 학습 프레임워크에 다음 두가지 핵심 전략을 포함시켰습니다.
- Incremental Training(점진적 학습)
- Unseen Entity Inference(보지 못한 엔티티에 대한 추론)

### Incremental Training
**✅ 이전 모델 파라미터로 새모델 워밍업하는 점진적 학습** <br>
파운데이션 모델은 모든 회원의 시청 및 행동 히스토리를 포함하는 방대한 데이터셋으로 학습되기 때문에, 자주 재학습(retraining)하는 것은 현실적으로 어려워요. <br>
하지만 Netflix의 카탈로그와 회원 선호는 계속 변화합니다. LLM은 상대적으로 안정적인 토큰 vocabulary를 사용하기 때문에 점진적 학습이 용이하지만, 추천 모델은 새로운 타이틀이 등장할 때마다 새로운 임베딩이 필요하게 되죠. 따라서 embedding layer와 output component가 지속적으로 확장되어야 합니다. <br>

이를 해결하기 위해 우리는 **warm-start 기법**을 사용합니다. 기존 모델의 파라미터를 re-use하면서, 새로운 타이틀을 위한 파라미터만 새롭게 초기화합니다. <br>

예를 들어, 신규 타이틀의 임베딩은 기존 타이틀 임베딩 평균에 약간의 랜덤 노이즈를 추가하거나, 메타데이터 기반으로 유사 타이틀들의 임베딩 가중 평균을 사용해 초기화할 수 있습니다. 이렇게 하면 새로운 타이틀이 관련성 높은 임베딩으로 시작할 수 있어 파인튜닝 속도가 빨라집니다. 실제로는, 회원 상호작용 데이터가 충분히 쌓이면 초기화 방식 자체는 큰 영향을 미치지 않게 됩니다.
* 기존 타이틀 임베딩 평균값 + 약간의 랜덤 노이즈 → 신규 타이틀 초기화
* 또는, 메타데이터 기반으로 유사 타이틀들의 임베딩 평균값 활용

결국 데이터가 조금만 쌓여도 금방 fine-tuning 가능하도록 설계한 거죠. <br>

### Dealing with Unseen Entities
**✅ 학습 가능한 ID 임베딩과 메타데이터 기반 임베딩 결합** <br>
점진적 학습을 하더라도 신규 타이틀(launch 이후 추가되는 엔티티) 학습이 항상 빠르게 이루어질 것이라는 보장은 없습니다. 특히, 파운데이션 모델을 자주 파인튜닝하더라도 학습 데이터에 포함되지 않은 새로운 엔티티가 등장하는 경우가 존재할 수 있습니다. <br>

따라서, 추천 파운데이션 모델은 회원 상호작용 데이터뿐 아니라, 엔티티와 입력(input)의 메타데이터 정보 또한 적극 활용할 수 있어야 합니다. <br>
Incremental Training으로도 안 잡히는 신규 아이템은 어떻게 할까요? 여기서 Netflix가 쓴 전략이 바로 'Metadata Embedding + ID Embedding 조합'입니다. 이때 learnable item ID embedding과 metadata embedding을 결합하여 최종 임베딩을 구성합니다. <br>

아래 그림을 통해 각 타이틀은 장르, 스토리라인, 톤 등 다양한 메타데이터와 연결되는 것을 확인할 수 있습니다. 각각의 메타데이터 타입은 해당 임베딩들의 평균으로 표현될 수 있고, 이를 연결(concatenation)하여 타이틀의 메타데이터 기반 임베딩을 구성합니다.

![image](https://github.com/user-attachments/assets/75de6c6a-d216-44c5-a53b-ad793014ba57)

**✅ 콘텐츠 "나이"에 따른 어텐션 메커니즘으로 균형 조정** <br>
최종 타이틀 임베딩은 이 메타데이터 기반 임베딩과 학습 가능한 ID 기반 임베딩을 mixing layer를 통해 결합하여 만듭니다. 여기서 재밌는 건 mixing 방식인데, 아이템 'age'를 기준으로 가중치를 조절합니다. 단순 합산이 아니라, 엔티티의 'age(출시 경과 시간)'를 기반으로 하는 attention mechanism을 사용합니다.
* 신규 아이템 → Metadata 쪽에 더 의존
* 오래된 인기 아이템 → ID 기반 임베딩에 더 의존

이렇게 하면, 상호작용 데이터가 적은 신규 타이틀은 메타데이터 기반 임베딩에 더 많이 의존하고, 충분한 데이터가 쌓인 타이틀은 ID 기반 임베딩에 더 많이 의존하게 됩니다. 비슷한 메타데이터를 가진 타이틀이라고 하더라도 사용자 참여도는 다를 수 있기 때문에, 임베딩이 그 차이를 반영해야 합니다. <br>

게다가 학습 과정에서는 일부러 약간의 랜덤성을 도입하여 모델이 ID 임베딩에만 의존하지 않고(의존도 떨어뜨리기) 메타데이터 기반 정보도 잘 활용할 수 있도록 학습하게끔 유도합니다. 이러한 방식 덕분에, 출시 직후이거나 아직 사용자 상호작용 데이터가 전혀 없는 신규 타이틀도 꽤  reasonable(합리적인)한 임베딩으로 cold start 문제를 완화할 수 있게 됩니다. <br>

<br><br>

# 🛠️ 다운스트림 응용 및 활용
Netflix 추천 파운데이션 모델은 장기적인 회원 선호도를 이해하도록 설계되었으며, 그렇게 해서 만들어진 모델은 실제로 어디에 쓰고 있냐 하면 크게 세 가지 방식으로 활용 중입니다.

#### Direct Use as a Predictive Model (예측 모델로 바로 사용)
**✅ 여러 예측 헤드를 가진 예측 모델로 직접 사용** <br>
가장 직관적인 방식이에요. 모델은 기본적으로 사용자가 다음에 어떤 엔티티(아이템)와 상호작용할지 예측하도록 학습되었습니다. 즉, 모델이 사용자 행동 시퀀스를 보고 다음에 어떤 콘텐츠를 소비할지 직접 예측합니다. <br>
다양한 작업을 처리할 수 있는 여러 개의 predictor head를 포함하고 있으며, 예를 들어 특정 장르에 대한 회원 선호도를 예측하는 등의 작업이 가능합니다. 이렇게 하면 모델 하나로 다양한 예측 문제를 동시에 풀 수 있어서, 이러한 predictor head들은 다양한 비즈니스 니즈를 직접 충족시키는 데 사용될 수 있습니다. <br>

#### Utilizing Embeddings (임베딩 활용)
**✅ 구성원 및 엔티티(비디오, 게임, 장르)에 대한 임베딩 활용** <br>
모델은 회원, 비디오, 게임, 장르 등과 같은 엔티티들에 대한 가치 있는 임베딩을 생성합니다. <br>
이 임베딩들은 배치 작업(batch job)을 통해 사전 계산되어 저장되고, 오프라인 및 온라인 애플리케이션 모두에서 사용됩니다. 예를 들어, 후보 아이템 추천(candidate generation) 작업에서 유용하게 쓰일 수 있으며, 특정 회원에게 매력적일 수 있는 타이틀을 검색하는 데 사용됩니다. <br>

**✅ 다른 모델 학습과 후보 생성에 임베딩 활용** <br>
파운데이션 모델은 user, title, genre 같은 다양한 엔티티에 대한 embedding을 뽑아낼 수 있습니다. 이 임베딩은 배치 작업(batch job)으로 미리 계산해두고, 온라인 서비스나 다른 추천 모델에서도 feature로 가져다 쓸 수 있게 했어요. <br>

대표적인 활용 예시는 다음과 같습니다.
* candidate generation (후보 아이템 추천)
* title-to-title recommendation (비슷한 콘텐츠 추천)

**✅ 모델 버전 간 임베딩 공간 안정화를 위한 직교 저차원 변환 적용** <br>
여기서 생기는 현실적인 고민이 하나 있었어요. 모델을 다시 학습하거나 배포할 때마다 embedding space(임베딩 공간)가 바뀌면 downstream 서비스들이 버그날 수 있거든요. 예를 들어, 특정 차원의 값에 의미를 부여해놨다면 그게 깨질 수 있다는 뜻입니다. <br>

이 문제를 해결하기 위해 Netflix는 'Orthogonal Low-Rank Transformation(직교 저차원 변환)' 기법을 도입했습니다. 이 방식은 모델을 새로 학습하더라도 embedding space 구조는 최대한 안정적으로 유지되도록 만들어줍니다. <br>

#### Fine-Tuning with Specific Data (특정 데이터로 파인튜닝)
**✅ 특정 애플리케이션을 위한 미세 조정 가능** <br>
마지막으로 Netflix가 강조하는 건 '유연성'이에요. 모델 전체를 그대로 쓸 수도 있고, 일부분(subgraph)만 떼서 가져가서 파인튜닝할 수도 있게 만들었습니다. 이렇게 하면 사용자는 파운데이션 모델 전체 또는 서브그래프(subgraph)를 자신의 모델에 통합하고, 비교적 적은 데이터와 계산 자원만으로 파인튜닝할 수 있습니다.  <br> 

이러한 접근 방식은 파운데이션 모델 초기 학습 시 엄청난 자원이 들더라도, 이후 파인튜닝을 통해 기존 특화 모델들과 유사한 성능을 달성할 수 있게 해줍니다. <br>

<br><br>

# 📈 확장성 
Netflix가 이런 파운데이션 모델을 더 잘 굴리기 위해 마지막으로 고민한 건 "스케일링(scaling)"입니다. <br>

Netflix 추천을 위한 파운데이션 모델을 확장하는 과정에서, 대형 언어 모델(LLM)의 성공에서 많은 영감을 얻었습니다. 대형 언어 모델(LLM)에서도 파라미터 수, 데이터량, 컨텍스트 길이를 늘리면 성능이 좋아진다는 건 이미 알려진 사실인데요. Netflix도 비슷하게, 생성 기반 추천(generative recommendation) 작업에서도 **스케일링**이 매우 중요하다는 사실을 확인했습니다. <br>

성공적인 스케일링을 위해서는 다음이 필수적입니다.
- Robust Evaluation(평가체계): 모델 성능을 예민하게 구분할 수 있어야 한다.
- Efficient Training Algorithms(효율적 학습 구조): 데이터를 최대한 잘 먹여야 한다.
- Sufficient Computing Resources(컴퓨팅 자원 확보): 결국 돈... GPU...

평가 체계는 모델 성능을 효과적으로 구별하고 개선 지점을 식별할 수 있어야 합니다. 스케일링은 **데이터, 모델, 컨텍스트 세 가지 측면**에서 이루어집니다. 여기에는 사용자 상호작용 데이터, 외부 리뷰, 멀티미디어 자산, 고품질 임베딩 등이 포함됩니다.
- Data Scaling : 사용자 행동 데이터, 외부 리뷰, 멀티미디어 데이터 등 최대한 많이 활용
- Model Scaling : 파라미터 수 늘리기
- Context Scaling : 더 긴 유저 히스토리 넣기

실험 결과, 스케일링 법칙(scaling law)은 추천 파운데이션 모델에서도 적용 가능함이 확인되었습니다. 데이터와 모델 크기를 증가시킬수록 일관되게 성능이 향상되는 것을 확인할 수 있었습니다.<br>

![image](https://github.com/user-attachments/assets/9df85d68-1ab2-4966-b147-397fa0a35887)

위 그래프는 모델 파라미터 크기와 상대적 성능 향상 간의 관계를 보여줍니다. 추천 모델링에서의 스케일링 법칙(scaling law)을 보여주며, 모델 크기가 커질수록 성능이 증가하는 추세를 나타냅니다. x축은 다양한 규모 차원에서 성장을 강조하기 위해 로그 스케일로 표현되었습니다.

<br><br>

# 🙌🏻 결론
Netflix의 개인화 추천을 위한 파운데이션 모델은 대규모 데이터를 활용하여 회원들에게 더 높은 품질의 추천을 제공하는 통합적이고 데이터 중심적인 시스템 구축의 중요한 진전을 의미합니다. <br>
정리하면, Netflix 파운데이션 모델의 핵심 가치는 다음과 같습니다.
- 데이터를 최대한 통합해서
- 장기 행동 패턴까지 학습하고
- 여러 서비스/모델이 공유해서 쓰고
- 신규 콘텐츠에도 빠르게 적응할 수 있고
- 리소스 효율성까지 고려한 설계

특히 LLM에서 성공적으로 검증된 반지도 학습 방식과 엔드투엔드 학습 구조를 그대로 추천 시스템에 잘 이식했다는 점이 인상적입니다. <br>

앞으로도 cold start, presentation bias 같은 추천 도메인 고유의 문제들을 해결하면서, 파운데이션 모델을 다양한 서비스에 확산시켜 나갈 예정이라고 합니다.

Netflix가 다양한 특화 모델들에서 점점 하나의 공통 기반 모델로 넘어가는 이 변화, 추천 시스템을 고민하는 사람이라면 꼭 눈여겨볼 만한 흐름 같습니다👍🏻

<br>

--------------

### Think
이 글 읽으면서 가장 인상 깊었던 건 Netflix가 '추천 시스템을 위한 파운데이션 모델'을 굳이 이렇게까지 무겁게 설계한 이유였습니다. 그냥 "추천 잘 되게 하는 모델 만들었다" 수준이 아니라, 서비스 전반에 걸쳐 누구나 가져다 쓸 수 있는 공통 인프라처럼 설계하려고 엄청나게 고민하고, 그걸 시스템화한 느낌? <br>

특히 LLM이 바꾼 패러다임을 비-언어 도메인(=추천)에 이렇게까지 자연스럽게 녹여낸 건 진짜 쉽지 않은 일인데, Netflix니까 가능한 방향성이 아닐까 싶었습니다👍🏻 개인적으로도 앞으로 추천 시스템 설계나 ML Infra 고민할 때 "데이터 중심 설계"나 "학습/서빙 효율성" 관점에서 이 아키텍처 흐름을 자주 떠올리게 될 것 같아요. <br>

결론: 추천도 점점 '인프라의 시대'구나!
