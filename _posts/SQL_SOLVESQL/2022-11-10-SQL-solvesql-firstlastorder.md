---
layout: post
title:  "[solvesql] 첫 주문과 마지막 주문"
subtitle:   "solvesql : First Order & Last Order"
categories: programming
tags: sql_solvesql
comments: true
use_math: true
---

## 문제 출처

###  <font color = "#EFC050"> 첫 주문과 마지막 주문 </font>    
     
[문제 링크](https://solvesql.com/problems/first-and-last-orders/) <br>
olist_orders_dataset 테이블에서 **첫 주문 일자**와 **마지막 주문 일자**를 조회하는 쿼리를 작성하면 된다.

-------

## 문제 풀이

쿼리문은 '이것이 정답이다!'라고 하는 것은 없다고 생각한다. 그래서 다양한 방법으로 결과를 추출할 수 있기 때문에, 가능한 쿼리문을 몇개 적어보았다.

### 1번 - MySQL
```sql  
select left(min(order_purchase_timestamp),10) as first_order_date
      ,left(max(order_purchase_timestamp),10) as last_order_date
from olist_orders_dataset
```

### 2번 - MySQL, SQLite
```sql  
select date(min(order_purchase_timestamp)) as first_order_date
      ,date(max(order_purchase_timestamp)) as last_order_date
from olist_orders_dataset
```

### 3번 - SQLite
```sql
select strftime('%Y-%m-%d',min(order_purchase_timestamp)) as first_order_date
      ,strftime('%Y-%m-%d',max(order_purchase_timestamp)) as last_order_date
from olist_orders_dataset
```

<br>

`strftime()` 날짜 함수는 이번에 처음 알게 되었는데, 지정된 형식에 따라 datatime값을 형식화하는데 사용된다.

#### Format Description
 %d | day of the month: 01-31        
----|--------------------------------
 %f | fractional seconds: SS.SSS     
 %H | hour: 00-24                    
 %j | day of the year: 001-366       
 %J | Julian day number              
 %m | month: 01-12                   
 %M | minute: 00-59                  
 %s | seconds since 1970-01-01       
 %S | seconds: 00-59                 
 %w | day of week 0-6 with Sunday==0 
 %W | week of the year: 00-53        
 %Y | year: 0000-9999                
 %% | %                              

Format이 위와 같기 때문에 datetime 변수를 년-월-일로 하기 위해 다음과 같이 사용하였다. <br>
```sql
strftime('%Y-%m-%d', datetime변수명)
```
