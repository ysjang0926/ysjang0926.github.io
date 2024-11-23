---
layout: post
title:  "추천시스템도 고민이 있다: 사용자 만족과 플랫폼 목표, 둘 다 잡기"
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

# 🎵 음악 추천시스템의 딜레마와 스포티파이의 새로운 접근법
음악 스트리밍 플랫폼에서 가장 중요한 목표 중 하나는 **"사용자가 좋아할 곡을 추천하는 것"**입니다. 하지만 여기에는 큰 딜레마가 존재합니다. 인기 있는 곡 위주로 추천하면 사용자는 만족할 수 있지만, 이는 신예 아티스트의 노출이 줄어들고 플랫폼 생태계의 다양성을 해칠 위험이 있습니다.  <br>
👉🏻 **문제점**: 사용자를 기쁘게 하려다 보면 신예 아티스트의 노출 기회가 줄어들고, 플랫폼의 장기적인 지속 가능성에도 부정적인 영향을 미칠 수 있음

### ✅ Mostra: 사용자와 플랫폼의 균형을 잡는 프레임워크
스포티파이는 이러한 문제를 해결하기 위해 **Mostra 프레임워크**를 제안했습니다. <br> Mostra는 단순히 사용자의 만족도를 최적화하는 것에 그치지 않고, 플랫폼 전체의 건강한 생태계를 유지하기 위해 다음의 주요 objective를 조화롭게 통합합니다:  
* **사용자 만족(Short-term User Satisfaction)**
* **새로운 음악 탐색(Discovery)**
* **신예 아티스트 노출(Exposure)**
* **특정 콘텐츠 부각(Boost)**  

Mostra는 **Set Transformer**를 기반으로 설계된 모델로, 다양한 objective를 균형 있게 고려하며 각 objective 간의 상충 관계를 효과적으로 조율합니다. 또한, **Beam Search**를 활용해 실시간으로 곡 순서를 최적화하며, 단기적인 사용자 경험과 장기적인 플랫폼 목표를 동시에 만족시킬 수 있는 구조를 제공합니다.  <br>

논문 핵심이 **"multi-objective 최적화"**이다보니, 이번 글에서는 각 objective에 대한 정의, Mostra 프레임워크의 작동 원리, 그리고 스포티파이가 추천시스템의 유연성과 실시간 조율 능력을 어떻게 강화했는지에 초점을 맞춰 설명 드리도록 하겠습니다. 

단순히 "좋은 곡"을 추천하는 것을 넘어, 사용자와 플랫폼 간의 균형을 추구하며 플랫폼의 장기적 지속 가능성을 목표로 하는 **Mostra**를 통해 스포티파이 추천시스템이 어떻게 진화하고 있는지 함께 알아보시죠! 😊

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
예를 들어, **사용자 A**가 과거에 **Track X**와 **Track Y**를 스트리밍한 기록이 있다면, 이는 사용자 A가 선호하는 음악 스타일에 대한 힌트를 제공합니다. 이러한 **과거 스트리밍 데이터(historic interaction data)**를 활용해, 사용자 A가 **Track Z**와 같은 새로운 음악을 탐색할 가능성을 예측할 수 있습니다. <br> 이를 **Discovery objective**로 정의하며, 이전에 들어본 적 없는 곡이나 아티스트를 추천함으로써 사용자가 새로운 음악 세계를 경험하도록 돕는 역할을 합니다. 

#### 📍 Boost
하지만 추천 시스템은 단순히 사용자 만족만을 고려하지 않습니다. **플랫폼의 전략적 목표** 또한 중요한 요소로 작용합니다. 예를 들어, 특정 아티스트가 새로운 곡을 발표했거나, 주목받아야 할 필요가 있는 **신예 아티스트**가 있다면 어떻게 해야 할까요? Spotify는 이러한 곡에 **Boost 태그**를 부여합니다. <br>
예를 들어, **아티스트 B(Artist B)**의 **Track M**이 플랫폼에서 전략적으로 중요한 곡으로 간주된다면, 이 곡은 Boost 태그가 붙어 추천 시스템에서 사용자에게 우선적으로 노출됩니다. 이러한 과정은 **editorial annotations**을 기반으로 이루어지며, 특정 곡이나 아티스트를 강조하기 위한 전략적 데이터를 활용합니다.


<br><br>

# ䷍ Objectives within Sets: 세션 기반 음악 추천 최적화
음악 추천 시스템에서 가장 도전적인 과제 중 하나는 **세션마다 달라지는 곡 구성(objective composition)**에 따라 최적화된 추천을 제공하는 것입니다. 스포티파이의 데이터 분석에 따르면, 사용자의 만족도(Short-term User Satisfaction, SAT)와 추천 목표들(Exposure, Discovery) 사이에는 특정한 관계가 존재하며, 이를 이해하면 더욱 정교한 추천 시스템을 설계할 수 있습니다.

