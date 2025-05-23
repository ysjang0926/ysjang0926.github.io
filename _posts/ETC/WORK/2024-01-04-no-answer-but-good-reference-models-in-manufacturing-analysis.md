---
layout: post
title:  "정답은 없지만 참고하면 좋은, 제조 분석의 모델링"
subtitle:   "Find missing time ranges in time series data"
categories: etc
tags: etc_work
comments: true
---

- 이번 글에서는 제조 분석에서의 분석 목적별 모델링 및 평가지표를 정리해 보았습니다.
- "이게 무조건 답이다!"라고 단정 짓는 것이 아닌, "일반적으로 이런 것들을 쓰더라~" 정도로 가볍게 읽어주시면 감사하겠습니다. 
- 데이터와 모델만 있으면 분석은 끝난 것이라고 절대 생각하지 않지만, 누군가에게 설명할 때는 이것만큼 편한 것이 없더라구요!😂
- 제가 잘못된 지식을 전파하여 그릇된 정보를 받아들이시게 될까봐, 여러 레퍼런스를 참고하여 작성해 보았습니다. 가볍게 읽어주시길 바랍니다!🙏

---------

혹시 'DX'라는 용어를 들어보신적 있나요?👀

오래전부터 기업의 변화와 혁신을 이끌어나가기 위하여 언급되고 있는 '**DX**'는 '**Digital Transformation**'의 약어인데요. 영어권에서는 Transformation을 X로 줄여부르기 때문에, 'DT'보다는 'DX'라는 약어를 더 많이 사용한다고 합니다! <br>
DX는 '디지털 전환'으로, 다양한 변화에 디지털 기반으로 기업의 전략, 조직, 프로세스, 시스템 등을 근본적으로 변화시키는 경영전략이라고 설명할 수 있습니다. <br>
그렇기 때문에 DX는 기업의 생존과 경쟁력을 위한 필수 과정이며, DX 역량 강화 및 변화 관리를 통해 Digital 기반의 성과 창출 뿐만 아니라 새로운 비즈니스 모델 창출과 성장을 이뤄낼 수 있을 것입니다.


DX를 위해서는 무엇보다 **구성원의 역량과 일하는 방식의 변화**가 필요합니다. <br>
이런 니즈로 인하여, 많은 회사들에서는 **DX를 위한 실무 역량 확보**를 위해 **현업 구성원들을 대상**으로 역량 육성 체계 및 서비스를 구축/운영하고 있습니다.

저희 회사도 이런 DX 활동을 활발히 하고 있는데요! <br>
현업 구성원들 대상으로 공정 특성과 분석 목적별로  어떤 분석을 하고 있는지 소개하는 시간이 필요하여, 해당 내용을 간략히 정리해보았습니다. <br>

-----------------

# 🔑 제조 분석 목적별 모델링 및 평가지표
* **목적**
	* 공정 특성별, 분석 단계별, 목적별 방법론 List 정의
	* 검증된 모델을 활용함으로써 과제 수행 기간 단축 및 효용 증가
* **카테고리**
	* 공정 특성 : 연속 공정 / 배치 공정
	* 분석 단계 : 데이터 전처리 / 분석 모델링
	* 분석 목적 : 주요인자 도출 / 최적 공정조건 도출 / 품질 예측 등

<br>

## 🚫 주의: 글을 읽기 전, 드리고 싶은 몇마디
이번 포스팅은 제조 현장에서 발생하는 문제에서 *(1)분석 목적을 정의**하여 **그에 따른 (2)모델링 및 평가지표**를 소개하는 내용입니다. <br>
무엇이든 정답이란 없기에, 글을 읽기 전 드리고 싶은 제 생각들을 적어보았습니다. <br>

### 💬 절대 답이 아니다!
이 글은 제조 분석 방법론에 관한 주제를 다루지만, 다소 주관적으로 작성하였기 때문에 **"이것이 무조건 답이다"는 아닌 점**을 꼭! 참고 해주시길 바랍니다. <br>
제조 분석에 대한 이해를 돕기 위한 것을 목적으로 작성하였으며, 분석 모델과 평가 지표는 일반적인 지침이기에 고유한 분석 환경과 요구사항을 고려하여 정보를 적용하시길 바랍니다. <br>
만약 틀린 내용이 기입되어 있거나, "어라? 이런 것도 있지 않았나?"와 같은 도움되는 말씀 있으시다면 언제든 얘기해주세요!😊 <br>
피드백 주신 내용 바탕으로, 더 많이 배우고 더 많이 노력하여 포스팅 수정하도록 하겠습니다. <br>

