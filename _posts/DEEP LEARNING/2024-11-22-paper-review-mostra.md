---
layout: post
title:  "추천시스템도 고민이 있다: 사용자와 플랫폼 중심의 목표를 모두 만족시킬 수 있을까?"
subtitle:   "Spotify - Generalist-Specialist Score"
categories: data
tags: dl
comments: true
use_math: true
---

- 해당 포스팅은 스포티파이가 음악 추천시스템에서 사용자, 아티스트, 그리고 플랫폼 간의 목표(objective)를 어떻게 조화롭게 균형 잡는지를 다룬 글입니다. 🎧
- 사용자의 단기적 만족뿐만 아니라, 새로운 음악 탐색, 신예 아티스트 노출, 그리고 플랫폼의 전략적 목표를 동시에 달성하기 위해 개발된 Mostra 프레임워크를 소개하고자 합니다.
- 이 글에서는 Mostra가 어떻게 서로 다른 목표들 사이에서 트레이드오프(Trade-off)를 조정하며, 추천시스템의 유연성과 실시간 조율 능력을 강화했는지 살펴보겠습니다.

----------

# 음악 추천 시스템의 딜레마와 스포티파이의 새로운 접근법
음악 스트리밍 플랫폼에서 가장 중요한 목표 중 하나는 **"사용자가 좋아할 곡을 추천하는 것"**입니다. 하지만 여기에는 큰 딜레마가 존재합니다. 인기 있는 곡 위주로 추천하면 사용자는 만족할 수 있지만, 이는 신예 아티스트의 노출이 줄어들고 플랫폼 생태계의 다양성을 해칠 위험이 있습니다.  <br>
👉🏻 **문제점**: 사용자를 기쁘게 하려다 보면 신예 아티스트의 노출 기회가 줄어들고, 플랫폼의 장기적인 지속 가능성에도 부정적인 영향을 미칠 수 있음

### Mostra: 사용자와 플랫폼의 균형을 잡는 프레임워크
스포티파이는 이러한 문제를 해결하기 위해 **Mostra 프레임워크**를 제안했습니다. Mostra는 단순히 사용자의 만족도를 최적화하는 것에 그치지 않고, 플랫폼 전체의 건강한 생태계를 유지하기 위해 다음의 주요 objective를 조화롭게 통합합니다:  
* **사용자 만족(Short-term User Satisfaction)**
* **새로운 음악 탐색(Discovery)**
* **신예 아티스트 노출(Exposure)**
* **특정 콘텐츠 부각(Boost)**  

Mostra는 **Set Transformer**를 기반으로 설계된 모델로, 다양한 objective를 균형 있게 고려하며 각 objective 간의 상충 관계를 효과적으로 조율합니다. 또한, **Beam Search**를 활용해 실시간으로 곡 순서를 최적화하며, 단기적인 사용자 경험과 장기적인 플랫폼 목표를 동시에 만족시킬 수 있는 구조를 제공합니다.  

<br>

논문 핵심이 **"multi-objective 최적화"**이다보니, 이번 글에서는 각 objective에 대한 정의, Mostra 프레임워크의 작동 원리, 그리고 스포티파이가 추천시스템의 유연성과 실시간 조율 능력을 어떻게 강화했는지에 초점을 맞춰 설명 드리도록 하겠습니다. 

단순히 "좋은 곡"을 추천하는 것을 넘어, 사용자와 플랫폼 간의 균형을 추구하며 플랫폼의 장기적 지속 가능성을 목표로 하는 **Mostra** <br>
이를 통해 스포티파이 추천시스템이 어떻게 진화하고 있는지 함께 알아보시죠! 😊

<br><br>

# 📻 Data Context
이번 연구는 Spotify의 radio-like music streaming 세션 데이터를 기반으로 진행되었습니다. <br>
연구에 사용된 데이터는 다음과 같습니다:
* 사용자 수: 1,000만 명
* 세션 수: 5억 회
* 상호작용 수: 10억 건
* 기간: 7일간의 청취 데이터

<br>

