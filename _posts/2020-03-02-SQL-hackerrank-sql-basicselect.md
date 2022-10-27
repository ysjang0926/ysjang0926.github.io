---
layout: post
title:  "[HackerRank] SQL Basic Select"
subtitle:   "HackerRank: SQL Basic Select"
categories: programming
tags: sql_hackerrank
comments: true
---

SQL 퀴즈 중 제일 간단한 파트인 **Basic Select**이다.
맛보기 용으로 뚝딱하고 다음 레벨 퀴즈 도전하는 걸로 고고!

<br>
    
###  <font color = "#EFC050"> Revising the Select Query 1 </font>    
     
[문제 링크](https://www.hackerrank.com/challenges/revising-the-select-query/problem) <br>
CITY 테이블에서 인구(Population)가 100,000보다 많은 모든 미국 도시 데이터들을 조회. 미국의 국가코드(CountryCode)는 'USA'.
```sql  
SELECT*
FROM CITY
WHERE POPULATION>=100000 AND COUNTRYCODE='USA'
```

---

###  <font color = "#EFC050"> Revising the Select Query 2 </font>    
   
[문제 링크](https://www.hackerrank.com/challenges/revising-the-select-query-2/problem)  <br>
CITY 테이블에서 인구(Population)가 120,000보다 많은 모든 미국 도시 이름(Name)들을 조회. 미국의 국가코드(CountryCode)는 'USA'.
       
```sql  
SELECT NAME
FROM CITY
WHERE POPULATION>=120000 AND COUNTRYCODE='USA'
```

---

###  <font color = "#EFC050"> Select All </font>    
  
[문제 링크](https://www.hackerrank.com/challenges/select-all-sql/problem) <br>
CITY 테이블에서 모든 행에 대한 모든 열(속성)을 조회.

```sql  
SELECT *
FROM CITY
```
---

###  <font color = "#EFC050"> Select By ID</font>    
  
[문제 링크](https://www.hackerrank.com/challenges/select-by-id/problem) <br>
CITY 테이블에서 ID가 1661인  도시의 모든 열을 조회.

```sql  
SELECT *
FROM CITY
WHERE ID=1661
```
---

###  <font color = "#EFC050"> Japanese Cities' Attributes</font>    
  
[문제 링크](https://www.hackerrank.com/challenges/japanese-cities-attributes/problem) <br>
CITY 테이블에서 모든 일본 도시의 속성을 조회. 일본의 국가코드(CountryCode)는 'JPN'.

```sql  
SELECT *
FROM CITY
WHERE COUNTRYCODE='JPN'
```
---

###  <font color = "#EFC050"> Japanese Cities' Names</font>    
  
[문제 링크](https://www.hackerrank.com/challenges/japanese-cities-name/problem) <br>
CITY 테이블에서 모든 일본 도시 이름(Name)들을 조회. 일본의 국가코드(CountryCode)는 'JPN'.

```sql  
SELECT NAME
FROM CITY
WHERE COUNTRYCODE='JPN'
```
---

###  <font color = "#EFC050"> Weather Observation Station1 </font>    
  
[문제 링크](https://www.hackerrank.com/challenges/weather-observation-station-1/problem) <br>
STATION 테이블에서 CITY와 STATE 목록을 조회.

```sql  
SELECT CITY, STATE
FROM STATION 
``` 
---

###  <font color = "#EFC050"> Weather Observation Station2 </font>    
  
[문제 링크](https://www.hackerrank.com/challenges/weather-observation-station-2/problem) <br>
STATION 테이블에서 CITY와 STATE를 조회.

```sql  
SELECT CITY, STATE
FROM STATION 
``` 
---

###  <font color = "#EFC050"> Weather Observation Station3 </font>    
  
[문제 링크](https://www.hackerrank.com/challenges/weather-observation-station-3/problem) <br>
STATION 테이블에서 ID값이 짝수인 CITY 이름(Name)을 조회 (단, 중복된 값은 제외) 
* `DISTINCT` : 지정한 열의 데이터가 중복될 경우 중복된 값을 제거하고 한개씩만 반환
* `MOD(m,n)` : m을 n으로 나누었을 때 나머지를 반환

```sql  
SELECT DISTINCT CITY
FROM STATION
WHERE MOD(ID,2)=0
```
---

###  <font color = "#EFC050"> Weather Observation Station4 </font>    
  
[문제 링크](https://www.hackerrank.com/challenges/weather-observation-station-4/problem)  <br>
STATION 테이블에서 CITY 이름의 총 개수와 중복된 CITY 이름을 제외한 개수의 차이값을 조회. 

```sql  
SELECT COUNT(CITY)-COUNT(DISTINCT CITY) AS DIFF
FROM STATION
```  

---

###  <font color = "#EFC050"> Weather Observation Station5 </font>    
    
[문제 링크](https://www.hackerrank.com/challenges/weather-observation-station-5/problem)  <br>
STATION 테이블에서 가장 짧은 CITY 이름과 가장 긴 CITY 이름과 각각의 길이(ex. 이름의 문자수)를 조회.
만약 가장 짧거나 긴 이름을 가진 도시가 두 개 이상인 경우 알파벳 순으로 선택했을 때 가장 먼저 나오는 것으로 선택.
* 이 문제는 나름 헤맸다...
* HANA SQL은 `TOP 1`을 통해 첫 번째 행을 추출할 수 있는데 MySQL은 `LIMIT 1`을 사용해야한다.
* ORDER BY에서 `DESC`는 내림차순(큰 값부터 작은 값 쪽으로의 순서)이고 `ASC`는 오름차순(작은 값부터 큰 값 쪽으로의 순서)이다.

<U>(1) 가장 긴 CITY 이름과 길이</U>
```sql
SELECT CITY, LENGTH(CITY) AS LEN
FROM STATION
ORDER BY LENGTH(CITY) DESC, CITY ASC
LIMIT 1
```

<U>(2) 가장 짧은 CITY 이름과 길이</U>
```sql
SELECT CITY, LENGTH(CITY) AS LEN
FROM STATION
ORDER BY LENGTH(CITY), CITY ASC
LIMIT 1
``` 

---

###  <font color = "#EFC050"> Weather Observation Station6 </font>    
   
[문제 링크](https://www.hackerrank.com/challenges/weather-observation-station-6/problem)   <br>
STATION 테이블에서 CITY 이름이 모음(i.e. a, e, i, o, u)으로 시작하는 것만 조회.  
중복없이 출력할 것.  

 <U>SOL1) SUBSTR 답안 </U>
* `SUBSTR("문자열","시작위치","길이")` : 문자단위로 시작위치와 자를 길이를 지정하여 문자열을 자름

```sql   
SELECT DISTINCT CITY 
FROM STATION
WHERE SUBSTR(CITY, 1, 1) IN ('A', 'E', 'I', 'O', 'U')
```  

<U>SOL2) LIKE 답안 </U>   
* `WHERE [컬럼명] LIKE [조건]`  : 쿼리문 WHERE절에 주로 사용되며 부분적으로 일치하는 칼럼을 찾을때 사용
* [조건]에 들어가는 것은 아래와 같다.
	* `-` : 글자 숫자를 정해줌 (ex. 컬럼명 LIKE '홍_동')
	* `%` : 글자 숫자를 정해주지 않음 (ex. 컬럼명 LIKE '홍%') 
	* 예시 참고
		* A로 시작하는 문자 찾기 :  'A%'
		* A로 끝나는 문자 찾기 : '%A'
		* A를 포함하는 문자 찾기 : '%A%' 
		* A로 시작하는 두글자 문자 찾기 : 'A_'
		* 첫번째 문자가 'A'가 아닌 모든 문자열 찾기 : '[^A]'    

```sql   
SELECT DISTINCT CITY 
FROM STATION
WHERE CITY LIKE 'A%' OR CITY LIKE 'E%' OR # A% = A로 시작하는 문자를 찾기
        CITY LIKE 'I%' OR CITY LIKE 'O%' OR CITY LIKE 'U%';
```  

<U>SOL3) REGEXP 답안  </U>
* `REGEXP` 연산자 : 정규 표현식을 토대로 하는 패턴 매칭 연산을 제공
	* [연산자 패턴](http://tcpschool.com/mysql/mysql_operator_patternMatching)

```sql  
SELECT DISTINCT CITY
FROM STATION
WHERE CITY REGEXP '^[aeiou]'
```     
---

### <font color = "#EFC050"> Weather Observation Station7 </font>    
   
[문제 링크](https://www.hackerrank.com/challenges/weather-observation-station-7/problem)  <br>
STATION 테이블에서 CITY 이름이 모음(i.e. a, e, i, o, u)으로 끝나는 것만 조회. 중복없이 출력할 것. 

<U>SOL1) SUBSTR 답안 </U>
```sql
SELECT DISTINCT CITY 
FROM STATION
WHERE SUBSTR(CITY, -1, 1) IN ('A', 'E', 'I', 'O', 'U')
``` 

<U>SOL2) REGEXP 답안 </U>
```sql   
SELECT DISTINCT CITY FROM STATION
WHERE CITY REGEXP '[aeiou]$'
```   
---

### <font color = "#EFC050"> Weather Observation Station8 </font>    
   
[문제 링크](https://www.hackerrank.com/challenges/weather-observation-station-8/problem)  <br>
STATION 테이블에서 CITY 이름이 모음(i.e. a, e, i, o, u)으로 시작하고 끝나는 것을 조회. 중복없이 출력할 것. 

<U>SOL1) SUBSTR 답안 </U>
```sql
SELECT DISTINCT CITY 
FROM STATION
WHERE SUBSTR(CITY, -1, 1) IN ('A', 'E', 'I', 'O', 'U') AND SUBSTR(CITY, 1, 1) IN ('A', 'E', 'I', 'O', 'U')
``` 

<U>SOL2) REGEXP 답안 </U>      
```sql   
SELECT DISTINCT CITY
FROM STATION
WHERE CITY REGEXP '^[aeiou]' AND CITY REGEXP '[aeiou]$'
```   

---

###  <font color = "#EFC050"> Weather Observation Station9 </font>    

[문제 링크](https://www.hackerrank.com/challenges/weather-observation-station-9/problem)  <br> 
STATION 테이블에서 CITY 이름이 모음(i.e. a, e, i, o, u)으로 시작하지 않는 것만 조회. 중복없이 출력할 것.  

<U>SOL1) SUBSTR 답안 </U>
```sql   
SELECT DISTINCT CITY 
FROM STATION
WHERE SUBSTR(CITY, 1, 1) NOT IN ('A', 'E', 'I', 'O', 'U')
```

<U>SOL2) LIKE 답안 </U>   
```sql   
SELECT DISTINCT CITY 
FROM STATION
WHERE NOT (CITY LIKE 'A%' OR CITY LIKE 'E%' OR
           CITY LIKE 'I%' OR CITY LIKE 'O%' OR CITY LIKE 'U%')
```  

<U>SOL3) REGEXP 답안  </U>
```sql  
SELECT DISTINCT CITY FROM STATION
WHERE CITY NOT REGEXP '^[aeiou]'
```  

---

### <font color = "#EFC050"> Weather Observation Station10 </font>    
   
[문제 링크](https://www.hackerrank.com/challenges/weather-observation-station-10/problem)  <br>
STATION 테이블에서 CITY 이름이 모음(i.e. a, e, i, o, u)으로 끝나지 않는 것만 조회. 중복없이 출력할 것. 

<U>SOL1) SUBSTR 답안 </U>
```sql
SELECT DISTINCT CITY 
FROM STATION
WHERE SUBSTR(CITY, -1, 1) NOT IN ('A', 'E', 'I', 'O', 'U')
```

<U>SOL2) REGEXP 답안 </U>
```sql   
SELECT DISTINCT CITY FROM STATION
WHERE CITY NOT REGEXP '[aeiou]$'
```   

----

###  <font color = "#EFC050"> Weather Observation Station11 </font>    
   
[문제 링크](https://www.hackerrank.com/challenges/weather-observation-station-11/problem)  <br>
STATION 테이블에서 CITY 이름이 모음(i.e. a, e, i, o, u)으로 시작하지 않거나 끝나지 않는 것을 조회. 중복없이 출력할 것. 

<U>SOL1) SUBSTR 답안 </U>
```sql
SELECT DISTINCT CITY 
FROM STATION
WHERE SUBSTR(CITY, -1, 1) NOT IN ('A', 'E', 'I', 'O', 'U') OR SUBSTR(CITY, 1, 1) NOT IN ('A', 'E', 'I', 'O', 'U')
``` 

<U>SOL2) REGEXP 답안 </U>      
```sql   
SELECT DISTINCT CITY
FROM STATION
WHERE CITY NOT REGEXP '^[aeiou]' OR CITY NOT REGEXP '[aeiou]$'
```   

---

###  <font color = "#EFC050"> Weather Observation Station12 </font>    
   
[문제 링크](https://www.hackerrank.com/challenges/weather-observation-station-12/problem)  <br>
STATION 테이블에서 CITY 이름이 모음(i.e. a, e, i, o, u)으로 시작하지 않고 끝나지 않는 것을 조회. 중복없이 출력할 것. 

<U>SOL1) SUBSTR 답안 </U>
```sql
SELECT DISTINCT CITY 
FROM STATION
WHERE SUBSTR(CITY, -1, 1) NOT IN ('A', 'E', 'I', 'O', 'U') AND SUBSTR(CITY, 1, 1) NOT IN ('A', 'E', 'I', 'O', 'U')
``` 

<U>SOL2) REGEXP 답안 </U>      
```sql   
SELECT DISTINCT CITY
FROM STATION
WHERE CITY NOT REGEXP '^[aeiou]' AND CITY NOT REGEXP '[aeiou]$'
```    

---

###  <font color = "#EFC050"> Higher Than 75 Marks </font>    
   
[문제 링크](https://www.hackerrank.com/challenges/more-than-75-marks/problem)   <br>
STUDNETS 테이블에서 점수(MARKS)가 75점보다 큰 학생들의 이름을 조회.
이름(NAME)에서 마지막 3문자를 기준으로 정렬하고 만약 마지막 3문자가 같은 학생이 두 명 이상 있으면(i.e.: Bobby, Robby, etc.) ID를 기준으로 증가하는 순으로 정렬.

```sql  
SELECT NAME
FROM STUDENTS
WHERE MARKS>75
ORDER BY SUBSTR(NAME,-3) ASC, ID ASC
```

---

###  <font color = "#EFC050"> Employee Names </font>    
  
[문제 링크](https://www.hackerrank.com/challenges/name-of-employees/problem)     <br>   
EMPLOYEE 테이블에서 모든 직원들의 이름을 알파벳 순서대로(A→Z) 조회.  

```sql    
SELECT NAME
FROM EMPLOYEE
ORDER BY NAME
```

---

###  <font color = "#EFC050"> Employee Salaries </font>     
   
[문제 링크](https://www.hackerrank.com/challenges/salary-of-employees/problem)   <br>
EMPLOYEE 테이블에서 month 값이 10(달)보다 작으면서 급여(SALARY )가 2,000$보다 큰 모든 직원들의 이름을 조회. EMPLOYEE_ID 기준 오름차순으로 정렬.  
```sql  
SELECT NAME
FROM EMPLOYEE
WHERE SALARY > 2000 AND MONTHS < 10
ORDER BY EMPLOYEE_ID
```  
