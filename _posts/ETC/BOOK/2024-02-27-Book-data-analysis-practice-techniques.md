---
layout: post
title:  "[책리뷰] 파이썬 데이터 분석 실무 테크닉 100 "
subtitle:   "real analysis tech"
categories: etc
tags: etc_book 
comments: true
---

저희 회사에서도 그렇고, 비즈니스 현장에서는 데이터 사이언티스트(=데이터 분석가)가 부족하다고 합니다. 하지만 제 주위에는 분석 업무로 일하고 있는 지인들이 많을 뿐만 아니라, 분석가로 취업하고 싶은 취준생도 많거든요.

왜 이런 간극이 있는 것일까요? chatgpt, 쉽게 제공되는 개발환경 등으로 인해 누구나 엔지니어가 될 수 있는 축복받은 시대임에도 불구하고 말이죠. <br>
그 이유 중 하나는 바로 **도메인에 맞게 응용 능력이 있는 데이터 사이언티스트**가 없다는 것입니다. 머신러닝 관련 책에서는 '붓꽃(iris)을 분류해보자!'라는 예제만을 알려주지만, 실제 비즈니스 현장에서는 이러한 과제만을 해결하지 않기 때문이죠.

![LinkedIn 𝗥𝗶𝗰𝗵𝗮𝗿𝗱 𝗟𝗶𝗺 페이지: #데이터리차드 #데이터분석](https://media.licdn.com/dms/image/C5622AQE7t1zIRfWtfA/feedshare-shrink_2048_1536/0/1656975530079?e=2147483647&v=beta&t=OhXRtW4H4V_BPsua2UuJnNrjfGByCiXUzcweH3_6-LU)

<br>

저의 경우에도 제가 일하고 있는 도메인 영역은 잘 알고 있지만, 다른 도메인 영역에서 어떤 데이터를 다루고 그런 데이터를 다룰 때 어떤 문제가 발생하는지 모릅니다. <br>
다른 도메인 영역의 데이터 분석 사례를 공부하고 싶어서 찾던 중에, 마음에 쏙 드는 책을 발견하여 이번 한 주 동안 공부하려고 합니다😘

<br>

## 📗 책 소개

소개할 책은 [파이썬 데이터 분석 실무 테크닉 100](https://www.yes24.com/Product/Goods/91302724) 입니다.
![cover](https://github.com/wikibook/pyda100/raw/master/cover.jpg)

<br>

이 책은 실제 비즈니스 현장을 가정한 100개의 예제를 풀면서 현장 감각을 익히고, 기술을 현장에 맞는 형태로 응용할 수 있는 힘을 기를 수 있게 설계한 **분석 실습서**라고 할 수 있습니다. 물론 이 책을 본다고 완전한 전문가가 되는 건 불가능하겠지만, 현장 감각을 익히기엔 좋을 것 같다고 생각이 들었습니다. 깊게는 아니더라도 최소한 '이런 데이터를 보면서, 이런 접근으로 가는구나'를 알 수 있기 때문이죠.

저처럼 **'비즈니스 현장에서 기술이 어떻게 응용되는지 알고 싶다'** 라는 생각하신 분들께 도움이 될 것이라 기대하고 있습니다. 이번주 중으로 책 완독을 목표로 하고 있어서, 해당 부분은 다 읽고 다시 후기를 업데이트 하도록 하겠습니다.

> 📌 최종 후기 📌 <br>
> 일부 챕터는 정리를 마저 못했지만, 완독까지는 했는데요! <br>
> 생각했던 것만큼 깊이 있는 내용을 다루지 않아서 아쉬웠지만, 분석을 막 시작하는 분들에게는 맛보기용으로 좋을 것 같다는 생각을 했습니다. <br>
> 다른 도메인에는 어떤 상황에서 어떤 데이터를 보고자 하는지 알 수 있어서 좋았습니다. <br>
> 하지만 분석을 하고 계시거나 실무에 계신 분들이 이 책을 읽으신다면, 조금 아쉬움이 많이 남는 책일 것이라 생각됩니다😢 <br>
> 가볍게 보기에 좋은 책 같아요!

<br>

이 책의 저자는 데이터 사이언티스트의 실제 모습은 **전략 컨설턴트**에 가깝다고 말합니다. 즉, **데이터를 통해 조직의 의사결정을 하는 것에 도움을 주는 역할**을 하는거죠. <br>
예시를 들며 '실제 현장에서는 이런 일이 있다'라고 적어주신 부분이 모두 공감 되어서 더 재밌게 술술 읽어지더라구요. 

저자는 업무를 할 때 필요로 되는 것 중 하나가 **'현장력'**이라고 말합니다. <br>
현장력은 **[어떤 데이터를 어떻게 분석하고 가시화해야 하는지 경험에 근거해서 앞을 바라보는 능력]**을 의미하는데요. 현장력을 구사해야 의뢰인이 원하는 분석을 할 수 있고, 그 보상으로 매출 증대에 공헌할 뿐만 아니라 현장 경험을 탄탄하게 쌓을 수 있다고 합니다.

<br>

## 📕 책 구성

책은 기초부터 실전까지 다루고 있기에 총 4부로 구성되어 있습니다. (4부, 10개 장)

* **1부 - 기초 편**
	* 비즈니스 현장에서 실제로 얻을 수 있는 데이터를 분석하기 위한 데이터 가공 방법
* **2부 - 실전 편(1)**
	* 머신러닝 기술을 사용해 고객을 분석하는 방법 <br>
	* 데이터를 사용해 문제를 발견하고 해결하는 과정
* **3부 - 실전 편(2)**
	* 최적화 기술 적용 방법 + 경영 현황 개선 방안
* **4부 - 발전 편**
	* 이미지 인식 기술과 자연어 처리 기술과 같은 AI기술 <br>
	* 데이터화되지 않은 정보를 이용해서 고객의 잠재적인 수요를 파악하는 방법

<br>

전체 10개의 장에서 상황에 맞는 사례가 있는데요. 고객으로부터 받은 의뢰(사례)를 바탕으로 다음의 두가지를 고민할 수 있도록 방향성을 제시합니다. <br>
**1️⃣ 현재 상황에 처한 정보와 데이터가 무엇인지 (전제조건) <br>
2️⃣ 고객의 요구에 부응하기 위해 어떤 분석을 진행해야 하는지**

분석 방법은 하나가 아니기 때문에, <br>
공부하면서 '이렇게 해도 되지 않을까?' 관점으로 개선점을 고민할 수 있다는 점이 이 책의 장점 중 하나인 것 같습니다. <br>
(저도 책을 보면서, 제가 개인적으로 궁금한 부분은 따로 코드쳐서 보기도 하였어요! 이런 부분에서 더 재밌게 다가왔습니다)

<br>

## 📘 공부 리스트
앞으로 매 chapter마다 공부한 내용을 정리한 글을 쓸 예정인데요. 글이 완성될 때마다 포스팅 url 업데이트를 하도록 하겠습니다😊

* 예제코드 : [데이터 및 예제코드](https://github.com/wikibook/pyda100)
* **1부 - 기초편) 데이터 가공**
	* [1장 - 웹에서 주문수를 분석하는 테크닉](https://github.com/ysjang0926/Study_Book/blob/main/Python%20Data%20Analysis%20Practice%20Techniques%20100/%ED%8C%8C%EC%9D%B4%EC%8D%AC%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B6%84%EC%84%9D%20%EC%8B%A4%EB%AC%B4%20%ED%85%8C%ED%81%AC%EB%8B%89%20100%20-%201%EC%9E%A5.ipynb)
	* [2장 - 대리점 데이터를 가공하는 테크닉](https://github.com/ysjang0926/Study_Book/blob/main/Python%20Data%20Analysis%20Practice%20Techniques%20100/%ED%8C%8C%EC%9D%B4%EC%8D%AC%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B6%84%EC%84%9D%20%EC%8B%A4%EB%AC%B4%20%ED%85%8C%ED%81%AC%EB%8B%89%20100%20-%202%EC%9E%A5.ipynb)
* **2부 - 실전편) 머신러닝**
	* [3장 - 고객의 전체 모습을 파악하는 테크닉](https://github.com/ysjang0926/Study_Book/blob/main/Python%20Data%20Analysis%20Practice%20Techniques%20100/%ED%8C%8C%EC%9D%B4%EC%8D%AC%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B6%84%EC%84%9D%20%EC%8B%A4%EB%AC%B4%20%ED%85%8C%ED%81%AC%EB%8B%89%20100%20-%203%EC%9E%A5.ipynb) 
	* [4장 - 고객의 행동을 예측하는 테크닉](https://github.com/ysjang0926/Study_Book/blob/main/Python%20Data%20Analysis%20Practice%20Techniques%20100/%ED%8C%8C%EC%9D%B4%EC%8D%AC%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B6%84%EC%84%9D%20%EC%8B%A4%EB%AC%B4%20%ED%85%8C%ED%81%AC%EB%8B%89%20100%20-%204%EC%9E%A5.ipynb)
	* [5장 - 회원 탈퇴를 예측하는 테크닉](https://github.com/ysjang0926/Study_Book/blob/main/Python%20Data%20Analysis%20Practice%20Techniques%20100/%ED%8C%8C%EC%9D%B4%EC%8D%AC%20%EB%8D%B0%EC%9D%B4%ED%84%B0%EB%B6%84%EC%84%9D%20%EC%8B%A4%EB%AC%B4%20%ED%85%8C%ED%81%AC%EB%8B%89%20100%20-%205%EC%9E%A5.ipynb)
* **3부 - 실전편) 최적화 문제**
	* 6장 - 물류의 최적경로를 컨설팅하는 테크닉 <br>
 	* 7장 - 물류 네트워크 최적 설계를 위한 테크닉 <br>
	* 8장 - 수치 시뮬레이션으로 소비자의 행동을 예측하는 테크닉 
* **4부 - 발전편) 이미지 처리 / 언어 처리**
	* 9장 - 잠재고객을 파악하기 위한 이미지 인식 테크닉 <br>
 	* 10장 - 앙케트 분석을 위한 자연어 처리 테크닉  

<br>

## 📒 중요한 부분 요약
5장까지는 분석보다는 데이터 전처리에 더 신경을 쓴 책이라고 느껴졌습니다. 좋았던 부분도 일부 있었기에, 까먹지말자는 차원에서 따로 정리를 해보았습니다.

#### ⬜ 좋았던 언급들
* 비즈니스 현장에서의 데이터 분석은 상상처럼 '화려'하지 않고 현장이라서 해야 하는 사소한 업무가 의외로 많습니다.
* 어떤 데이터를 어떻게 연결해서 활용할 것인가가 데이터 분석가의 역량을 보여주는 부분입니다.

<details>
<summary> ⬛ 경고(warning) 비표시 </summary>
<div markdown="1">

```python
import warnings
warnings.filterwarnings('ignore')
```

</div>
</details>

<details>
<summary> ⬛ 데이터 결합하기 </summary>
<div markdown="1">

데이터를 결합하는 방법은 세로로 결합(union)하는 방법과 가로로 결합(join)하는 방법이 있습니다.

##### 1. UNION
세로로 결합하기 위해서는 `concat`함수를 사용하며, <br>
`ignore_index=True` 하지 않으면 이전 데이터에 있던 인덱스를 그대로 가져오기 때문에, 특별하게 인덱스를 유지해야하는 경우가 아니면 해당 옵션 사용이 필요합니다.
```python
transaction = pd.concat([transaction_1, transaction_2], ignore_index=True)
```

##### 2. JOIN
가로로 결합하기 위해서는 `merge`함수를 사용하며, <br>
추가하고 싶은 데이터 칼럼이 무엇인지 & 공통되는 데이터 칼럼(key)가 무엇인지 파악하는 것이 중요합니다.
* on : join key 칼럼
* how : join 종류
```python
join_data = pd.merge(transaction_detail, transaction[["transaction_id", "payment_date", "customer_id"]], on="transaction_id", how="left")
```

</div>
</details>


#### ⬛ 데이터 파악하기
데이터 분석을 진행할 때 제일 먼저 살펴봐야할 데이터 파악 방법입니다.

##### 1. 결측치
```python
# 결측치 칼럼 확인
uriage.isnull().any(axis=0)
```

```python
# 결측치 개수 파악
join_data.isnull().sum()
```

##### 2. 데이터 범위
```python
# 데이터 range 파악
join_data.describe()
```

##### 3. 변수 타입
```python
# 데이터형 확인
join_data.dtypes
```

##### 4. 개별 개수 확인
```python
pd.unique(uriage.item_name)
```
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/9e7b9802-e23b-4f39-92d3-333e5538b8c2)


#### ⬛ 데이터 집계하기
##### 1. groupby
```python
# 리스트형으로 추출됨
join_data.groupby(["payment_month", "item_name"]).sum()[["price", "quantity"]] 
```

만약 집계함수를 여러개를 쓰고 싶을 때는 다음과 같이 사용하면 됩니다.
```python
uselog_customer = uselog_months.groupby("customer_id").agg(["mean","median","max","min"])["count"]
uselog_customer = uselog_customer.reset_index(drop=False)
```
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/9004b27a-5795-4e43-a5f3-b38a68ae1892)


##### 2. pivot
* index : 행
* columns : 열
* values : 집계하고싶은 칼럼
* aggfunc : 집계 함수
```python
price_sum_data = pd.pivot_table(join_data, index='payment_month', columns='item_name', values=['price'], aggfunc='sum')
price_sum_data
```
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/2932200a-1030-49e3-9ac8-916b93a0caf9)

이때 가로 값을 뽑고싶을 때는 다음과 같이 추출할 수 있습니다.
```python
list(price_sum_data.index)
```
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/cb1f4892-f192-415c-b99d-bfca9d30b73e)

칼럼은 두개 존재하며 다음과 같이 볼수 있습니다.
```python
price_sum_data.columns
```
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/d72ccf71-47f8-4be5-b899-5437de1c2add)

