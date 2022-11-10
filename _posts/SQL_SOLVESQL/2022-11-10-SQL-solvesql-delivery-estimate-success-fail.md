---
layout: post
title:  "[solvesql] 배송 예정일 예측 성공과 실패"
subtitle:   "solvesql : Delivery Estimate success&fail"
categories: programming
tags: sql_solvesql
comments: true
use_math: true
---

## 문제 출처

###  <font color = "#EFC050"> 배송 예정일 예측 성공과 실패 </font>    
     
[문제 링크]([https://solvesql.com/problems/first-and-last-orders/](https://solvesql.com/problems/estimated-delivery-date/)) <br>

olist_orders_dataset 테이블에서 **2017년 1월 한 달 동안 발생한 주문의 배송 예측이 정확했는지에 대한 여부를 집계**하는 쿼리를 작성하면 된다.
* 고객의 구매 일자별로 배송 예정 시각 안에 고객에게 도착한 주문과, 배송 예정 시각이 지나서 고객에게 도착한 주문을 각각 집계해야함
* 배송 완료 또는 배송 예정 시각 데이터가 없는 경우는 계산에서 제외해야함
* 계산 결과는 구매 날짜를 기준으로 오름차순 정렬되어야함

-------

## 문제 풀이

일자를 추출하는 함수는 여러개가 있지만, 이번에는 `date()`함수와 `strftime()`함수로 쿼리문을 작성하였다.
* where : olist_orders_dataset 테이블에서 2017년 1월 데이터만 추출한다.
* is not null : 배송 완료 또는 배송 예정 시각 데이터가 없는 경우를 제외한다.
* group by : order_purchase_timestamp 칼럼에서 yyyy-mm-dd 형식으로 구성하여 일자별(구매 날짜) 값이 나오도록 한다.
* case when then : 각 구매 일자별로 order_delivered_customer_date가 order_estimated_delivery_date보다 작거나 큰 경우를 나눈다.
* order by : 구매 일자 기준으로 오름차순 정렬한다.

### 1번 - MySQL
```sql
select date(order_purchase_timestamp) as purchase_date
      ,count(case when order_delivered_customer_date < order_estimated_delivery_date then order_id end) as success
      ,count(case when order_delivered_customer_date > order_estimated_delivery_date then order_id end) as fail
from olist_orders_dataset
where year(order_purchase_timestamp) = '2017' and month(order_purchase_timestamp) = '01' and order_delivered_customer_date IS NOT NULL and order_estimated_delivery_date IS NOT NULL
group by date(order_purchase_timestamp)
order by date(order_purchase_timestamp)
```

### 2번 - SQLite
```sql
select strftime('%Y-%m-%d',order_purchase_timestamp) as purchase_date
      ,count(case when order_delivered_customer_date < order_estimated_delivery_date then order_id end) as success
      ,count(case when order_delivered_customer_date > order_estimated_delivery_date then order_id end) as fail
from olist_orders_dataset
where strftime('%Y%m',order_purchase_timestamp) = '201701' and order_delivered_customer_date IS NOT NULL and order_estimated_delivery_date IS NOT NULL
group by strftime('%Y-%m-%d',order_purchase_timestamp)
order by strftime('%Y-%m-%d',order_purchase_timestamp)
```

<br>

회사 와서 필요한 경우에 그때마다 쿼리문을 작성하다보니, 이렇게 나름 간단한 쿼리도 고민해서 작성한 나를 보며 다시금 반성을 느낀다. <br>
`count()`함수와 `case when then`을 함께 사용하여 집계할 수 있는 이 쿼리는 다른 데이터를 전처리할 때도 유용하게 쓰일 것 같다.
