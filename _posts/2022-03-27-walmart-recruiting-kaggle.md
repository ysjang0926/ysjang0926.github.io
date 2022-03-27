---
layout: post
title: "WalMart Recruiting 데이터로 배우는 Kaggle 기초"
categories: data
tags: kaggle
comments: true
toc: true
toc_sticky: true
---

> WalMart Recruiting 데이터를 통해 간단하게 데이터 불러오기, 데이터 전처리, 모델링, 결과 제출까지의 전체 프로세스를 정리해보았습니다🐱‍👓

## 대회 소개 - WalMart 판매량 예측
![image](https://user-images.githubusercontent.com/54492747/160285517-a952adf8-e22f-4998-88ab-2d082909ee7a.png)

* 대회 링크 : [Walmart Recruiting - Store Sales Forecasting](https://www.kaggle.com/competitions/walmart-recruiting-store-sales-forecasting)
* 목표 : WalMart의 각 지점과 부서의 과거 매출 기록들을 토대로 미래 월마트 지점들의 부서 전체 판매량을 예측하는 것
* 데이터 : 45개의 Walmart 매장에 대한 과거 판매 데이터 (2010년 2월 ~ 2011년 마지막 주)
    * Walmart는 연중 프로모션 인하 이벤트를 운영하는데, 이런 이벤트들은 메인 연휴를 앞두고 진행되는 편 (슈퍼볼, 노동절, 추수 감사절, 크리스마스가 가장 대표적인 연휴)
    * 위의 메인 공휴일을 포함하는 주(week)는 비공휴일보다 판매량에 더 영향을 주지 않을 것인가? 라고 생각하고 진행

---

## 1. Data Load
* 이번 notebook에서는 train과 test data만을 불러와서 진행하였습니다. (stores, features data 사용 안함)
* `pd.read_csv()` 함수를 사용하면 csv 형식의 파일을 읽어서 저장할 수 있습니다.
    * 괄호 안에는 해당 파일의 경로를 넣어주면 되는데, 만약 zip 형식이더라도 파일이 하나만 있다면 상관없습니다.
    * train과 test라는 데이터명에 데이터를 저장하고 train과 test를 쳐주면 data를 출력할 수 있으며, 이때 row와 column 갯수를 확인할 수 있습니다.

```python
# This Python 3 environment comes with many helpful analytics libraries installed
# It is defined by the kaggle/python Docker image: https://github.com/kaggle/docker-python
# For example, here's several helpful packages to load

import numpy as np # linear algebra
import pandas as pd # data processing, CSV file I/O (e.g. pd.read_csv)

# Input data files are available in the read-only "../input/" directory
# For example, running this (by clicking run or pressing Shift+Enter) will list all files under the input directory

import os
for dirname, _, filenames in os.walk('/kaggle/input'):
    for filename in filenames:
        print(os.path.join(dirname, filename))

# You can write up to 20GB to the current directory (/kaggle/working/) that gets preserved as output when you create a version using "Save & Run All" 
# You can also write temporary files to /kaggle/temp/, but they won't be saved outside of the current session
```
![image](https://user-images.githubusercontent.com/54492747/160290122-2ba81d5b-c5b0-414f-bd01-70e93b1c3ef2.png)

### train 출력 결과
* 421570 rows × 5 columns
* 변수(5개) : 지점번호 / 부서 번호 / 날짜 / 그 주의 매출 / 휴일 유무

```python
train = pd.read_csv('/kaggle/input/walmart-recruiting-store-sales-forecasting/train.csv.zip')
train
```
![image](https://user-images.githubusercontent.com/54492747/160289190-ca84a14e-fb78-4bac-8ceb-06138e1d83bd.png)

* `isna().sum()`을 통해 결측치 확인을 할 수 있습니다.
    * train 데이터는 모두 0이라고 결과가 나오니 결측치가 없는 것을 알 수 있습니다.

```python
train.isna().sum()
```
![image](https://user-images.githubusercontent.com/54492747/160289237-42b1eb4f-b4ca-4291-8244-b068667c5ee2.png)

### test 출력 결과
* 115064 rows × 4 columns

```python
test = pd.read_csv('/kaggle/input/walmart-recruiting-store-sales-forecasting/test.csv.zip')
test
```
![image](https://user-images.githubusercontent.com/54492747/160289281-13669245-ffe6-4c0a-8185-edc5f09da131.png)

```python
test.isna().sum()
```
![image](https://user-images.githubusercontent.com/54492747/160289300-3d36b7ee-842c-4a5e-914a-7e523ce701ef.png)

------
## 2. Data Preprocessing
* 데이터 전처리 과정에서는 간단하게 두가지 과정만 거쳤습니다.
    1. Data column를 datetime 타입으로 변환하여 Year, Month 변수를 새로 생성
    2. train data와 test data의 column 개수 맞추기 (필요없는 column 제거)

### (1) Data column 전처리

#### Column Type 확인
* `.dtypes`를 통해 column type을 확인할 수 있습니다.
* 여기서 Date column은 object 타입으로 나왔기 때문에, datetime 타입으로 변환이 필요합니다.

```python
train.dtypes
```
![image](https://user-images.githubusercontent.com/54492747/160289372-4c04767c-c4a1-4b64-8691-e50ab52baa3c.png)

#### datetime 타입으로 변환
* Date column에서 숫자만 추출하여 Month, Year 등으로 추출하기 위해서 진행하였습니다.
    * pandas의 `to_datetime()`함수를 이용해 datetime 타입으로 바꾸어주었습니다.
    * Year, Month, Date를 추출하기 위해 진행하였습니다.
* Year : Date 중 년도 추출 / Month : Date 중 월 추출
* train을 출력하면 column이 2개(Year, Month) 추가되어있는 것을 확인할 수 있습니다.

```python
train['Date'] = pd.to_datetime(train['Date']) # train["Date"] = train["Date"].astype("datetime64")
train['Year'] = train['Date'].dt.year
train['Month'] = train['Date'].dt.month
train
```
![image](https://user-images.githubusercontent.com/54492747/160289436-dbd73e0f-c8e4-49dd-81df-2d0ffd6090a9.png)

* `.dtypes`로 확인을 하면 Date는 datetime 타입으로 변했고, Year과 Month column은 정수(int64) 형태로 추가된 것을 알 수 있습니다.

```python
train.dtypes
```
![image](https://user-images.githubusercontent.com/54492747/160289475-577cf4c5-7073-442a-9d34-2c7553c04a96.png)

* test도 train과 같은 방식으로 데이터 타입을 바꾸고 column을 추가해주었습니다. (train과 test의 column 개수는 동일해야하기 때문)

```python
test['Date'] = pd.to_datetime(test['Date'])
test['Year'] = test['Date'].dt.year
test['Month'] = test['Date'].dt.month
test
```
![image](https://user-images.githubusercontent.com/54492747/160289500-ae5d2317-d57c-4ee4-a0be-86ae90727f66.png)


### (2) 필요없는 column 제거
* train data에 Weekly_Sales column 제거
    * test data를 보면 Weekly_Sales column이 존재하지 않습니다. (구하고자하는 판매량 column이기 때문)
* train data와 test data에 Date column 제거
    * 분석에 필요한 Year과 Month column을 추출하였으니, 필요없는 Date column은 제거하였습니다.
    * 즉, datetime 타입으로 변환한 것은 Year와 Month column을 쉽게 정수로 변형하기 위함이었습니다. (datetime 타입만으론 분석에 활용하기에 어렵다고 판단)
* `.drop(columns=[...])`를 사용하여 column 제거
    * column 여러개를 삭제하고 싶으면 `[]`를 사용하여 적어주면 됩니다. (여러 column 호출 또는 특정 column 호출하기 위함)
* train2와 test2로 새로운 데이터명으로 저장
    * 왜냐하면 기존 train, test 데이터에 column을 제거하면, 제거한 상태로 저장이 되기 때문에 필요할 때 다시 불러올 수 없기 때문입니다.
    * train 데이터의 Weekly_Sales column이 필요하기 때문에 새로운 데이터명으로 저장하였습니다.

```python
train2 = train.drop(columns=['Date', 'Weekly_Sales']) # Date,Weekly_Sales 제거
train2
```
![image](https://user-images.githubusercontent.com/54492747/160289548-fa504bde-b786-45df-9901-ffdea9036ff1.png)

```python
test2 = test.drop(columns=['Date']) # Date 제거
test2
```
![image](https://user-images.githubusercontent.com/54492747/160289567-7eb6fc5d-2727-4644-879e-f8f1a22f37c8.png)

---------------

## 3. EDA (Month vs Weekly_Sales)
* Box-plot을 통해 Month column이 Weekly_Sales column과 유의미한 관계가 존재하는지 확인하였습니다.
    * seaborn과 matplotlib.pyplot을 import하고, `plt.figure()`과 `sns.boxplot()`을 사용하면 Box-plot을 그릴 수 있습니다.
    * `plt.figure()` 함수에 `figsize = (가로, 세로)` 파라미터를 넣어주면 그림 크기를 조정할 수 있습니다.
    * 이때 Month column과 Weekly_Sales column 사이의 유의미한 관계가 존재하는를 알아보기 위함이니, `sns.boxplot()` 함수의 파라미터로 Month column과 Weekly_Sales column을 넣어주었습니다.


```python
import seaborn as sns
import matplotlib.pyplot as plt
plt.figure(figsize = (14, 8))
sns.boxplot(train['Month'], train['Weekly_Sales'])
```
![image](https://user-images.githubusercontent.com/54492747/160289608-71166c1e-660d-4fad-b414-9b154e1fcdf4.png)

* outlier를 제거하여 다시 Box-plot을 그렸습니다. (outlier가 존재해서 한눈에 Box-plot을 파악하기 어렵기 때문)
    * `sns.boxplot()` 함수에서 `showfliers` 옵션을 `False`로 설정하여 outlier를 제거해주었습니다.

```python
plt.figure(figsize = (14, 8))
sns.boxplot(train['Month'], train['Weekly_Sales'], showfliers = False)
```
![image](https://user-images.githubusercontent.com/54492747/160289657-432147a5-4d34-476d-aeea-29f25a5c337b.png)

* Box-plot 해석
    * box의 가운데 선 : 중앙값 / box의 윗 선 : 상위 25%에 해당하는 값 / box의 아랫선 : 75%에 해당하는 값 / 맨 위의 선 : 최댓값 / 맨 아래의 선 : 최솟값
    * 월별로 꽤 유의미한 차이가 존재한다는 것을 알 수 있습니다.
    * 또한 최솟값 중에서 마이너스인 값들이 일부 존재하는데, 실제 판매량보다 반품 또는 환불이 많아 수익이 나지 않은 경우 마이너스 값으로 들어왔을 것이라 추측하였습니다.
<br>

※ Overview에서 [Evaluation](https://www.kaggle.com/competitions/walmart-recruiting-store-sales-forecasting/overview/evaluation)을 보면, 휴일 주의 가중치를 다른 주의 가중치보다 5배 더 높게 부여한 것을 확인할 수 있습니다.

![image](https://user-images.githubusercontent.com/54492747/160285717-2aa6794f-106c-43ce-bf93-974c7ef68896.png)

* 즉, 휴일의 판매량을 정확하게 예측할수록 정확도가 더 높은 것으로 판단하겠다는 의미입니다.
* 그렇기 때문에 어떤 월의 휴일에 판매량이 더 높고 낮은지를 판단할 수 있는 Month column이 중요한 역할을 하게 됩니다.
* 대회마다 다르겠지만 WalMart 대회처럼 특정 조건의 점수 산정 방식이 다를 수 있기 때문에, 대회에서 좋은 점수를 받고 싶다면 점수 산정 방식을 고려하여 어떻게 데이터를 전처리하고 분석하면 더 높은 점수를 받을 수 있을지에 대한 고민이 필요합니다.

-----------

## 4. Modeling
* RandomForest 모델 사용
    * RandomForest에는 크게 두 가지 종류가 있으며, RandomForestClassifier와 RandomForestRegressor가 있습니다.
    * RandomForestClassifier : 분류를 할 때 사용 / RandomForestRegressor : 숫자 예측할 때 사용
    * 여기서는 예측하고자 하는 결과값이 한 주의 판매량이었기 때문에 RandomForestRegressor를 사용하였습니다.

### (1) 모델 가져오기
```python
from sklearn.ensemble import RandomForestRegressor 
```

### (2) 모델 적용
* `?RandomForestRegressor`를 통해 모델을 구성할 때 필요한 옵션들을 확인할 수 있으며, 이번에는 `n_jobs` 옵션만 사용하였습니다.
* `n_jobs` : 사용할 코어수(CPU의 core개수) 지정하며, 사용하는 CPU 코어 개수에 비례해서 속도도 빨라집니다.
    * `n_jobs=-1`로 지정하면 컴퓨터의 모든 코어를 사용하는 것 (Use all available cores on the machine)
    * kaggle에서는 사용자 당 최대 4개의 CPU를 지원해주며, 제일 많이 사용하고 싶다면 4를 적어도 되고 최댓값을 의미하는 -1을 적어도 됩니다. (코랩의 경우 최대 2개의 CPU를 지원)
* `.fit()` : 모델 훈련
    * 이때 왼쪽에는 학습 데이터, 오른쪽에는 구하고자하는 column을 넣어줘야 합니다.
    * 학습 데이터 : 전처리한 train2 데이터 / 구하고자하는 column : Weekly_Sales column
* `%%time` : 코드를 수행하는데 걸린 시간 측정

```python
%%time
rfr = RandomForestRegressor(n_jobs = -1)
rfr.fit(train2, train['Weekly_Sales'])
```

### (3) 값 예측
* `.predict()` : 값 예측
    * 모델을 학습시키고 나서 test 데이터에 대한 예측을 하기 위해 result에 test2에 대한 예측 결과를 저장합니다.

```python
result = rfr.predict(test2)
result # array 형식
```
![image](https://user-images.githubusercontent.com/54492747/160289770-0c0366dc-0c71-4667-afcb-28164f0c6365.png)

--------

## 5. Submit Result
* 예측한 결과값을 제출 데이터 형태에 맞게 저장한 후 제출하면 됩니다.
    * kaggle에서는 제출 데이터의 경우 sampleSubmission 파일을 통해 확인할 수 있고 기본으로 제공되며, train과 test data와 같이 위에서 데이터 경로를 알 수 있습니다.

### 제출 데이터 형태
* 제출해야하는 데이터는 아래와 같은 형식이고, Weekly_Sales 부분에 예측한 결과값을 넣어주면 됩니다.

```python
sub = pd.read_csv('/kaggle/input/walmart-recruiting-store-sales-forecasting/sampleSubmission.csv.zip') # 제출양식
sub
```
![image](https://user-images.githubusercontent.com/54492747/160289817-518ebf50-77c9-4d64-8bac-af119190156b.png)

### 예측한 결과값 넣기
* sub data의 Weekly Sales column에 result값(예측값)을 넣어주고 출력하면 값이 무사히 들어간 것을 확인할 수 있습니다.

```python
sub['Weekly_Sales'] = result
sub
```
![image](https://user-images.githubusercontent.com/54492747/160289849-103ea5a5-d685-45a7-8804-5e40c72c50a6.png)

### csv file 저장
* `.to_csv` 함수를 통해 sub data를 다시 csv 파일로 저장한 후에 제출을 하면 됩니다.
    * 왼쪽에 파일 이름, 오른쪽에 `index = False` 라고 기입
    * index 옵션은 왼쪽의 쭉 적혀있는 0~115063 숫자들을 의미하며, 이 숫자를 column에 포함할건지에 대한 부분입니다.
    * False로 설정하지 않으면 default로 `index = True`로 설정되어 있기 때문에 index가 column으로 포함되게 됩니다.
    * 해당 대회의 제출 양식에 index는 포함되지 않았으므로 index를 만들지 않기 위해 `index = False`라는 옵션을 넣어줍니다.

```python
sub.to_csv('sub.csv', index = False) # index = 0
```

-----

다음 포스팅에서는 유명한 대회 중 하나인 [Bike Sharing Demand](https://www.kaggle.com/c/bike-sharing-demand/overview)를 다뤄보도록 하겠습니다.🚴‍♂️