그렇기 때문에 하나의 칼럼에 대한 값을 추출하기 위해서는 다음과 같이 작성해야 합니다.
```python
price_sum_data['price']['PC-A']
```
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/2dbe600b-ed6c-4548-9576-f4a25062a483)

#### ⬛ 데이터 수정하기
##### 1. 대문자 변환
```python
uriage["item_name"] = uriage["item_name"].str.upper()
```

##### 2. 공백 제거
```python
uriage["item_name"] = uriage["item_name"].str.replace(" ", "")
```

##### 3.결측치 채워넣기
책에서는 결측치에 같은 상품의 단가를 이용하여 수정하였습니다.
```python
# step1 : null값인 행 뽑기
flg_is_null = uriage["item_price"].isnull()

# step2 : null인 행의 item_name 추출하기
# step3 : null이 아닌 행의 item_name 가격 뽑아보기
# step4 : 위 내용을 loop문을 통해 만들어주기
for trg in list(uriage.loc[flg_is_null,"item_name"].unique()):
  price = uriage.loc[(~flg_is_null)&(uriage["item_name"]==trg), "item_price"].max()
  uriage["item_price"].loc[(flg_is_null)&(uriage["item_name"]==trg)] = price
```

##### 4. 변수명 변경
```python
uselog_months.rename(columns={"log_id":"count"}, inplace=True)
```