### 🔄 방법론보다 중요한 것은?
데이터 분석을 하는 이유는 **데이터로 무언가 가치를 만들어내는, 즉 데이터 기반 의사결정을 하기 위함**입니다. <br>
이때 좋은 결과를 도출하기 위해서 방법론을 많이 안다고 하여, 무조건적으로 분석을 잘하게 되는 것이 아닙니다.

저는 분석 교육을 진행할 때 매번 '**분석 방법을 활용하기 위한 사고방식이 필요하다**'를 강조하는데요! <br>
데이터 분석이 어려운 이유 중 하나는, 매뉴얼이나 교과서에 쓰여 있는 대로 흉내 낸다고 하여 답이 뚝딱 나오는 것이 아니기 때문입니다. <br>
답이 쉽게 나오지 않기에, 데이터 분석가에 있어서 문제를 정의하여 가설을 구축한 과제 해결 과정 속에서 (1)**결과물을 도출**하여 (2)**목적과 문제에 따른 해석을 추가**해 (3)**상대방을 설득**하는 기술이 중요시 됩니다. <br>
그렇기 때문에 이런 방법론은 수단이므로, 목적으로 대하면 안됨을 명심해야 합니다. <br>

### 📖 다양한 레퍼런스 참고
실무 바탕으로 작성된 부분도 일부 있으나,  대부분 다양한 테크 블로그, 컨퍼런스, 논문 등에서 참고하여 작성하였습니다. <br>
참고한 내용에 대한 레퍼런스 부분은 포스팅 맨 하단에 기입하였으며, 자세한 내용이 궁금하신 분들은 레퍼런스 URL을 통해 보시면 됩니다👍 <br>

<br>

## 0️⃣ 가설 수립
분석을 하기 전 우선적으로 진행되어야 할 것은 **문제 정의**입니다.<br>
문제 정의는 **현재 고민하고 있는 부분이 무엇이고 이를 지표화하여, 분석 과제 목표를 구체화한 뒤 분석 대상 Scope 및 X,Y인자를 정의하는 단계**라고 할 수 있습니다. <br>
이때 **다양한 가설 설정**을 한다면, 각 가설별로 분석을 진행하여 원하는 분석 결과를 도출하기 손쉬워집니다.  <br>
* 데이터를 통한 현상 재확인 및 분석 Target 설정
* 데이터 현상 분석, 배경 지식 및 공정 이론을 통한 원인에 대한 가설 및 상세 시나리오 수립
* 데이터 분석을 통한 각 가설별 검증 진행 <br>

특히 제조 데이터 분석의 주된 목적은 **공정 최적 제어를 통한 생산성 향상 및 비용 감소**이기 때문에,<br>
현재 현업에서 겪고 있는 문제가 무엇이고 이것을 해결하기 위해 어떤 과정이 필요한지를 잘 고민하여 가설 수립이 필요합니다.

<br>

## 1️⃣ 데이터 전처리
### ⬛ 1.1 변수 선택
현업 지식, 공정 운전 조건 및 통계적 기법을 활용하여 **모델링에 사용할 제약조건과 주요 변수를 선택**합니다. <br>
* 전체 데이터를 다 사용하는 것이 아니라, 실활용을 고려한 제약조건별 모델링 구현이 효과적일 때가 많음
* 원료 변경, 공정물질 조성 변경 등의 공정 조건 변화에 따른 모델 유지보수 및 고도화 필요 <br>

### ⬛ 1.2 이상치 처리
이상치는 **정상적인 값의 범위에서 크게 벗어난 값**입니다. <br>
도메인에 따라 이상치 기준이 다르기 때문에, 도메인 및 데이터에 따라 이상치 처리 기준을 정해야합니다.  또한 일부 도메인에서는 이상치를 더 중요시 여기는 케이스도 있습니다.
1. **1.5*IQR 기준:**
	* `Q1-1.5*IQR`와 `Q3+1.5*IQR` 기준으로 이상치 처리
2. **평균에서 표준편차만큼 떨어진 정도:**
	* `평균-3*표준편차`와 `평균+3*표준편차` 기준으로 이상치 처리 (6sigma)
