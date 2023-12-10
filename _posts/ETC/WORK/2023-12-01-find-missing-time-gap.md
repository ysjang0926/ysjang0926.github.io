---
layout: post
title:  "[SQL] 시계열 데이터에서 누락된 시간의 범위 구하기"
subtitle:   "Find missing time ranges in time series data"
categories: etc
tags: etc_work
comments: true
---

-   이번 글에서는 시계열 데이터에서 누락된 시간의 범위를 구하는 SQL문을 정리해보았습니다.
-   종종 이런 케이스가 발생하여 누락된 시간 범위를 구하는 일이 생기는데, 그때마다 빠르게 일처리를 진행해야하는 제자신을 돕기 위해 이 글을 바칩니다. (For Me😂)
-   간단한 쿼리지만 장황(?)하게 써보았으니, '제조 데이터는 이런 상황도 생기는구나!'하고 가볍게 참고만 해주시면 감사하겠습니다.

---------

현재 저희 회사는 실시간으로 센서(또는 태그) 데이터를 대표적인 TSDB(Time Series Database)인 📈**InfluxDB**에 적재하고 있습니다.
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/c7fee92d-facb-4f9d-94a2-0de708099a2e)


InfluxDB를 간략하게 설명하면 다음과 같습니다.
* **Time Series Data를 효율적으로 저장하고 조회할 수 있는 데이터베이스**
	* 데이터 적재와 조회 성능이 우수함
	* 센서, 로그 데이터와 같은 시계열 데이터를 다루는 환경에 용이
* **다른 TSDB에 비해 자료와 플러그인이 풍부**
	* 풍부한 플러그인 생태계를 가지고 있어, 다양한 기능과 연동이 쉬움
* **데이터가 축적됨에 따라 최적화되도록 설계**
	* 시간에 따라 파티셔닝하여 저장하기 때문에 입력 부하를 줄일 수 있음

<br>

이때 실시간으로 DB에 쌓이는 데이터를 통해 분석을 진행해야 하는데, 초당 수천 수만건의 데이터가 발생하는 환경에서 분석한다는 것은 큰 오버헤드를 발생시킬 수 있습니다. 또한 오버헤드가 발생하여 장애가 발생했을 경우 데이터가 유실될 가능성이 높습니다. 

<br>

실제 분석을 진행할 때에는 실시간으로 데이터가 적재되는 DB에서 데이터를 추출하여 분석하면 안되겠죠? 이러한 이유로 따로 분석용 DB를 따로 마련하여, 1초 단위로 쌓이는 데이터를 순시값으로 들고 와서 1분 데이터로 집계되도록 되어 있습니다.
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/fed01e76-7b0a-4a1a-b9df-ed762b4db171)


> **순시값**은 주로 시계열 데이터나 센서 데이터에서 사용되는 용어이며, 시간에 따라 측정되거나 기록된 데이터 중에서 순차적으로 발생한 값 또는 순서를 나타냅니다.
> 여기에서 순시값은 순간의 시간값으로, **t 순간의 센서(태그)값**을 의미합니다.![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/48da02ea-1d95-4382-9239-5ba704e67391)
> (사진 출처: [측정값과 각 로깅 조건 작동 설명](https://gastec-soft.com/anasys/korea/ghs8at_measure_condition_k.html))

<br>

이때 1분 데이터로 집계될 때 일부 시간대 데이터가 누락되는 케이스가 종종 있는데요. <br>
1~2분 간격으로 간헐적으로 존재한다면 이를 무시하여 분석할 수도 있지만, 기기 이상 등 여러 요소들로 인해 10분 이상의 데이터가 누락된 케이스도 존재하게 됩니다.  <br>
이때 1분 집계 데이터에서 **이력이 끊기는 일자 및 시간대 집합**을 만들어서, **해당 범위의 1초 데이터를 들고 오려고 합니다.** (1초 데이터를 들고 온 뒤, 1분 데이터로 변환 예정)<br>

<br>
<br>

## 단절된 시간대의 구간별 min, max를 구하는 방법
(예시)
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/7b841dfe-7758-41db-a174-7b9e55898176)

<br>

## 📍 목표
시계열 데이터에서 단절된 시간대가 존재하며,
테이블에서 각 **단절된 시간대의 MIN(시작시간), MAX(종료시간)**을 구하는 효율적인 방법 찾기

<br>

## 🔑 Key Idea
시간(1분 단위)이 끊기는 날짜 및 시간 집합(기준값) 만들기

<br>

## ⏩ 단절된 시간대의 구간별 min, max를 구하는 과정
크게 3가지 스텝으로 이루어지며 다음과 같습니다.
* **STEP 1) 기준 만들기**
	* `LAG` 함수를 통해, 이전의 위치에 있는 시간대 데이터를 가져오기 (LAG_DATE 변수)
	* 두 개의 시간을 빼서 시간 차이값 산출 (BEFORE_TIME_DIFF 변수)
