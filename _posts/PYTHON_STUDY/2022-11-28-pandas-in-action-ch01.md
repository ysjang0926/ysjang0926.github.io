---
layout: post
title:  "[판다스 인 액션] 1장 판다스 소개"
subtitle:   "Bakejoon: python 1000"
categories: programming
tags: python_study
comments: true
---

> 🚩 [1장 판다스 소개] 부분 중 중요하다고 생각한 부분만 정리한 포스팅입니다.

## 핵심 요약
* 판다스 : 파이썬 프로그래밍 언어를 기반으로 구축된 데이터 분석용 라이브러리
  * 정렬, 필터링, 정리, 중복 제거, 집계, 피벗 등의 데이터 조작 작업을 위한 도구 모음
* `DataFrame` : 판다스의 기본 자료구조로 여러 열이 있는 테이블
* `Series` : 레이블이 있는 1차원 배열 -> 단일 데이터 열로 생각하면 됨
* 정렬 / 하위 집합 추출 / 결과 그룹에 대한 집계 작업 수행

* 공부한 노트북 링크 : [1장 판다스 소개](https://github.com/ysjang0926/Study_Book/blob/main/Pandas_In_Action/study_chapter_01_introducing_pandas.ipynb)

<br>

### 1. 데이터셋 불러오기

* `read_csv()`
  * csv 파일의 내용을 DataFrame 객체로 가져옴
  * 객체 : 데이터를 저장하는 컨테이너
```python
pd.read_csv("/content/movies.csv")
```

* 인덱스
  * 인덱스 레이블은 데이터 행의 식별자 역할
  * 인덱스에 적합한 열이란? 각 행에 대한 기본 식별자 또는 참조 지점으로 사용할 수 있는 값
```python
movies = pd.read_csv('/content/movies.csv', index_col="Title")
```
![image](https://user-images.githubusercontent.com/54492747/204236663-28ef9e1e-83ae-453e-a052-6401ef15c4d8.png)

<br>

### 2. DataFrame 조작

* 처음 또는 마지막 행 조회
```python
movies.head(5)
```
```python
moveis.tail(5)
```

* 행과 열 개수 확인
```python
# 782개 행 & 4개 변수
movies.shape
```

* 총 셀 갯수
```python
movies.size
```

* 데이터 유형
```python
# int64 : 정수열 / object : 텍스트열
movies.dtypes
```
![image](https://user-images.githubusercontent.com/54492747/204237540-24afa949-fcab-446b-9c42-3aef5ada66bc.png)

* 레이블 접근
  * Series의 인덱스 레이블은 4개의 변수임 -> 기존의 행값을 열로 변경하여 표시함
```python
# 인덱스 위치 사용
movies.iloc[499]

# 인덱스 레이블 사용
movies.loc["Forrest Gump"]
```
![image](https://user-images.githubusercontent.com/54492747/204238195-6f597c13-2382-4b87-a05d-7ec9cc541ead.png)

* `sort_values()`
  * 목표 : 영화 제작사와 개봉일을 알파벳 순서대로 나열한 영화
  * `by` : 정렬할 변수 리스트 기입하기
```python
movies.sort_values(by = ["Studio", "Year"])
```

* `sort_index()`
  * 목표 : 영화 제목을 알파벳 순서대로 보고 싶음
```python
movies.sort_index().head()
```

<br>

### 3. Series의 값 계산

* `value_counts()`
  * 목표 : 가장 많은 수익을 올린 영화가 가장 많은 영화 제작사 찾기
```python
movies["Studio"].value_counts()
```
![image](https://user-images.githubusercontent.com/54492747/204242468-1a40a59e-38ec-486a-aa28-3d3ecde30594.png)

<br>

### 4. 하나 이상의 기준으로 열 필터링

* 한가지 조건 필터링
  * 목표 : 유니버셜 스튜디오에서 제작한 영화만 찾고 싶음
```python
released_by_universal = (movies["Studio"] == "Universal")
movies[released_by_universal]
```

  *  목표 : 1975년 이전에 개봉한 영화
```python
before_1975 = movies["Year"] < 1975
movies[before_1975]
```

* 두가지 조건 필터링
  * 목표 : # Universal에서 제작 or 2015에 개봉
```python
released_by_universal = (movies["Studio"]=="Universal")
released_in_2015 = (movies["Year"]==2015)
movies[released_by_universal | released_in_2015] 
```

  * 목표 : 1983년에서 1986년 사이에 개봉한영화
```python
mid_80s = movies["Year"].between(1983, 1986)
movies[mid_80s]
```

* 문자열 필터링
  * 목표 : 영화 제목을 소문자로 바꾸고 제목에 'dark'라는 단어가 있는 영화
  * `lower()` : 소문자로 바꿈
  * `contains()` : 특정 문자열 추출
```python
has_dark_in_title = movies.index.str.lower().str.contains("dark")
movies[has_dark_in_title]
```

<br>

### 5. 데이터 그룹화

* Gross 열의 값이 숫자가 아닌 텍스트로 저장됨 -> $와 ,를 제거하는 과정이 필요함
  * $와 ,를 모두 빈텍스트로 바꾸기 & 부동 소수점 숫자로 변환
  * `replace(현재문자, 바꿀문자)` : 텍스트 바꾸기
  * `astype(float)` : 부동 소수점 숫자로 변환
```python
movies['Gross'] = (movies['Gross'].str.replace("$", "", regex=False).str.replace(",", "", regex=False).astype(float))
```

* 목적 : 제작사 당 영화 수
  * `count()` : group별 갯수 계산
```python
# movies.groupby("Studio")["Gross"].count().sort_values(ascending=False)
studios = movies.groupby("Studio")
studios["Gross"].count().sort_values(ascending=False)
```

* 목적 : 제작사 당 총 수익
  * `sum()` : group별 합계 계산
```python
studios = movies.groupby("Studio")
studios["Gross"].sum().sort_values(ascending=False)
```

* 목적 : 제작사 당 평균 수익
  * `mean()` : group별 평균 계산
```python
studios["Gross"].mean().sort_values(ascending=False)
```