# 💡(Binary) Objectives
Spotify 플랫폼에서 다루는 주요 objective는 다음과 같은 네 가지로 나뉩니다.

| **objective**                         | **objective 설명**                                                                                                                                   | **측정 방법**                                                                                                                                             |
|----------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------|
| **단기적 사용자 만족도 <br> (Short-term User Satisfaction, SAT)** | 사용자와 트랙의 상호작용 완료 확률을 예측                                                                                                           | 사용자가 해당 트랙을 끝까지 들었는지, 혹은 건너뛰었는지(스킵) 여부를 이진값으로 기록 <br> 👉🏻 binary value: 1,0                                                              |
| **노출(Exposure)**              | 트랙이 신예 아티스트에 속하는지 여부                                                                                                               | 플랫폼이 특정 아티스트를 신예 아티스트로 분류할 경우, 해당 아티스트의 트랙은 1로 라벨링 부여 <br> 👉🏻 binary value: 1,0                                                               |
| **발견(Discovery)**             | 사용자가 이전에 들어본 적 없는 아티스트의 트랙인지 여부                                                                                               | 사용자가 해당 트랙, 혹은 트랙을 제작한 아티스트를 이전에 들은 적이 없다면 1로 라벨링 부여 <br> 👉🏻 binary value: 1,0                                                                |
| **부스팅(Boost)**               | 플랫폼이 전략적으로 강조하고자 하는 트랙인지 여부                                                                                                   | 플랫폼에서 전략적 중요성을 이유로 특정 트랙에 1로 라벨링 부여 <br> 👉🏻 binary value: 1,0                                                                                                |

### ✅ 사용자와 트랙의 상호작용(User–Track Pair)
음악 추천 시스템에서 가장 중요한 질문 중 하나는 **"사용자에게 어떤 곡을, 그리고 왜 추천해야 할까?"**입니다. 이를 해결하기 위해 스포티파이는 사용자의 개별적인 취향과 스트리밍 행동 데이터를 기반으로 특정 곡이 그들에게 적합한 이유를 분석합니다.

#### 📍 Discovery
예를 들어, **사용자 A**가 과거에 **Track X**와 **Track Y**를 스트리밍한 기록이 있다면, 이는 사용자 A가 선호하는 음악 스타일에 대한 힌트를 제공합니다. 이러한 **과거 스트리밍 데이터(historic interaction data)**를 활용해, 사용자 A가 **Track Z**와 같은 새로운 음악을 탐색할 가능성을 예측할 수 있습니다. 이를 **Discovery objective**로 정의하며, 이전에 들어본 적 없는 곡이나 아티스트를 추천함으로써 사용자가 새로운 음악 세계를 경험하도록 돕는 역할을 합니다. 

#### 📍 Boost
하지만 추천 시스템은 단순히 사용자 만족만을 고려하지 않습니다. **플랫폼의 전략적 목표** 또한 중요한 요소로 작용합니다. 예를 들어, 특정 아티스트가 새로운 곡을 발표했거나, 주목받아야 할 필요가 있는 **신예 아티스트**가 있다면 어떻게 해야 할까요? Spotify는 이러한 곡에 **Boost 태그**를 부여합니다. <br>
예를 들어, **아티스트 B(Artist B)**의 **Track M**이 플랫폼에서 전략적으로 중요한 곡으로 간주된다면, 이 곡은 Boost 태그가 붙어 추천 시스템에서 사용자에게 우선적으로 노출됩니다. 이러한 과정은 **editorial annotations**을 기반으로 이루어지며, 특정 곡이나 아티스트를 강조하기 위한 전략적 데이터를 활용합니다.


<br><br>

# Objectives within Sets
음악 추천 시스템에서 가장 까다로운 부분 중 하나는 **세션마다 달라지는 곡 구성(objective composition)**에 따라 최적화된 추천을 제공하는 것입니다. 스포티파이의 데이터분석 내용에 따르면, 사용자의 만족도(Short-term User Satisfaction, SAT)와 추천 목표들(Exposure, Discovery) 사이에는 특정한 관계가 존재합니다. 이를 이해하면 더욱 정교한 추천 시스템을 설계할 수 있습니다.

