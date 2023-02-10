실험 의사결정 가이드 자동화 : QXP Tracker
> 제가 회사에서 솔루션에 도입했던 실험 의사결정 가이드 자동화 과정을 정리하였습니다. 수정~!

회사에서 작년부터 약 3달 간 솔루션 개발을 진행하여 올해 1월 말에 구축 완료하였습니다.🥳<br>
(정식적으로 솔루션 개발 및 구축을 한 것은 이번이 처음이라 고생하였는데 넘 뿌듯하네요!)
<br>
현재는 화면 수정 및 고도화 단계를 계속 진행하고 있으며, 이번 글에서는 솔루션에 대한 간단한 소개와 최근에 진행하였던 통계적 유의성을 반영하기 위해 평균차이 검정 결과값을 적용한 과정을 소개하려고 합니다.

------

## 유의차(significant differece) 분석
**유의차 분석**은 데이터의 평균 또는 비율 등의 차이가 통계적으로 의미가 있는지, 통계 모형을 사용하여 검정(test)하는 분석 기법입니다. 아래의 그림을 예시로 들면, 수집한 데이터에 대한 group별, condition별 평균값의 차이는 실제로 차이가 있다고 할 수 있는지 판단할 수 있도록 도와주는 역할을 합니다.
![image](https://user-images.githubusercontent.com/54492747/217399254-28be5e51-475a-453b-8b5e-4b549202d1d6.png)
[출처 : Assessing differences of significance](https://janhove.github.io/analysis/2014/10/28/assessing-differences-of-significance)

<br>

그렇다면 실제 제조 분석에서는 유의차분석을 언제 사용할까요?
- 생산 설비에 설치된 **수많은 센서(sensor) 데이터** 중
- **제품 물성이 좋은 Group과 그렇지 않은 Group 사이에 평균이 차이가 있는 센서**는 어떤 센서이며
- **제품 물성에 영향력이 있는지**를 확인할 때 필요합니다.
(여기서 sensor는 어떤 물질의 양이나 온도를 감지하여 측정하는 장치를 말하며, 여기서 측정된 값을 센서값 또는 태그값이라 부릅니다.)

현장에서 비교(ex.양품/불량품)하고자 하는 생산품을 기준으로 유의차분석 수행을 통해 원인 인자 파악이 가능하다면 좋지 않을까 생각했고, 이러한 기능을 구현한 프로그램을 **유의차분석 솔루션**이라고 부르기로 했습니다.

유의차분석 솔루션을 간단하게 설명하자면 다음과 같습니다.
* 시스템에 저장된 데이터를 분석하여 제품 품질에 영향을 미치는 주요 원인을 파악하기 위한 프로그램
* 목적 : 비교(ex.양품/불량품)하고자 하는 그룹 간 유의 인자 탐색 및 시각화

솔루션을 사용하기 위해서는 **분석을 실행하는 부분**과 **분석 결과를 집계 및 시각화**하는 부분으로 나뉩니다. 이번 글에서는 **분석 결과를 집계**하는 부분을 다루도록 하겠습니다.

분석 결과 집계는 아래와 같은 과정으로 이루어져 있습니다. <br>
우선 분석하고자 하는 요인들(센서, 품질 등)을 머신러닝 모델을 통해 중요도를 산출하여, 주요 요인 리스트를 생성합니다. 주요 요인 리스트 기준으로 통계적 유의성 검정을 진행하여 검정 결과를 산출합니다. 마지막으로 중요도 및 검정 결과를 집계하여 하나의 테이블로 보여줍니다.
1. 주요 요인 리스트 및 중요도 산출
2. 대조군/실험군 평균 비교 검정 결과 추출

### 1. 주요 요인 리스트 및 중요도 산출
비교하고자 하는 그룹 간 유의 인자에 대한 중요도는 **LightGBM**에 의해 결정됩니다. <br>
LightGBMdms GBM 기반의 알고리즘이고, XGBoost(eXtra Gradient Boost)보다 2년 후에 만들어졌으며 XGBoost의 장점은 계승하고 단점은 보완하는 방식으로 개발되었습니다. 가장 큰 장점은 XGBoost와 GBM에 비해 훨씬 학습에 걸리는 시간이 적다는 것이며, 성능상으로 보았을 때에도 XGBoost와 큰 차이가 없습니다. <br>
센서 데이터의 경우 1초, 1분 등 시계열 데이터로 구성되어 있기 때문에, 비교하고자 하는 그룹 수와 센서 수가 많아질수록 데이터의 양은 방대해집니다. 이러한 이유로 유의차분석 솔루션에서는 모델 정확성보다는 중요도 결과를 빠르게 내는 것이 더 중요하기 때문에, 다른 모델 대비 빠른 시간 내에 학습하여 결과를 산출하는 LightGBM을 선정하게 되었습니다.
<br>
이와 같은 LightGBM을 python의 `lightgbm` library를 활용하여 구현했고 아래와 같습니다. <br>
이때 lightGBM은 중요도를 Gain, Split 이렇게 2가지를 제공하며, `Gain`은 모델 정확도에 더 좋으나 솔루션에서는 데이터를 나누는 기준점을 찾는 것이 더 중요하기 때문에 `Split`으로 사용하였습니다.
```python
# LightGBM 모델에 맞게 Data Set 변환
import lightgbm as lgb
lgb_dtrain = lgb.Dataset(data = data_x, label = data_y)

# LGBM : Binary Split & Boosting을 얼마나 돌릴지는 100으로 지정
model = lgb.train(
params = {"objective": "binary"},
train_set = lgb_dtrain,
num_boost_round = 100
)

# Importance 계산 : type은 Split 사용
importance_df = (
pd.DataFrame({
# 'feature_name': model.feature_name(),
'TAGID' : x.columns,
# 'Variable_Importance': model.feature_importance(importance_type = 'gain')
'Variable_Importance': model.feature_importance(importance_type = 'split'),
})
.sort_values('Variable_Importance', ascending = False)
.reset_index(drop = True)
)
```

<br>

### 2. 대조군/실험군 평균 비교 검정 결과 추출
중요도를 통해 생성된 주요 요인 리스트가 통계적으로 유의미하게 차이가 있는지는, 서로 다른 2개 집단 평균 비교 검정을 통하여 확인하였습니다. <br>
저희가 흔히 알고 있는 검정 방법은 다음과 같습니다.~~수정~~
![유의차분석솔루션_t검정종류](https://user-images.githubusercontent.com/54492747/217399696-30e4362e-ac07-445f-bb39-cc4c62f85fc6.png)

하지만 제조 데이터의 경우, 하나의 BATCH 또는 LOT에 대하여
- **1번씩만 기록되는 품질 검사결과(2번 이상일 수도 있음)**와
- **1초, 1분으로 값이 기록되기 때문에 수없이 많은 시계열 데이터인 센서 값**이 있기 때문에
- 이에 대한 기준을 설정하는 것이 중요하였습니다. <br>

데이터에 따라 어떤 검정 방식을 사용해야 하는지는 아래와 같은 방식에 의해 결정되도록 하였습니다.
![유의차분석솔루션_t검정솔루션케이스](https://user-images.githubusercontent.com/54492747/217399633-a2d56aab-9a9f-40ca-9606-14f5533b49de.png)

1. 데이터의 종류
    * 우선 데이터의 종류를 센서(=TAG) 리스트와 품질 리스트로 구분하였습니다.
    * 센서값의 경우 최소 1,000개 이상이 존재하기 때문에 충분한 모수를 지니고 있기에, 정규성 가정을 만족한다고  
2. ㅇㅇ

우선 metric의 종류를 proportion과 continuous로 나누었습니다.

proportion은 0 아니면 1인 값을 집계하여, 결과가 0~1 사이의 percentage로 나오는 metric으로, 전환율이나 retention과 같은 metric이 이에 해당합니다. continuous는 그 이외에, continuous한 값을 집계하는 metric으로, ARPU(Average Revenue Per User)나 유저당 검색 수와 같은 metric이 이에 해당합니다.

빈도주의 관점에서, proportion 타입의 metric은 z-test를 활용하여 통계적 유의성을 계산합니다. 그리고 continuous 타입의 metric은 t-test를 활용하여 통계적 유의성을 계산합니다.

p-value를 계산하여, p-value가 0.05 이하면 통계적으로 유의미하다고 판단합니다.

기준으로 통계적 유의성 검정을 진행하여 검정 결과를 산출합니다. 

최근에 진행하였던 통계적 유의성을 반영하기 위해 평균차이 검정 결과값을 적용한 과정을 소개하려고 합니다.

python의 `scipy` library의 `stats` 함수를 이용하여 구현했고 아래와 같습니다. <br>
이때 lightGBM은 중요도를 Gain, Split 이렇게 2가지를 제공하며, `Gain`은 모델 정확도에 더 좋으나 솔루션에서는 데이터를 나누는 기준점을 찾는 것이 더 중요하기 때문에 `Split`으로 사용하였습니다.

##### 얘는 얘애~
```python
import scipy.stats

col_list = list(final_df.columns[1:])
shapiro0_p_list = []; shapiro1_p_list = []
levene_p_list = []; ttest_p_list = []; welcht_p_list = []; mannwhitney_p_list = []
test_result_list = []

for i in col_list:
    YN_0 = final_df[final_df['CMPRSN_EXPRM_YN']=='0'][i].dropna()
    YN_1 = final_df[final_df['CMPRSN_EXPRM_YN']=='1'][i].dropna()
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

##### 얘는 얘애~
ㅇㅇㅇ
```python
search = 'N000'

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

##### 얘는 얘애~
ㅇㅇㅇ
```python
df_ttest['real_pvalue'] = df_ttest.apply(pvalue_result, axis=1)
df_ttest['real_result'] = ['차이있음' if t else '차이없음' for t in list(df_ttest['real_pvalue']<0.05)]
df_ttest['real_pvalue'] = df_ttest['real_pvalue'].round(3)
df_ttest['real_pvalue'] = df_ttest['real_pvalue'].fillna('N/A')
```

### 결과 집계
실험 결과 분석
실험의 결과 분석은 이전에 Metric을 체계적으로 관리하기 : Metrics Store에서 소개해드린 metrics store를 활용하여 집계하고 있습니다.

다음과 같이 원하는 실험의 ID를 BigQuery Table Function의 argument로 넘겨서, 실험 결과를 구할 수 있습니다.

## 결론
QANDA Team에서는 실험 플랫폼인 QXP와 metrics store를 만들어서 실험과 metric이 체계적으로 관리되도록 노력해왔습니다. QXP와 metrics store를 잘 연동함으로써, 실험 결과 집계를 자동화하고, decision tree를 통해 실험의 의사결정에 대한 가이드를 자동으로 결정하고, 이에 대한 내용을 slack으로 발송하여 궁극적으로는 실험에 대해 올바르게 의사결정 할 수 있도록 도울 수 있었습니다.

이제는 admin에 선언한 성공 지표와 가드레일 지표뿐만 아니라, 다른 지표까지 자동으로 집계하도록 개선하는 것을 고민하고 있습니다. 기본적인 가드레일 지표 목록을 구비하고, 이 지표들은 시스템이 자동으로 집계하려고 합니다. 그리고 하나라도 가드레일 지표가 저해되면, 메시지를 발송하도록 하여, 모든 가드레일 지표가 자동으로 확인되기를 기대합니다.

### Reference
* [Raghavendra Chalapathy, Sanjay Chawla. “Deep Learning for Anomaly Detection: A Survey.” arXiv, 2019](https://arxiv.org/abs/1901.03407)
* [깃헙 블로그 글 - Anomaly Detection 개요](https://hoya012.github.io/blog/anomaly-detection-overview-1/)
	* 이상치 탐지 분야에 대한 소개 및 주요 문제와 핵심 용어, 산업 현장 적용 사례를 깔끔하게 잘 정리해주셨습니다.
* [고려대학교 DMQA 연구실 세미나 내용](http://dmqm.korea.ac.kr/activity/seminar/339)

------
### 🔜 Next
드디어 작년부터 생각만 해오던 Anomaly Detection 포스팅 시리즈의 막을 올렸습니다🥳 활발하게 연구가 진행되고 있는 분야인만큼 소개할 내용이 많아서 기분이 좋네요! <br>
제조업 특성상 장비로부터 측정된 시계열 데이터가 메인이다보니, 장비의 고장과 같은 Abnormal한 케이스를 예측하고 싶어하는 니즈가 많습니다. 특히 저는 Normal 관측치만으로 학습하여 Abnormal 관측치 탐지를 진행하는 Deep Learning 기반 알고리즘에 관심이 많아, 짬짬히 공부하던 Anomaly Detection 내용을 한번 정리하는 것이 좋겠다고 생각되어 글을 작성하게 되었습니다. <br>
관련해서 캐글 필사를 하기도 하고, 논문을 읽기도 하고, 실데이터에 접목시키기도 하고 다양하게 공부를 진행하고 있는데, 이것을 잘 정리해서 많이 부족하지만 처음 접하시는 분들이 재밌게 읽으실 수 있도록 해보겠습니다🔥 <br>
이어지는 포스팅에서는 순차적으로 Machine Learning for Anomaly Detection, Deep Learning for Anomaly Detection, Anomaly Detection for Time Series에 대해 다뤄보도록 하겠습니다.
