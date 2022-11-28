---
layout: post
title:  "HANA DB 연결 + 데이터 추출"
subtitle:   "Bakejoon: python 1000"
categories: programming
tags: python_study
comments: true
---

> 파이썬에서 HANA DB에 연결하여 DB에 있는 데이터를 저장하는 방법입니다.

* 참고 : [Connect to SAP HANA from Python](https://help.sap.com/docs/HANA_SERVICE_CF/1efad1691c1f496b8b580064a6536c2d/d12c86af7cb442d1b9f8520e2aba7758.html?locale=en-US)

## HANA DB 접속 방법
1. Python Driver 설치
2. Import dbapi module
```python
from hdbcli import dbapi
```
3. Use connect method
  * `connect()`에 DB 접속정보를 넣으면 됨
```python
dbapi.connect(address='localhost', port=30015, user='system', password='manager')
```

<br>

## DB 데이터 저장
1. DB에서 취득하고 싶은 내용을 작성한 쿼리 저장
2. Use read_sql method
  * `read_sql()`를 사용해 실행하면 DB의 테이블을 Dataframe 형태로 읽어올 수 있음

<br>
------

## 예시
1. `dbapi.connect()`에 기입해야하는 정보
  * address : IP 주소
  * port : 포트
  * user : 아이디
  * password : 비밀번호
2. query 저장
  * 불러오고자 하는 내용의 쿼리를 `"""` 안에 작성
3. `read_sql()`
  *  [DataFrame이름] = pd.read_sql("[SQL구문]", [Connection객체], [index컬럼지정])
  *  index 컬럼이 None이면, 디폴트로 0부터 시작하는 정숫값

```python
from hdbcli import dbapi

# connect method
connection = dbapi.connect(
    address = "<hostname>", 
    port = <port number>, 
    user = "<username>", 
    password = "<password>"
)

# query
query = """ SELECT * FROM <data name> """

# read_sql 
df = pd.read_sql(query, con = connection)
```