![image](https://github.com/user-attachments/assets/c7b0bd0d-e8c7-4242-8819-2ac61da4f578)


#### 📍 노출(Exposure): 사용자 만족도와 독립적 관계
스포티파이는 신예 아티스트의 곡을 추천해 노출을 늘리는 것을 중요 objective 중 하나로 설정하고 있습니다. 분석에 따르면, Exposure 곡과 SAT 사이에는 유의미한 상관관계가 없는 것으로 나타났습니다. 즉, 사용자가 신예 아티스트의 곡을 듣는다고 해서 만족도가 특별히 올라가지도, 떨어지지도 않는다는 뜻입니다. 이는 신예 아티스트의 노출을 늘리더라도 사용자 경험에는 큰 영향을 미치지 않는다는 것을 의미하며, 이를 전략적으로 활용할 여지가 있습니다.

#### 📍 발견(Discovery): 사용자 만족도에 부정적 영향
Discovery는 사용자가 이전에 들어본 적 없는 곡이나 아티스트를 추천하는 objective입니다. 그러나 이 과정은 다소 까다로운 문제를 제기합니다. 분석에 따르면, Discovery 트랙 비율이 높아질수록 사용자 만족도가 감소하는 경향이 있습니다. <br>
이러한 결과는 사용자가 익숙하지 않은 음악에 대해 거부감을 가지거나, 새로운 음악 탐색에 부담을 느끼는 경우가 많기 때문일 수 있습니다. 따라서 Discovery 트랙을 포함한 추천은 무작위로 이루어져서는 안 되며, 사용자 경험에 미치는 영향을 세심하게 조율해야 합니다.

#### 📍 세션 기반 추천 최적화의 필요성
스포티파이의 세션 데이터를 살펴보면, **세션마다 곡 구성(objective composition)**이 다르다는 것을 알 수 있습니다. 어떤 세션은 Discovery 트랙이 많고, 어떤 세션은 Exposure 트랙이 많습니다. <br>
* 예시
  * Discovery 비중이 높은 세션 : 사용자가 부정적인 경험을 피할 수 있도록 discovery 트랙의 적절한 비율을 유지해야함
  * Exposure 비중이 높은 세션 : exposure를 늘리면서도 사용자의 만족도를 떨어뜨리지 않는 전략이 필요함
이렇듯 각 세션의 곡 구성에 따라 추천 시스템이 동적으로 적응할 수 있는 메커니즘이 필요함

#### 📍 Set-awareness 기반 세션 맞춤 추천
스포티파이는 이러한 문제를 해결하기 위해 Set-awareness를 추천 시스템에 통합하고자 합니다. Set-awareness는 세션 내 곡 구성을 분석해, 세션마다 최적화된 곡 추천을 가능하게 만듭니다. <br>
다시 말해, 추천시스템이 각 세션의 고유한 특성을 이해하고, 이에 따라 트랙 순서를 동적으로 조율할 수 있도록 설계된 것입니다. 이는 사용자 만족도를 유지하면서도 Discovery 및 Exposure와 같은 플랫폼의 목표를 달성할 수 있는 핵심적인 접근 방식입니다.

<br><br>

# 👾 Mostra Architecture: 새로운 Multi-Objective 프레임워크
스포티파이의 Mostra는 위의 문제들을 해결하기 위해 다음과 같은 기술을 통해 유연한 음악 추천을 가능하게 하는 **Transformer 기반 인코더-디코더 프레임워크**입니다. <br>
👉🏻 **[기존의] Set Transformer Encoder + [논문에서 새롭게 개발된] Multi-Objective Beam Search Decoder**
* **1. Transformer-based Encoder-Decoder**  
  * 곡 집합 내의 상호작용 관계를 학습하고, 곡 순위를 생성하는 데 필요한 컨텍스트를 반영
* **2. Novel Beam Search Algorithm**  
  * 기존 빔 서치 방식을 확장해, 여러 objective를 동시에 최적화하며 곡 순서를 동적으로 생성
* **3. Submodular Multi-Objective Scoring**  
  * 각 곡에 대해 Relevance Score와 objective-based 점수(Discovery, Exposure, Boost)를 통합하여 최종 점수를 계산
* **4. Counterfactual Performance on User Metrics**  
  * 모델이 추천하는 곡이 사용자 만족도 및 다른 지표에 미치는 영향을 Counterfactual(가정적 시뮬레이션) 방식으로 평가

![end-to-end neural architecture for multi-objective track sequencing](https://github.com/user-attachments/assets/3b57f004-65f2-4f64-ab5d-8fd0305ac461)

#### 📍 Desiderata (필수 요건)
1. **Set-aware method**  
  * 세션의 곡 구성(객체 간 관계)을 인지하며 최적화된 추천을 제공할 수 있는 방법론
2. **Multi-Objective decision making**  
  * 사용자 만족, 창작자 노출, 플랫폼 목표 등 여러 목표를 동시에 고려한 의사 결정 능력
3. **Dynamic & Flexible Control**  
  * 다양한 세션 구성에 따라 동적으로 조정 가능하며, 여러 목표 간의 균형을 유연하게 제어할 수 있는 기능

<br>

Mostra는 크게는 **Training**과 **Inference** 두 가지 단계로 나뉘며, 이를 세부적으로 살펴보면 총 5단계로 구성됩니다. 각 단계를 하나씩 설명드리겠습니다.

## 💡Training: 곡과 사용자 간의 관계를 모델링하여 곡의 기본 점수를 계산

![image](https://github.com/user-attachments/assets/87c7981b-2148-49aa-bb70-67a8a78a145b)

### 1️⃣ Representation Layer
**Representation Layer**는 **사용자와 곡의 특징을 입력으로 받아 이를 벡터로 표현**하는 역할을 합니다. 이 단계는 추천 시스템의 기본 데이터를 학습 가능한 형식으로 변환하는 과정입니다.

#### ✅ 입력 데이터
Representation Layer에는 다음과 같은 요소들이 입력됩니다:
* **사용자 특성(user embeddings)**: 사용자의 과거 스트리밍 행동과 선호도를 나타내는 벡터
* **곡 특성(learnt track embeddings)**: 각 곡의 고유한 특징을 학습한 임베딩 벡터
* **사용자-곡 조합 특징(joint user–track features)**: 특정 사용자와 곡 간의 상호작용을 나타내는 조합 데이터

#### ✅ 주요 역할
* 이 레이어는 입력된 데이터를 벡터 형태로 변환함으로써, 다음 단계(Encoder)에서 사용자와 곡 간의 관계를 효과적으로 분석하고 학습할 수 있도록 준비
* 결과적으로, Representation Layer는 사용자의 취향과 곡의 특성을 통합해 **기초 데이터**를 제공

<br>

### 2️⃣ Set Transformer-based Encoder
**Set Transformer-based Encoder**는 곡들 간의 상호작용을 고려하여, 각 곡의 컨텍스트를 반영한 표현을 생성하는 역할을 합니다. 이 과정은 곡 집합(Set) 내의 곡들 사이 관계를 학습하여, **Relevance Score(중요도 점수)**를 계산하는 데 중점을 둡니다.

#### ✅ 주요 기능
* **곡 집합(Set)을 input으로 받아, Relevance Scores 계산**
  * Encoder는 곡들 간의 관계를 분석하여, 각 곡의 중요도를 평가하기 위한 **Relevance Score(=SAT 점수)**를 계산합니다.
  * 이 점수는 **user–track pair 간의 예상 만족도(predicted user satisfaction)**를 기반으로 생성됩니다.
  * 각 곡에 대해 계산된 Predicted scores(예측 점수)는 추천시스템의 첫번째 단계에서 곡 순위를 결정하는데 사용됩니다.

#### ✅ 세부 구성 요소
1. **Multi-head Self-Attention**
  * 곡들 간의 상호작용 관계를 학습하여, 곡의 컨텍스트를 고려한 임베딩을 생성합니다.
  * 이를 통해 단순한 개별 곡의 특징이 아닌, **곡들이 서로 어떤 맥락에서 의미를 가지는지**를 반영합니다.
2. **Feed-Forward Layer (Relevance Scores 계산)**
  * Set Transformer에서 생성된 곡 임베딩을 입력받아, 각 곡의 **Relevance Score**를 계산합니다.
  * 컨텍스트화된 곡 representation을 **스칼라 점수로 변환**하여 곡의 중요도를 수치화합니다.
    * 예시: 곡 t1의 Relevance Score → 0.96

<br>

## 💡Inference: Encoder에서 계산된 Relevance Score를 활용해, Multi-Objectives를 균형있게 고려하며 곡 순서 생성

![image](https://github.com/user-attachments/assets/75e12737-33c2-4bac-bd43-70198073b6e0)

### 3️⃣ Multi-Objective Decorator
> multi-objective 점수(Discovery, Exposure, Boost)를 반영

이 단계에서는 각 곡에 대해 다양한 **objective**를 부여하고, 이를 바탕으로 곡의 최종 점수를 조정합니다. 스포티파이의 **Discovery, Exposure, Boost**와 같은 플랫폼 목표를 반영하여, 추천 곡이 단순히 사용자 만족뿐 아니라 플랫폼의 전략적 목표에도 부합하도록 조율합니다.

* Multi-Objective 추가
  * 각 곡 t1, t2, t3, t4에 대해 세 가지 objective(Discovery, Exposure, Boost)를 부여 (예: t2 - exposure)
  * 이 단계에서 곡에 해당하는 objective를 연결해, objective에 따른 추가 점수를 반영함

#### ✅ 작업 프로세스
1. **곡별 Objective 부여**
  * 각 곡에 대해 하나 이상의 objective를 부여합니다.
  * 예: 곡 t2는 Exposure 목표를 충족하도록 설정
2. **Objective 기반 추가 점수 반영**
  * 곡별로 부여된 objective에 따라, 해당 곡의 **Relevance Score**에 추가 점수를 반영합니다.
  * 예: t2의 점수 = 기본 Relevance Score(0.95) + Exposure Objective Score(0.07) = 1.02

<br>


Inline 수식: $s_{\text{max}} - s_t \leq \epsilon$

$$
s_{\text{max}} - s_t \leq \epsilon
$$



### 4️⃣ Multi-Objective Counterfactual Scoring (Submodular Scoring)
> 곡의 최종 점수를 조정

**Multi-Objective Counterfactual Scoring**은 곡의 최종 점수를 조정하는 과정으로, **Relevance Score**와 **Objective Score**를 통합하여 플랫폼의 다양한 목표를 충족시킵니다. 이 단계에서는 점수 필터링과 Multi-Objective 최적화를 통해 사용자 경험과 플랫폼 전략 간의 균형을 맞추는 데 중점을 둡니다.

#### ✅ 주요 기능
1. **Counterfactual Filtering(ε)**
  * 곡의 점수가 특정 임계값(ε)을 만족하지 못하면 필터링하여 제외합니다.
    * 예를 들어, 다음 곡을 선택할 때 SAT 점수가 일정 임계값(ε) 이하인 곡은 추천 후보에서 제외
  * 이 과정을 통해 기존의 좋은 추천 품질을 유지하면서도 새로운 objective를 달성할 수 있도록 조율합니다.
    * 훈련 메트릭(SAT)에 따른 잠재적 손실을 제한 → 추천 품질을 높이는 역할
2. **Submodular: MO(Multi-Objective) Beam Scoring**
  * 곡의 **Relevance Score(rt)**와 **Objective 기반 점수(g(MO))**를 통합하여 최종 점수를 계산합니다.
    * Submodular Scoring 방식으로 여러 objective를 균형 있게 고려하여 점수화
    * 기존에 선택된 곡들에서 덜 대표된 objectives(ex. Discovery, Boost 등)를 보완할 수 있는 곡을 더 높은 점수로 평가
  * 계산된 최종 점수는 각 곡이 추천 리스트에서 차지하는 순위를 결정합니다.
    * 예시: 곡 t2의 최종 점수 = 기본 점수(0.95) + Objective 점수(0.07) = 1.02
    * multi-objective 간의 조화를 유지하는 데 중점을 둠 → objectives가 고르게 반영된 추천결과 생성

<br>

### 5️⃣ Multi-Objective Beam Search
> 최종적으로 곡 순서를 결정
**Multi-Objective Beam Search**는 곡 순서를 최적화하는 과정으로, 다양한 Objective를 최대화하면서 곡 간의 균형을 유지합니다. 이 단계에서는 추천 리스트의 곡 순서를 결정하며, 사용자 만족과 플랫폼 목표를 동시에 충족시키는 데 중점을 둡니다.

#### ✅ 주요 기능
1. **Multi-Objective 기반 순서 최적화**
  * 여러 Objective(예: Discovery, Exposure, Boost)를 동시에 고려하여 곡 순서를 최적화합니다
  * 곡 리스트가 사용자 경험과 플랫폼의 전략적 목표를 균형 있게 반영하도록 설계
2. **다양성 유지 및 Objectives 조화**
  * 곡 추천에서 특정 목표(Objective)에 편중되지 않도록, 리스트 내 곡들 간의 균형을 맞춥니다.
  * 추천 리스트가 사용자와 플랫폼의 다각적인 요구를 만족시킬 수 있도록 정렬

#### ✅ 예시
* 곡 순서 예시: 곡 t1, t2, t4의 순서로 추천 (t1 → t2 → t4)
  * 이 순서는 Relevance Score와 Multi-Objective 점수를 통합하여 계산된 결과

<br><br>

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
