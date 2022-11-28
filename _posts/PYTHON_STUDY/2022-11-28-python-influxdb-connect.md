---
layout: post
title:  "InfluxDB 연결 + 데이터 추출"
subtitle:   "InfluxDB Python"
categories: programming
tags: python_study
comments: true
---

> 파이썬에서 InfluxDB에 연결하여 DB에 있는 데이터를 저장하는 방법입니다.

* 참고1 : [InfluxDB와 Python3 연동하기](https://foreverhappiness.tistory.com/62)
* 참고2 : [InfluxDB Python Examples](https://influxdb-python.readthedocs.io/en/latest/examples.html)

## InfluxDB 접속 방법
1. Install influxdb library
```python
pip install influxdb
```
2. Import DataFrameClient module
```python
from influxdb import DataFrameClient  
```
3. Use get_ifdb function
  * InfluxDB에 연결하는 함수(get_ifdb)
```python
def get_ifdb(db, host='<hostname>', port=<port number>, user='<username>', passwd='<password>'):
    # Create an object include information for connect to the InfluxDB
    client = DataFrameClient(host, port, user, passwd, db)
    return client
```

<br>

## DB 데이터 저장
1. DB에서 취득하고 싶은 내용을 작성한 쿼리 저장
2. Use read_sql method
  * `read_sql()`를 사용해 실행하면 DB의 테이블을 Dataframe 형태로 읽어올 수 있음

<br>

------

## 예시
1. `get_ifdb` 함수에 기입해야하는 정보
  * host : IP 주소
  * port : 포트
  * user : 아이디
  * passwd : 비밀번호
  * db : DB명
2. `ifdb.query()` 내 query 기입
  * 불러오고자 하는 내용의 쿼리를 `"""` 안에 작성

```python
from pandas import DataFrame
import pandas as pd
from influxdb import DataFrameClient    

# get_ifdb function (DataFrameClient)
def get_ifdb(db, host='<hostname>', port=<port number>, user='<username>', passwd='<password>'):
    client = DataFrameClient(host, port, user, passwd, db)
    return client

ifdb = get_ifdb(db='pis_cpi')

# Read DataFrame
result = ifdb.query(""" SELECT "<tag name1>", "<tag name2>" FROM "<data name>" WHERE TIME >= '2021-12-05 00:00:00' AND TIME <= '2021-12-05 23:59:59' """)
column = next(iter(result)) # <data name>
output_table = result[column] # load data
data = output_table
data
```

<br>

### 참고 내용
* result
  *  result는 다음과 같이 `ifdb.query()`를 통해 InfluxDB 내 데이터를 들고온 것을 확인할 수 있음 
![image](https://user-images.githubusercontent.com/54492747/204196183-e92cc732-f270-426d-9920-60111c58cee9.png)

* column
  * result는 list형태에 데이터가 담겨있다보니 그것을 추출하는 과정이 필요함
  * `next(iter(result))`를 사용하여 data명을 추출함

* output_table
  * pandas dataframe 형태로 저장됨
  * `result[column]`를 사용하여 data를 추출