##### 5. 변수 제거
```python
del uselog_months["usedate"]
```

#### ⬛ 데이터 필터링
```python
customer_newer = join_data.loc[(join_data["end_date"] >= pd.to_datetime("20190331")) | (join_data["end_date"].isna())]
```

#### ⬛ DATE 타입 변환

##### 1. 숫자인지, date인지 확인
```python
flg_is_serial = kokyaku_daicho["등록일"].astype("str").str.isdigit()
flg_is_serial.sum() # 22건의 숫자 데이터 존재
```

##### 2. 숫자를 날짜로 변환
* `pd.to_timedelta` : 숫자를 날짜로 변환
* `loc` : 숫자인 부분 추출 (1번 참고)
```python
fromSerial = pd.to_timedelta(kokyaku_daicho.loc[flg_is_serial, "등록일"].astype("float"), unit="D") + pd.to_datetime("1900/01/01")
```

##### 3. 날짜 타입으로 변환
```python
fromString = pd.to_datetime(kokyaku_daicho.loc[~flg_is_serial, "등록일"])
```

##### 4. yyyy-mm 단위로 추출
```python
join_data["payment_month"] = join_data["payment_date"].dt.strftime("%Y-%m")
```

4번의 경우 3번과 같이 진행되어야하기 때문에 다음과 같이 붙여서 set라고 생각하면 됩니다.
```python
use_log["usedate"] = pd.to_datetime(use_log["usedate"])
use_log["yyyymm"] = use_log["usedate"].dt.strftime("%Y-%m")
```