* **STEP 2) GAP이 있는 시작, 종료 시간대 구하기**
	* 시간 차이값이 1분 초과일 때(=2분 이상) 추출 (BEFORE_TIME_DIFF 변수)
* **STEP 3)  단절된 시간대의 구간별 min, max 산출**
	* LAG_DATE와 DATE 사이 구간이 데이터가 없기 때문에, 해당 구간을 단절된 시간으로 처리하기
	* LAG_DATE에서 1분을 더하여, 단절된 시간대 min값 산출 (MISSING_START_TIME 변수)
	* DATE에서 1초를 빼서, 단절된 시간대 max값 산출 (MISSING_END_TIME 변수) 

![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/a1d4e6c8-5573-4f0a-8956-0544fef0528e)

<br>

## 💻 SQL문
각 스텝별로 SQL문을 보며, 일부 내용에 대한 부가 설명을 적어보도록 하겠습니다. <br>
이때 `MY_SCHEMA`와 `MY_TABLE`에 적절한 스키마와 테이블 이름을 사용하여 쿼리를 수정하시면 됩니다. <br>

### 1️⃣ STEP 1) 기준 만들기
먼저 1분 단위로 순차적으로 존재하는 시계열 데이터에서 누락 값이 있었는지 확인하는 것이 필요합니다. <br>
그렇기 위해서는 **시간 GAP 발생 기준**을 산출하여, **어떤 시간 구간을 추출하면 될지** 확인하면 되겠죠? <br>
다양한 방법으로 구할 수 있겠지만, 저는 `LAG`함수를 이용하여 시간 GAP 발생 기준을 구하였습니다.
> **Analytic Function**
> * Business 분야에서 자주 행하여지는 여러가지 형태의 분석에 유용하게 활용될 수 있는 SQL Function
> * 대량의 Data에서 Analytic Function을 여러 개 사용할 경우 Performance 저하
> * 장점 : 개발 작업의 효율화, SQL문의 속도의 향상

> **LAG Function**
> * Analytic Function 중 하나로 서로 다른 두 Row 값을 비교하기 위한 Function
> *  주어진 offset만큼 이전의 위치에 있는 데이터를 가져옴
> * 다음의 위치에 있는 데이터를 가져오기 위해서는 **LEAD Function**를 쓰면 됨

1. `LAG_DATE` 변수 : 이전의 위치에 있는 시간대 데이터 가져오기
	* LAG Function 사용
2. `BEFORE_TIME_DIFF` 변수 : 두 개의 시간을 빼서 시간 차이값 산출
	* SECONDS_BETWEEN Function 사용
	* second 단위로 산출하기 때문에, minute 단위 데이터를 산출하기 위해 60을 나누어줌 (60초=1분) <br>

```sql
SELECT	 LAG(DATE) OVER(ORDER BY DATE) AS LAG_DATE
		,DATE
		,SECONDS_BETWEEN(LAG(DATE) OVER(ORDER BY DATE), DATE)/60 AS BEFORE_TIME_DIFF
FROM MY_SCHEMA.MY_TABLE
```
(결과 - 예시표)
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/e4a1ed33-01b4-4cf1-b0f5-a969b1160b45)

<br>

### 2️⃣ STEP 2) GAP이 있는 시작, 종료 시간대 구하기
LAG Function으로 구한 `BEFORE_TIME_DIFF` 변수는 **현재 시간대(DATE)와 이전 시간대(LAG_DATE)의 시간 차이값**입니다. <br>
시간대 누락이 없을 경우에는 시간 차이값은 무조건 1로 나오게 되므로, **시간 차이값이 1분 초과일 때(=2분 이상)는 시간대 누락이 발생**했음을 확인할 수 있습니다. <br>
WHERE문을 통해 BEFORE_TIME_DIFF 값이 1분 초과일 때에 대한 GAP 존재 시간을 추출합니다.<br>