### 노출(Exposure): 사용자 만족도와 독립적 관계
스포티파이는 신예 아티스트의 곡을 추천해 노출을 늘리는 것을 중요 objective 중 하나로 설정하고 있습니다. 분석에 따르면, 노출(Exposure) 곡과 사용자 만족도(SAT) 사이에는 유의미한 상관관계가 없는 것으로 나타났습니다. 즉, 사용자가 신예 아티스트의 곡을 들어도 만족도가 특별히 올라가지도, 떨어지지도 않는다는 뜻입니다.

### 발견(Discovery): 사용자 만족도에 부정적 영향
Discovery, 즉 사용자가 이전에 들어본 적 없는 곡이나 아티스트를 추천하는 objective는 다소 도전적인 문제를 제기합니다. 분석에 따르면, Discovery 트랙의 비율이 높을수록 사용자 만족도가 감소하는 경향이 있습니다. 이는 사용자가 익숙하지 않은 음악에 대한 거부감이나 탐색에 대한 부담감 때문일 수 있습니다. <br>
따라서 Discovery를 포함한 추천은 무작위로 이루어져서는 안 되며, 사용자 경험에 미치는 영향을 신중히 고려해야 합니다.

### 세션의 곡 구성(objective composition)에 따라 추천 최적화
Spotify의 세션 데이터를 살펴보면, 세션마다 곡 구성의 특징이 다릅니다. 어떤 세션은 Discovery 트랙이 많고, 어떤 세션은 Exposure 트랙이 많습니다. 이렇듯 세션의 **곡 구성(objective composition)**이 달라지면, 추천 시스템도 그에 맞게 최적화된 전략이 필요합니다.
* 예시
  * Discovery 비중이 높은 세션 : 사용자가 부정적인 경험을 하지 않도록 발견 트랙의 적절한 비율을 유지하는 것이 중요
  * Exposure 비중이 높은 세션 : 노출을 늘리면서도 사용자의 만족도를 떨어뜨리지 않도록 하는 방법을 찾아야함

### 세션 맞춤 추천의 필요성
스포티파이는 이러한 문제를 해결하기 위해 Set-awareness를 추천 시스템에 통합하고자 합니다. 이는 세션 내 곡 구성(objective composition)을 분석해, 각각의 세션에 최적화된 곡 추천을 가능하게 합니다. <br>
다시 말해, 추천 시스템이 각 세션의 고유한 특성에 따라 트랙 순서를 동적으로 조율할 수 있도록 설계된 것입니다.

<br>

# Mostra Architecture
이걸 해결하기 위해 제시된게 바로 아래 내용입니다. <br>
** [기존의] set transformer encoder + [논문에서 개발한] Multi-Objective Beam Search decoder **