```python
```

#### LOOP 활용
```python
# skipna는 NaN의 무시 여부를 설정하여, NaN이 존재할 경우 최소값이 NaN으로 표시됨
for trg in list(uriage["item_name"].sort_values().unique()):
  print(trg + "의 최고가 : " + str(uriage.loc[uriage["item_name"]==trg]["item_price"].max())
            + "의 최저가 : " + str(uriage.loc[uriage["item_name"]==trg]["item_price"].min(skipna=False)))
```
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/ad93b413-0b52-467e-a37d-00b53567da88)

#### ⬛ 시각화
```python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(8,4)) ## 캔버스 생성
ax = fig.add_subplot() ## 그림 뼈대(프레임) 생성
ax.plot(join_data[join_data["item_name"]=="PC-A"].groupby("payment_month").sum()["price"],marker='o',label='A') ## 선그래프 생성
ax.plot(join_data[join_data["item_name"]=="PC-B"].groupby("payment_month").sum()["price"],marker='o',label='B')
ax.plot(join_data[join_data["item_name"]=="PC-C"].groupby("payment_month").sum()["price"],marker='o',label='C')
ax.plot(join_data[join_data["item_name"]=="PC-D"].groupby("payment_month").sum()["price"],marker='o',label='D')
ax.plot(join_data[join_data["item_name"]=="PC-E"].groupby("payment_month").sum()["price"],marker='o',label='E')
ax.legend() ## 범례
plt.title("price sum")
plt.show()
```
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/065fc424-7a7d-4f66-a620-683a307d8712)

```python
%matplotlib inline
plt.plot(list(price_sum_data.index), price_sum_data['price']['PC-A'], label='PC-A')
plt.plot(list(price_sum_data.index), price_sum_data['price']['PC-B'], label='PC-B')
plt.plot(list(price_sum_data.index), price_sum_data['price']['PC-C'], label='PC-C')
plt.plot(list(price_sum_data.index), price_sum_data['price']['PC-D'], label='PC-D')
plt.plot(list(price_sum_data.index), price_sum_data['price']['PC-E'], label='PC-E')
plt.legend()
```
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/688d0ae8-8ec0-43e7-84f8-91262eb65c8b)
