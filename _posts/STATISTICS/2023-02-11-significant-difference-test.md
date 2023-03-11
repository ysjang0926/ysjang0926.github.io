---
layout: post
title: "양품 불량품 간의 주요 요인인자 산출 및 통계적 차이 검증"
subtitle: "유의차분석: 주요 요인인자 리스트 산출 및 집계"
categories: data
tags: stat
comments: true
use_math: true
---

> 양품 불량품 간의 차이가 존재하는 주요 요인인자 산출과 그에 대한 통계적 검증 과정을 정리한 글입니다.


(https://ysjang0926.github.io/etc/2020/12/13/manufacturing_data_intro/)

회사에서 작년부터 약 3달 간 솔루션 개발을 진행하여 올해 1월 말에 구축 완료하였습니다.🥳<br>
(솔루션 개발 및 구축을 한 것은 이번이 처음이라 고생하였는데 넘 뿌듯하네요!)
<br>

현재는 화면 수정 및 고도화 단계를 계속 진행하고 있으며, <br>
이번 글에는 솔루션에 대한 간단한 소개와 최근에 진행하였던 통계적 유의성을 반영하기 위해 평균차이 검정 결과값을 적용한 과정을 소개하려고 합니다.

------

통계학에서 두 그룹군의 차이를 비교하고자 할 때, 통계적 유의차가 있는지 확인하는 과정을 거치게 됩니다. 이때 이런 과정을 통틀어 **유의차 분석**이라고 부르고 있습니다.

## 유의차(significant differece) 분석
**유의차 분석**은 데이터의 평균 또는 비율 등의 차이가 통계적으로 의미가 있는지, 통계 모형을 사용하여 검정(test)하는 분석 기법입니다. <br>
아래의 그림을 예시로 들면, 수집한 데이터에 대한 group별, condition별 평균값의 차이는 실제로 차이가 있다고 할 수 있는지 판단할 수 있도록 도와주는 역할을 합니다.

![image](https://user-images.githubusercontent.com/54492747/217399254-28be5e51-475a-453b-8b5e-4b549202d1d6.png)
[출처 : Assessing differences of significance](https://janhove.github.io/analysis/2014/10/28/assessing-differences-of-significance)

<br>

그렇다면 실제 제조 분석에서는 유의차분석을 언제 사용할까요?
- 생산 설비에 설치된 **수많은 센서(sensor) 데이터** 중
- **제품 물성이 좋은 Group과 그렇지 않은 Group 사이에 평균이 차이가 있는 센서**는 어떤 센서이며,
- 만약 차이가 있다면 **제품 물성에 영향력이 있는지**를 확인할 때 필요합니다.

(여기서 sensor는 어떤 물질의 양이나 온도를 감지하여 측정하는 장치를 말하며, 여기서 측정된 값을 센서값 또는 태그값이라 부릅니다.)

<br>

현장에서 비교(ex.양품/불량품)하고자 하는 생산품을 기준으로 유의차분석 수행을 통해 원인 인자 파악이 가능하다면 좋지 않을까요? 관련하여 일반적으로 쓰이는 두 방법에 대하여 소개하도록 하겠습니다.

우선 제조업에서 유의차 분석을 사용하는 이유에 대해 간단하게 설명하자면 다음과 같습니다.
* 시스템에 저장된 데이터(생산, 품질 등)를 분석하여 제품 품질에 영향을 미치는 주요 원인을 파악하기 위한 솔루션
* 목적 : 비교(ex.양품/불량품)하고자 하는 그룹 간 유의인자 탐색 및 통계적 유의성 검정

<br>

솔루션을 사용하기 위해서는 **분석을 실행하는 부분**과 **분석 결과를 집계 및 시각화**하는 부분으로 나뉩니다. 이번 글에서는 **분석 결과를 집계**하는 부분을 다루도록 하겠습니다.

분석 결과 집계는 아래와 같은 과정으로 이루어져 있습니다. <br>
우선 분석하고자 하는 요인들(센서, 품질 등)을 머신러닝 모델을 통해 중요도를 산출하여, 주요 요인 리스트를 생성합니다. 주요 요인 리스트 기준으로 통계적 유의성 검정을 진행하여 검정 결과를 산출합니다. 마지막으로 중요도 및 검정 결과를 집계하여 하나의 테이블로 보여줍니다.
1. 주요 요인 리스트 및 중요도 산출
2. 대조군/실험군 평균 비교 검정 결과 추출

<br>

### 1. 주요 요인 리스트 및 중요도 산출
비교하고자 하는 그룹 간 유의 인자에 대한 중요도는 **LightGBM**에 의해 결정됩니다. <br>

LightGBM은 GBM 기반의 알고리즘이고, XGBoost(eXtra Gradient Boost)보다 2년 후에 만들어졌으며 XGBoost의 장점은 계승하고 단점은 보완하는 방식으로 개발되었습니다. 가장 큰 장점은 XGBoost와 GBM에 비해 **훨씬 학습에 걸리는 시간이 적다는 것**이며, 성능상으로 보았을 때에도 XGBoost와 큰 차이가 없습니다. <br>

센서 데이터의 경우 1초, 1분 등 시계열 데이터로 구성되어 있기 때문에, 비교하고자 하는 그룹 수와 센서 수가 많아질수록 데이터의 양은 방대해집니다. 이러한 이유로 유의차분석 솔루션에서는 모델 정확성보다는 중요도 결과를 빠르게 내는 것이 더 중요하기 때문에, 다른 모델 대비 빠른 시간 내에 학습하여 결과를 산출하는 LightGBM을 선정하게 되었습니다. 

<br>

LightGBM을 python의 `lightgbm` library를 활용하여 구현했고 아래와 같습니다. <br>
이때 lightGBM은 중요도를 Gain, Split 이렇게 2가지를 제공하며, `Gain`은 모델 정확도에 더 좋으나 솔루션에서는 **데이터를 나누는 기준점을 찾는 것이 더 중요**하기 때문에 `Split`으로 사용하였습니다.

```python
import lightgbm as lgb

# LightGBM 모델에 맞게 Data Set 변환
lgb_dtrain = lgb.Dataset(data = data_x, label = data_y)

# LGBM : Binary Split & Boosting을 얼마나 돌릴지는 1000으로 지정
model = lgb.train(
params = {"objective": "binary"},
train_set = lgb_dtrain,
num_boost_round = 1000
)

# Importance 계산 : type은 Split 사용
importance_df = (
pd.DataFrame({
# 'feature_name': model.feature_name(),
'TAGID' : x.columns,
'Variable_Importance': model.feature_importance(importance_type = 'split'), # Gain : model.feature_importance(importance_type = 'gain')
})
.sort_values('Variable_Importance', ascending = False)
.reset_index(drop = True)
)
```

<br>

### 2. 대조군/실험군 평균 비교 검정 결과 추출
lightGBM 중요도를 통해 생성된 주요 요인 리스트가 통계적으로 유의미하게 차이가 있는지는, 서로 다른 2개 집단 평균 비교 검정을 통하여 확인하였습니다. <br>
두 집단의 평균을 비교할 때는 t-검정이 사용되며, t-검정은 다음과 같이 분류됩니다. <br>
![t검정종류](https://user-images.githubusercontent.com/54492747/217399696-30e4362e-ac07-445f-bb39-cc4c62f85fc6.png)

<br>

제조 데이터의 경우, 하나의 BATCH 또는 LOT에 대하여
- **1번씩만 기록되는 품질 결과**와
- **1초, 1분으로 값이 기록되기 때문에 수없이 많은 시계열 데이터인 센서값**으로 나뉘어지기 있기 때문에
- 이에 대한 기준을 설정하는 것이 중요하였습니다. <br>

데이터에 따라 어떤 검정 방식을 사용해야 하는지는 아래와 같은 방식에 의해 결정되도록 하였습니다.
![t검정솔루션케이스](https://user-images.githubusercontent.com/54492747/217399633-a2d56aab-9a9f-40ca-9606-14f5533b49de.png)

<br>

#### 1. 데이터의 종류
* 우선 데이터의 종류를 센서(=TAG) 리스트와 품질 리스트로 구분하였습니다.
* 센서값의 경우 최소 1,000개 이상이 존재하기 때문에 충분한 모수를 지니고 있기에, 정규성을 따른다고 가정하였습니다.
* 품질값이 경우 BATCH 또는 LOT 개수가 많지 않으면 충분한 모수를 지니지 못할 가능성이 있기 때문에, 정규성 검정을 진행하였습니다.
	* 이때 정규성 검정으로는 Sharpiro-Wilk test를 사용하였습니다.
	* 정규성 검정 결과 p-value가 0.05 이상이면 정규성을 따른다고 가정하였습니다.

#### 2. 정규성 가정
**1) 정규성을 가정할 수 있는 경우** <br>
* 센서 데이터
* 품질 데이터일 때 정규성 검정을 통과한 경우 등분산성 검정을 진행해야 합니다.
	* 이때 등분산 검정으로는 Levene's test를 사용하였습니다.
	* 등분산 검정 결과 p-value가 0.05 이상인 경우 등분산성을 따른다고 가정하였습니다.

**2) 정규성을 가정할 수 없는 경우** <br>
* 품질 데이터일 때 정규성 검정을 통과하지 않은 경우 비모수 검정을 진행해야 합니다.
	* 이때 비모수 검정인 Mann-whitney U test를 사용하였습니다.

#### 3. 등분산성 가정
**1) 등분산성을 가정할 수 있는 경우** <br>
* 정규성과 등분산성 모두를 만족하였기 때문에, Independent two sample t-test를 사용하였습니다.

**2) 등분산성을 가정할 수 없는 경우** <br>
* 정규성은 만족하였으나 등분산성 만족하지 못하였기 때문에, Welch's t-test를 사용하였습니다.

이렇게 데이터 타입 / 정규성 / 등분산성에 따라 해당하는 검정 방법을 구분하였으며, 각 컬럼당 구분된 검정을 진행하여 통계적 유의성 검정 결과를 산출하였습니다. <br>
* 이때 p-value를 계산하여, p-value가 0.05 이하이면 통계적으로 유의미하다고 판단하였습니다. 

<br>

위의 내용을 python의 `scipy` library의 `stats` 함수를 이용하여 구현했고 아래와 같습니다. <br>

#### 컬럼별 검정결과(p-value) 산출
```python
import scipy.stats

col_list = list(final_df.columns[1:])
shapiro0_p_list = []; shapiro1_p_list = []
levene_p_list = []; ttest_p_list = []; welcht_p_list = []; mannwhitney_p_list = []
test_result_list = []

for i in col_list:
    YN_0 = final_df[final_df['YN']=='0'][i].dropna()
    YN_1 = final_df[final_df['YN']=='1'][i].dropna()
    # Shaprio-Willks Test
    stat_0, p_0 = scipy.stats.shapiro(YN_0)
    stat_1, p_1 = scipy.stats.shapiro(YN_1)
    # Levene's Test
    levene_t, levene_p = scipy.stats.levene(YN_0, YN_1)

    # Independent samples t Test (equal_var=True)
    ttest_t, ttest_p = scipy.stats.ttest_ind(YN_0, YN_1, equal_var=True) # nan_policy='omit'
    # Welch's t Test (equal_var=False)
    welch_t, welch_p = scipy.stats.ttest_ind(YN_0, YN_1, equal_var=False)
    # Mann-Whitney U Test
    mannwhiteney_u, mannwhiteney_p = scipy.stats.mannwhitneyu(YN_0, YN_1)

    shapiro0_p_list.append(p_0)
    shapiro1_p_list.append(p_1)
    levene_p_list.append(levene_p)
    ttest_p_list.append(ttest_p)
    welcht_p_list.append(welch_p)
    mannwhitney_p_list.append(mannwhiteney_p)

frames = {'TAGID':col_list,
          'shapiro_test_0':shapiro0_p_list, 'shapiro_test_1':shapiro1_p_list, 'levene_test':levene_p_list,
          't_test':ttest_p_list, 'welch_t_test':welcht_p_list, 'mannwhitney_u_test':mannwhitney_p_list}
df_ttest = pd.DataFrame(frames)
```

#### 데이터 타입 / 정규성 / 등분산성에 따른 최종 p-value 산출
```python
search = 'QA_NUM' # 품질명에 해당하는 것

def pvalue_result(df_name):
    if search in df_name['TAGID']: # 품질
        if (df_name['shapiro_test_0'] > 0.05) & (df_name['shapiro_test_1'] > 0.05) & (df_name['levene_test'] > 0.05):
            return df_name['t_test']
        elif (df_name['shapiro_test_0'] > 0.05) & (df_name['shapiro_test_1'] > 0.05) & (df_name['levene_test'] < 0.05):
            return df_name['welch_t_test']
        else:
            return df_name['mannwhitney_u_test']
    else: # 태그
        if (df_name['levene_test'] > 0.05):
            return df_name['t_test']
        else:
            return df_name['welch_t_test']
```

#### p-value를 통한 유의여부 산출
```python
df_ttest['real_pvalue'] = df_ttest.apply(pvalue_result, axis=1)
df_ttest['real_result'] = ['차이있음' if t else '차이없음' for t in list(df_ttest['real_pvalue']<0.05)]
df_ttest['real_pvalue'] = df_ttest['real_pvalue'].round(3)
df_ttest['real_pvalue'] = df_ttest['real_pvalue'].fillna('N/A')
```

<br>

## 결론
해당 솔루션은 사용자가 비교하고자 하는 생산품을 기준으로 주요 요인인자를 보다 수월하게 파악하여 보다 나은 방향으로 공정 운전 개선을 하기 위해 만들어졌습니다. <br>

글에서는 소개하지 않았지만 사용자가 사용하기 편하게 분석 시나리오 관리 기능,  다양한 시각화 기능 등 다양한 기능이 많이 있으며, 화면 수정 및 고도화 단계를 지속적으로 진행하고 있습니다. 또한 아무래도 하나의 솔루션에 다양한 기능들을 모두 추가하는 것이 어렵다보니, 솔루션 목적에 맞게 어떻게 하면 보다 사용자가 더 편리하게 사용하고 인사이트를 도출할 수 있을지 고민하고 있습니다. 

앞으로 다른 솔루션들도 기획 및 개발 단계가 이루어질 예정인데, 이번 솔루션 구축을 통해서 많은 것들을 배웠다고 생각합니다.<br>

### Reference
* [[통계분석 언제 뭘 써야하나] 2. t검정의 분류 (두 집단의 평균비교)](https://hsm-edu.tistory.com/1207)
* [[LightGBM] LGBM는 어떻게 사용할까?](https://greatjoy.tistory.com/72)

------
### 🔜 Next
드디어 작년 말부터 진행했던 솔루션 프로젝트가 끝이 났습니다.🥳 다른 솔루션을 만들 때 있어서 필요한 리스트들을 정리해 나갈수 있던 좋은 경험이었던 것 같아 기분이 좋네요! <br>
데이터의 특성과 분석하고자 하는 목적에 따라 분석 방법이 달라지는데, 해당 솔루션의 경우 비교하고자 하는 두 그룹군에 대한 분석 결과를 산출하는 것이다보니 보다 수월하게 작업할 수 있었던 것 같습니다. <br>
올해에는 꾸준히 솔루션 고도화 및 새로운 솔루션 개발이 이루어질 것 같은데, 다른 솔루션은 어떤 방법을 사용하고 결과들을 보여줄지 고민하는 시간을 많이 가져야할 듯 합니다. 회사 보안상 일부 내용은 축약하고 스킵하다 보니 글이 어수선할 수 있는데, 잘 정리해서 많이 부족하지만 처음 접하시는 분들이 재밌게 읽으실 수 있도록 해보겠습니다🔥