3. **PCA:**
	* 데이터 분포를 최대한 보존하면서 고차원 공간의 데이터를 저차원 공간으로 변환하는 방법
	* 이상치는 주성분 기여도가 작아 PCA 변환 후 일반적인 데이터와 구분됨
4. **[DBSCAN 클러스터링](https://ablearn.kr/newsletter/?idx=13581593&bmode=view):**
	* 밀도 기반 클러스터링 알고리즘
		* 클러스터링 : 서로 유사한 속성을 갖는 데이터를 같은 군집으로 묶어주는 작업
		* 분할적 클러스터링 中 중심 기반 : 같은 클러스터링의 데이터는 **특정 중심**을 기준으로 분포 (ex. K-MEANS Clustering)
		* 분할적 클러스터링 中 밀도 기반 : 같은 클러스터링의 데이터는 **서로 근접**하게 분포 (ex. DBSCAN)
	* 특정 포인트가 클러스터의 많은 포인트에 가까이 위치하면 클러스터에 속한다고 판단함
5. **[LOF(Local Outlier Factor)](https://yjjo.tistory.com/45):**
	* 데이터 안에서 밀도 기반의 각 군집들을 생성하여 해당 군집에 얼마나 떨어져 있는지를 확인 (이상치 정도를 나타내는 척도)
	* Abnormal Score를 만들어 이상치를 탐지함 → 값이 크면 클수록, 이상치 정도가 큼 <br>

### ⬛ 1.3 파생변수 생성
파생변수는  **기존의 변수를 조합하여 새로운 변수를 만들어 내는 것**을 의미합니다. <br>
도메인 전문가의 아이디어가 중요하며, 주의 깊게 생각하는 부분의 가정(가설)을 설정하여 파생변수를 생성하는 과정을 거치게 됩니다. 
1. **직전 Lag값:**
	* 바로 전 시점(직전)의 값을 현재 시점으로 옮겨서 변수화
2. **이동 평균값:**
	* 특정 크기의 부분 집합 데이터을 연속적으로 이동하며 산출한 평균값으로 변수화
	* 연속된 데이터에서 발생하는 급격한 흔들림을 제거하거나 장기적인 방향을 보고 싶을 때 사용
3. **이동 분산값:**
	* 특정 크기의 부분 집합 데이터을 연속적으로 이동하며 산출한 분산값으로 변수화
	* 단기 변동을 제거하고 큰 편차를 강조하기 때문에, 연속된 데이터의 편차를 자세히 보고 싶을 때 사용
4. **공정 반응단계별 소요시간 세분화:**
	*  공정 반응단계별 소요시간의 변수화
	* 제품이 생산되기까지 n개의 단계로 나뉘어지게 되는데, 각 단계별 소요시간이 얼만큼 되는지 보고 싶을 때 사용
	* ex) 생산 라인의 가동시간, 정지시간, 교체시간 등의 변수를 생성
5. **클러스터링:** 
	* 주어진 데이터 집합을 유사한 데이터들의 그룹으로 나누어 변수화
6. **로그, 제곱근 변환:** 
	* 정규화 및 왜도 감소 목적으로 로그, 제곱근 변환하여 변수화
7. **변수 변화량 :** 
	* 일정 기간 각 변수의 차이를 변수화
	* 하나 또는 두 개 이상의 변수를 조합(ex. 곱셉, 나눗셈, 덧셈, 뺄샘 등의 연산)하여 새로운 변수를 생성
	* ex) 좌우 또는 상하 온도 센서 차이값, 팬 속도와 차압, 가스 센서 이동평균
8. **생산 단계 품질 검사 결과:** 
	* 각 생산 단계별 품질 검사 결과(ex. 정상/불량)를 변수화
	* ex) 제조과정에서 발생하는 불량의 원인에 대한 변수를 조합하여 생성 <br>