![end-to-end neural architecture for multi-objective track sequencing](https://github.com/user-attachments/assets/3b57f004-65f2-4f64-ab5d-8fd0305ac461)

## [Training] 곡과 사용자 간의 관계를 모델링하여 곡의 기본 점수를 계산
### 1️⃣ Representation Layer
사용자와 곡의 특징을 input으로 받아 이를 벡터로 표현
* 사용자(user embeddings) 특성, 곡 특성(learnt track embeddings), joint user–track features이 Representation Layer에 입력됨

### 2️⃣ Set Transformer-based Encoder
곡들 간의 상호작용을 고려하여 컨텍스트화된 표현 생성
* 곡 집합(Set)을 input으로 받아, 각 곡의 중요도를 평가하기 위한 Relevance Scores를 계산
  * _predicted user satisfaction for each user–track pair_
* Multi-head Self-Attention
  * 곡들 간의 상호작용 관계를 파악하여, 곡의 컨텍스트를 고려한 임베딩을 생성
* Feed-Forward Layer (Relevance Scores 계산)
  * Set Transformer로 생성된 곡 임베딩을 입력받아 각 곡의 Relevance Score를 계산
  * 컨텍스트화된 곡 representation → 스칼라 점수로 변환 (예: 곡 t1 → 0.96)

## [Inference] Encoder에서 계산된 Relevance Score를 활용해, Multi-Objectives를 균형있게 고려하며 곡 순서 생성
### 3️⃣ Multi-Objective Decorator
> multi-objective 점수(Discovery, Exposure, Boost)를 반영
* Multi-Objective 추가
  * 각 곡 t1, t2, t3, t4에 대해 세 가지 objective(Discovery, Exposure, Boost)를 부여 (예: t2 exposure)
  * 이 단계에서 곡에 해당하는 objective를 연결해, objective에 따른 추가 점수를 반영함

### 4️⃣ Multi-Objective Counterfactual Scoring (Submodular Scoring)
> 곡의 최종 점수를 조정
* Counterfactual Filtering (ε)
  * 점수가 를 만족하지 않으면 필터링
  * 즉, 특정 임계값(ε) 이하로 점수가 낮은 곡은 제외함
    * 기존의 좋은 추천을 해치지 않으면서, 새로운 목표를 동시에 달성
* Submodular MO Beam Scoring
  * relevance score과 objective score 통합
  * Relevance Score(rt)에 objective 기반 점수(g(MO))를 추가하여, 최종 점수를 계산함
→ 예: t2점수=기본점수(0.95)+objective점수(0.07)=1.02

### 5️⃣ Multi-Objective Beam Search
> 최종적으로 곡 순서를 결정
* Multi-Objective를 최대화하는 방향으로 순서를 정하고, 다양한 곡 간의 균형을 맞춤
  * 예: t1 → t2 → t4 순서로 추천


# Conclusion
결국, Spotify는 사용자의 취향 데이터를 기반으로 트랙을 분석하고, 과거의 스트리밍 기록과 같은 데이터(예: Discovery)를 활용해 새로운 곡을 추천합니다. 동시에, Boost 태그와 같은 플랫폼 중심의 전략도 반영해 모든 이해관계자(사용자, 아티스트, 플랫폼)의 목표를 조화롭게 달성하려 노력합니다.

Spotify의 추천 시스템은 단순히 "좋아할 곡"을 찾는 것을 넘어, 사용자가 새로운 음악을 발견하고 아티스트의 가치를 플랫폼 전체에서 극대화하도록 설계된 매우 정교한 시스템이라 할 수 있습니다.



<br>

### Reference
* [스포티파이 테크 블로그 글 - Mostra: Balancing multiple objectives for music recommendation](https://research.atspotify.com/2022/04/mostra-balancing-multiple-objectives-for-music-recommendation/)
* [논문 - Mostra: A Flexible Balancing Framework to Trade-off User, Artist and Platform Objectives for Music Sequencing](https://arxiv.org/pdf/2204.10463)
* [발표내용 - Mostra: A Flexible Balancing Framework to Trade-off User, Artist and Platform Objectives for Music Sequencing](https://e-bug.github.io/assets/pdf/mostra_slides.pdf)

----------------------------

### 🔜 Think
이번 포스팅에서는 GS-Score와 추천 알고리즘에 대해 다루면서 스포티파이가 어떻게 더 정교한 음악 경험을 제공하려고 하는지 살펴보았습니다.🎧📚 <br>
요즘 **현재 마주한 이 문제를 어떻게 진단하고, 방향을 어떻게 잡아야 하는가**에 대해서 많은 고민을 하고있거든요. 특히 유저의 성향을 어떤 지표로 담으면 좋을지 많이 고민하고 있다보니, 이런 부분이 매우 재미있게 다가왔습니다. 또 어떤 유저들의 음악 소비 패턴이 있을까 궁금해지네요! 여러분은 어떤 음악적 탐험을 하시는지도 궁금합니다!🎶 <br>
gs-score를 코드로 어떻게 구현하고, 실제 어떻게 값이 나오는지 확인하고 싶어서 다음 포스팅에서는 실제 구현을 진행해보려고 합니다. 많관부-💫