```sql
SELECT	 LAG_DATE, DATE, BEFORE_TIME_DIFF
FROM (
		SELECT	 LAG(DATE) OVER(ORDER BY DATE) AS LAG_DATE
				,DATE
				,SECONDS_BETWEEN(LAG(DATE) OVER(ORDER BY DATE), DATE)/60 AS BEFORE_TIME_DIFF
		FROM MY_SCHEMA.MY_TABLE
	)
WHERE BEFORE_TIME_DIFF > 1
```
(결과 - 예시표)
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/502399a6-b58c-4864-9f90-89bb2b84d16f)

<br>

### 3️⃣ STEP 3)  단절된 시간대의 구간별 min, max 산출
STEP 2를 통해 추출된 GAP 존재 시간은, 현재 시간대(DATE)와 이전 시간대(LAG_DATE) 사이 구간에 데이터가 존재하지 않는 것을 의미합니다. <br>
그렇기 때문에 해당 구간을 **이력이 끊긴 단절된 시간**으로 처리하여, 해당 범위의 1초 데이터를 들고 오는 작업을 진행해야 합니다. <br>
실제 값이 누락된 시간대를 추출해야 하기 때문에, `LAG_DATE` 변수와 `DATE` 변수에서 단절된 시간대의 min값과 max값을 산출합니다. <br>
(즉, STEP 2까지 산출된 시간대의 사이값이 단절된 시간대이기 때문에 추가적인 산출 과정 필요)
1. `MISSING_START_TIME` 변수 : `LAG_DATE` 변수에서 1분을 더하여, 단절된 시간대 min값 산출
2. `MISSING_END_TIME` 변수 : `DATE` 변수에서 1초를 빼서, 단절된 시간대 max값 산출 <br>

```sql
SELECT	 LAG_DATE, DATE, BEFORE_TIME_DIFF
		,ADD_SECONDS(LAG_DATE, 60) AS MISSING_START_TIME
		,ADD_SECONDS(DATE, -1) AS MISSING_END_TIME
FROM (
		SELECT	 LAG(DATE) OVER(ORDER BY DATE) AS LAG_DATE
				,DATE
				,SECONDS_BETWEEN(LAG(DATE) OVER(ORDER BY DATE), DATE)/60 AS BEFORE_TIME_DIFF
		FROM MY_SCHEMA.MY_TABLE
	)
WHERE BEFORE_TIME_DIFF > 1
```
(결과 - 예시표)
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/c431db34-3bc6-4dd7-9d59-a12ddd731d73)


<br>

### 🔚 최종 SQL문
최종 SQL문은 다음과 같으며,
여기서 산출된 `MISSING_START_TIME` 변수와 `MISSING_END_TIME` 변수의 데이터 기간을 1초 Influx DB에서 Data I/F 작업을 진행하면 됩니다.
```sql
SELECT	 LAG_DATE, DATE
		,BEFORE_TIME_DIFF
		,BEFORE_TIME_DIFF - 1 AS REAL_TIME_DIFF
		,ADD_SECONDS(LAG_DATE, 60) AS MISSING_START_TIME
		,ADD_SECONDS(DATE, -1) AS MISSING_END_TIME
FROM (
		SELECT	 LAG(DATE) OVER(ORDER BY DATE) AS LAG_DATE
				,DATE
				,SECONDS_BETWEEN(LAG(DATE) OVER(ORDER BY DATE), DATE)/60 AS BEFORE_TIME_DIFF
		FROM MY_SCHEMA.MY_TABLE
	)
WHERE BEFORE_TIME_DIFF > 1
ORDER BY DATE
```


<br>

### Reference
*  [[22년] InfluxDB를 활용한 웹 모니터링 시스템 구축](https://hello-jaemin.tistory.com/171)

----------------------------

### 🔜 Think
업무를 진행하면서, 생각보다 자주 쓰이는 SQL문이 많은데요! <br>
최근에 2020년부터 2023년까지 모든 기간 데이터를 적재하여 분석 데이터셋을 만들 일이 있었는데, 생각보다 데이터 누락 기간이 있는 것을 발견하였습니다.😂 <br>
간단한 SQL문이지만 이런 케이스도 있는걸 소개하면 좋을 것 같아서 몇자 끄적여보았습니다. <br>
다음 포스팅은 관심 있게 보고 있는 논문 리뷰로 진행할 예정인데, 종종 이런 케이스에 맞는 SQL문을 소개해보도록 하겠습니다. <br>
오늘도 재밌게 읽어주셔서 감사합니다🙏