### ⬛ 1.4 외부 데이터 활용
내부 데이터라고 할 수 있는 설비 및 품질 데이터로 분석을 진행함과 더불어, 추가 데이터로 외부 데이터를 사용하여 분석에 활용하는 케이스도 있습니다.
1. **기상청 기상자료 데이터:**
	* 기상청에 제공하는 기상청 관측소별 날씨 데이터(온도, 습도, 강수량 등)
	* ex) [과거 기상 관측 데이터 가져오기](https://ysjang0926.github.io/etc/2023/10/11/kma-apihub-how-to-use-it/)
2. **경제 지표:**
	* GDP, 소비자물가지수, 생산자물가지수, 수출입물가지수, 환율, 원자재 가격 등
3. **공급망 데이터:**
	* 원자재의 유통, 납품, 재고 등과 관련된 데이터 <br>

<br>

## 2️⃣ 분석 모델링

### ⬛ 제조 데이터
아마 제조 분석을 하고 계신 분이 아니라면, 각 분석 케이스별 필요 데이터 리스트를 보고 이해가 안 되실 것 같더라구요😂 <br>
그리하여 간단하게 한줄 코멘트로만 데이터 설명을 적어보았습니다! 아래의 내용 읽으시기 전 참고로만 슉슉- 읽어주세요.
1. **생산실적 데이터** : 생산 품종 및 생산 시간 데이터
2. **TAG 리스트** : 공정 설비 tag 리스트
3. **품질 데이터** : 품질, 물성 데이터
4. **작업 History 데이터** : TA(Turn Around) 및 정비 기간 데이터
5. **원료 데이터** : 제품 생산을 위해 투입한 원료 데이터
6. **리드타임 데이터** : 공정 리드타임(체류시간) 데이터 <br>

<br>

### ⬛ 2.1 주요 인자 도출(원인 분석)
> 데이터 분석 기반 제조 품질 경쟁력 강화

* **분석 설명**
	* 생산 제품의 이상 원인을 원료, 공정, 품질 관점에서 분석하여 **설비 주요 인자를 도출**
* **분석 목적**
	* 공정마다 발생하는 불량 문제의 경우 다양한 변수에 의해 발생하게 되며, **품질 상황 변화에 영향을 미치는 공정 변화를 파악할 수 있는 제조 전문 분석이 필요**
* **분석 목표**
	* 높은 수율과 품질을 유지하고 불량률을 줄이기 위한, **생산 제품의 품질에 영향을 주는 중요공정 및 설비 파악 및 관리**

| ![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/58d38150-aac8-4233-9700-69316a751452) | 
|:--:| 
| @i-매거진, 설비 상태/품질 결함 예측 분석 적용 예 |

* 품질 원인 분석 (ex. x 제품 y 물성 품질개선 , 동일 공정간 품질 편차 분석 등)으로 볼 수 있음
	* 품질 원인 분석 생산 제품의 이상 원인을 원료 , 공정 , 품질 관점에서 분석을 하는 것
* 특정 공정에서 가동되고 있는 센서(sensor) 값을 이용하여 물성 또는 불량에 영향을 주는 인자를 파악
* 해당 인자를 이용하여 지속적인 물성 유지와 양품 제품 생산을 위한 관리 방안을 현업에게 제시 하는 작업까지 진행됨
* 품질 원인 분석을 진행할 경우 제일 주의 해야할 점은 현업들의 경험적으로 알고 있는 부분들과 분석을 통해 나온 결과들이 상충될 수 있다는 것임
	* 이를 해소하기 위해서는 분석가는 해당 공정의 도메인에 대한 이해도를 높여 분석을 진행하여 유의미한 결과를 산출할 수 있어야 함
	* 현업 파트는 분석가에게 공정 이슈 및 분석 결과에 대해 즉각적인 피드백을 줄 수 있어야 함 (커뮤니케이션 역량 必) <br>

#### ✅ 데이터 / 모델링 / 평가지표
1. **데이터**
	* 생산 실적 데이터
	* 설비 데이터(센서)
	* 작업 History 데이터
	* 공정조건 데이터
	* 물성, 품질 데이터
	* 외부 데이터
2. **모델링**
	* 상관분석
	* 주로 결정 트리 기반의 앙상블 모델을 사용 (interpretability가 중요하기 때문)
		* Decision Tree, Random Forest, XGBoost, LightGBM
	* 핵심인자 간 최적 조합을 구하기 위해서는, PCA를 사용
		* 주성분에 미리 정해진 가중치를 부여하고, 그 결과 값이 높은 순서대로 핵심인자 추출
3. **평가지표**
	* 기초통계량 → 데이터 분포 비교
	* 상관계수
	* 변수 중요도 <br>

<br>

### ⬛ 2.2 최적 조건 도출
> 공정 최적화를 통한 제품 생산성, 안정화 기여

* **분석 설명**
	* 과거의 best practice 기반으로 **앞으로의 양품 생산을 위한 생산 품종, 라인별 최적 공정 조건 도출 및 적용**
	* best practice : 현업이 생각할 때 최고로 생각되는 물성 조건에 맞는 양품 생산 시기
* **분석 목적**
	* 연속 공정에서 선행 공정의 물성이나 조작에 따라 후행 운전 조건이 다양하게 변경되며, 제어 인자와 조작 수치가 미표준화 되어 작업자별 숙련도에 따라 발생하는 **품질 편차를 줄이는 것이 필요**
* **분석 목표**
	* 과거 Best Practice 기반 양질의 제품 생산을 위한 생산 품종, Line별 Golden Recipe 도출 및 적용

| ![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/1ad0de15-89ab-4aae-a9cb-77f2ed2417da) | 
|:--:| 
| [control chart](https://www.qimacros.com/lean-six-sigma-articles/stability-analysis-vs-capability-analysis) |
 
* [데이터 수집 → 표준 최적 공정 관리조건 도출 → 현장 적용 → 모니터링] 단계로 진행
	* 최적 조건을 찾아서 이를 실제 현장의 공정 운영 조건에 설정하여 운영 최적화까지 적용하는 것을 목표함
* 대부분의 공장에서는 제품을 생산하면서 공정조건 변경은 현업의 경험과 감에 의존하고 있는 경우가 큼
	* 해당 분석을 통해 데이터 기반의 생산 공정의 기술축적 및 기술 표준화로 생산성 및 품질 향상을 위한 초석을 마련할 수 있음
	* 그래서 실제로 공장별로 분석 과제를 진행할 때 최적 공정 조건 도출 분석이 기본적으로 진행되기도 함 <br>

#### ✅ 데이터 / 모델링 / 평가지표
1. **데이터**
	* 생산 실적 데이터
	* 설비 데이터(센서)
	* 작업 History 데이터
	* 물성, 품질 데이터
2. **모델링**
	* 통계적 방법론 (6sigma)
	* Decision Tree의 Rule Set 사용
3. **평가지표**
	* 기초통계량 → 데이터 분포 비교
	* 상관계수
	* 변수 중요도 <br>

<br>

### ⬛ 2.3 품질, 물성 예측
> 비용 감소 및 생산시간 단축을 위한 제품 물성 예측

* **분석 설명**
	* 품질 불량에 영향을 주는 주요 인자를 분석하여, **현장 적용 가능한 예측 모델링** 개발
* **분석 목적**
	* 제품에 있어서 핵심적인 경쟁력인 품질에 대한 측정은 공정 완료 후 진행하는 경우가 많아 실시간으로 평가하기 어렵기 때문에, 현 운용 중인 설비에서 **다양한 공정 조건을 통해 제품 품질이나 설비 고장을 예측할 수 있는 환경이 필요**
* **분석 목표**
	* 축적된 공정 데이터를 활용하여 제품 품질을 사전에 예측하여, **공정 이상을 즉각 대응**하며 **설비 유지보수, 재생산 등의 제반 비용을 절감**

| ![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/5a512c48-2b0f-4c70-a6e2-ef31d2cff743)
 | 
|:--:| 
| @SK TECH SUMMIT 2023, 과제 수행 사례 "임가공 동절기 사출 Color out 원인 분석" |

* 품질 측정은 공정 완료 후 진행하는 경우가 많아서 , 공정 중 제품의 품질 수준을 실시간으로 평가하기 어려움
	* 공정 중 품질 측정의 경우 비용 문제로 샘플링 측정을 하고 있으며 , 제품의 최종 품질과의 연관성이 높지 않을 때도 있음
* 고객의 요구 특성 (Y 값)에 대한 예측 성능이 곧 시장에서의 고객 만족 및 품질 비용으로 직결될 수 있는 사안임
* 최종 품질과 직결되는 중간 물성을 예측하여 중간 공정에서 작업자의 판단을 도와주는 역할을 함 (공정별로 차이 존재)
* 높은 수준의 품질 예측으로 제조 품질 경쟁력을 강화할 수 있음
	* 공정 중 제품의 품질을 실시간으로 사전에 예측 및 조 치 하여 , 제조 공정의 선제적 품질관리 및 불량 조기 탐지 가능
	* 실시간 데이터 기반 품질 예측 및 피드백을 통한 공정 및 품질 상황 변화 실시간 모니터링 가능 <br>

#### ✅ 데이터 / 모델링 / 평가지표
1. **데이터**
	* 생산 실적 데이터
	* 설비 데이터(센서)
	* 작업 History 데이터
	* 공정조건 데이터
	* 물성, 품질 데이터
2. **모델링**
	* Multiple Linear Regression
	* 주로 결정 트리 기반의 앙상블 모델을 사용 (interpretability가 중요하기 때문)
		* Decision Tree, Random Forest, XGBoost, LightGBM
	* Bayesian Optimazation
	* [1D-CNN](https://goatlab.tistory.com/entry/Deep-Learning-%ED%8A%B8%EB%9E%9C%EC%8A%A4%ED%8F%AC%EB%A8%B8-Transformer)
	* DNN(Deep Neural Network)
	* MLP(Multiple Layer Perceptron)
	* LSTM(Long Short Term Memory)
	* SVM(Support Vector Machine)
3. **평가지표**
	* [MAE, NMAE, RMSE](https://dacon.io/forum/405791?dtype=recent)
	* R-Squared
	* Maximum error
	* Accuracy (오차율 계산)
	* Detection Ratio <br>

<br>

### ⬛ 2.4 설비 장애 예지(이상감지)
> 비용 감소 및 생산시간 단축을 위한 제품 물성 예측

* **분석 설명**
	* 생산 설비나 공정의 안정성을 수치화하여 **이상을 미리 예측**하여, 사전에 파악할 수 있도록 모니터링 진행
* **분석 목적**
	* **공정 및 설비 이상 위치를 즉시 파악**하여, 이상 공정을 인지하고 빠르게 조치할 수 있는 환경이 필요
* **분석 목표**
	* 공정 설비 데이터를 분석하여, **설비 고장 원인 분석**과 **설비 고장 조기 예측**


| ![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/0adff696-65c6-4542-9274-5a33e0507848) | 
|:--:| 
| @Yeh , Chin Chia Michael, et al. "Online Amnestic Dynamic Time Warping to Allow Real Time Golden Batch Monitoring." |

* 설비 장애 예지 분석은 생산 설비나 공정의 안정성을 수치화하여 고장 원인 분석과 이상을 조기 예측
	* ex) 실시간 y 품질 예측 / z 설비 이상 모니터링을 위한 분석 모델	
* 부품 고장 예측 및 시스템 다운타임 최소화를 위해 수십억 개의 데이터 행 처리 및 숨겨진 패턴 발견
* 효율적인 설비 유지보수를 위해 , 예측이 필요한 센서 뿐만 아니라 주변에 설치된 센서를 실시간으로 분석하여 설비 이상 발생을 사전에 파악할 수 있는 스마트한 모니터링 구축을 목표로 함
	* 공장의 기존 장비로는 설비 이상을 정확하게 진단하고 , 이상 원인을 파악하기에 한계가 있음
	* 이때 모니터링 구축 시 예측 결과를 시스템에 적용하여 현재 설비가 정상 범위를 이탈하는 경우 담당자에게 알람을 발송하는 등 현업이 필요로 하는 요소가 무엇인지 파악하는 것이 중요 <br>

#### ✅ 데이터 / 모델링 / 평가지표
1. **데이터**
	* 설비 데이터(센서)
	* 작업 History 데이터 (ex. 장비 성능 다운타임)
2. **모델링**
	* Statistical Pattern Recognition
	* [Isolation Forest](https://pizzathiefz.github.io/posts/isolation-forest-with-shap/)
	* Autoencoder + LSTM
	* Time Series Clustering : Dynamic Time Wrapping(DTW)
		* 일반적인 유클리드 거리 측정법은 시계열에 적합하지 않음 (특히, Batch 공정)
			* 만약 두 시계열이 높은 상관 관계가 있지만, 하나가 한 시간 단계만큼 이동하는 경우 유클리드 거리는 더 멀리 떨어져 있는 것으로 잘못 측정하기 때문
			* 그렇기 때문에 time sequence를 고려한 모델링을 하는 것이 필요
3. **평가지표**
	* Binary evaluation metrics
		* F-score
		* [MCC(Matthews Correlation Coefficient)](https://choice-life.tistory.com/82) 
	* Non-binary evaluation metrics
		* AUC-ROC <br>

<br>

## 3️⃣ 분석 수행 사례
실사례 부분은 시간될 때마다 조금씩 업데이트하겠습니다🐱‍👤

### ⬛ 주요 인자 도출(원인 분석)
* Feed stock, rBHET 품질과 CR-PET 물성의 상관 관계 도출
* ECOZEN 고내열 Grade Color 개선

### ⬛ 최적 조건 도출
* LNG/LPG 연료 사용량 변화인자 발굴을 통한 운전 최적화
* TEG#3 Feed 조성에 따른 최적 운전조건 도출

### ⬛ 품질, 물성 예측
* 임가공 동절기 사출 Color out 원인 분석 및 Color 예측 모델링
	* 목표 : 임가공 사출 분석 회수 감소 및 추가 Action을 통한 원하는 품질로 조정
* CP-4 PETG 공정 조건에 따른 품질 예측 모델링
	* 목표 : 주간 공정 품질을 예측하여, 현재 생산량과 Grade 기준 품질 오차 범위 내로 운전가이드 제시하는 모델 생성 <br>
* [머신 러닝과 데이터 전처리를 활용한 증류탑 온도 예측](https://scienceon.kisti.re.kr/srch/selectPORSrchArticle.do?cn=JAKO202113759911250&dbt=NART)
	* 목표 : 혼합부탄 증류탑의 데이터 기반 온도 예측 모델 개발 및 최적화
* [친환경 섬유 소재 개발을 위한 최적 물성 예측 알고리즘에 관한 연구](https://jkiie.org/xml/34976/34976.pdf)

### ⬛ 설비 장애 예지(이상감지)
* [LBAD 이상감지 AI 알고리즘 개발 및 양산적용](http://dsba.korea.ac.kr/lbad-%ec%9d%b4%ec%83%81%ea%b0%90%ec%a7%80-ai-%ec%95%8c%ea%b3%a0%eb%a6%ac%ec%a6%98-%ea%b0%9c%eb%b0%9c-%eb%b0%8f-%ec%96%91%ec%82%b0%ec%a0%81%ec%9a%a9/)
	* 목표 : 용접 비파괴 검사 시스템에 대한 비지도 학습 기반 시계열 이상감지 알고리즘 개발

<br>

### Reference
* [디지털 트랜스포메이션(DX)에 대한 모든 것](https://gscaltexmediahub.com/future/digital-transformation/concepts-of-dx/)
* [데이터 분석을 잘하는 사람과 못하는 사람의 결정적 차이](https://brunch.co.kr/@ashashash/133)
* [[데이터전처리] Outlier(이상치/이상값/특이값/특이치 등) 탐지 방법(detection method)](https://claryk.tistory.com/category/AI%20%26%20%EB%B9%85%EB%8D%B0%EC%9D%B4%ED%84%B0/%EB%8D%B0%EC%9D%B4%ED%84%B0%20%EC%A3%BC%EB%AC%BC%EB%9F%AD%28%20%2B%20feature%20engineering%29)
* [고려대학교 강필성 교수님 수업자료 - Anomaly Detection](https://github.com/pilsung-kang/Machine-Learning-Basics-Bflysoft/blob/master/Lecture%2010_Anomaly%20Detection.pdf)
* [Isolation Forest 로 이상치 찾기 (+ SHAP로 설명하기)](https://pizzathiefz.github.io/posts/isolation-forest-with-shap/)
* [[Paper Review] Navigating the Metric Maze: A Taxonomy of Evaluation Metrics for Anomaly Detection in Time Series](http://dsba.korea.ac.kr/seminar/?mod=document&uid=2743)
* [[Must Have] 데싸노트의 실전에서 통하는 머신러닝](https://goldenrabbit.co.kr/product/must-have-%eb%8d%b0%ec%8b%b8%eb%85%b8%ed%8a%b8%ec%9d%98-%ec%8b%a4%ec%a0%84%ec%97%90%ec%84%9c-%ed%86%b5%ed%95%98%eb%8a%94-%eb%a8%b8%ec%8b%a0%eb%9f%ac%eb%8b%9d)
* [[BASIC] 대회에서 자주 사용되는 평가산식들에 대한 정리✏️ (1) 회귀모델 평가산식](https://dacon.io/forum/405791?dtype=recent)
* [SK TECH SUMMIT 2023](https://sktechsummit.com/main/main.do)
* [DSBA 연구실 PROJECTS](http://dsba.korea.ac.kr/projects/)
