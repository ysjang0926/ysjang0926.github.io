---
layout: post
title:  "[HackerRank] SQL Advanced Select - Type of Triangle (CASE WHEN 순서 주의)"
subtitle:   "HackerRank: SQL Advanced Select - Type of Triangle"
categories: programming
tags: sql
comments: true
---

SQL 퀴즈 중 **Advanced Select**파트의 첫 문제인 'Type of Triangle'이다.
이번 파트도 쉬울 줄 알았는데 나름 생각을 하고 퀴즈를 풀어야 했다. 문제 풀다가 얻은 삽질 과정도 모두 적어보았다.

---------
###  <font color = "#EFC050"> Type of Triangle </font>    
     
[문제 링크](https://www.hackerrank.com/challenges/what-type-of-triangle/problem) <br>
TRIANGLES 테이블에서 세 변의 길이를 나타내는 칼럼을 사용하여 삼각형의 유형을 파악하는 쿼리를 작성. 삼각형의 유형은 아래와 같음.
* Equilateral : 세 변의 길이가 모두 같은 삼각형
* Isosceles : 두 변의 길이가 같은 삼각형
* Scalene : 세 변의 길이가 다른 삼각형
* Not A Triangle : 세 변의 길이가 삼각형을 형성하지 않음

TRIANGLES 테이블은 삼각형의 세 변의 길이를 나타내는 A, B, C 변수를 가지고 있으며, 테이블 형태의 예시는 아래와 같다.<br>
![sample output](https://user-images.githubusercontent.com/54492747/76062973-18a94380-5fca-11ea-96f2-50125bad46f0.png){: .align-center}


예시 테이블을 기준으로 삼각형의 유형은 다음과 같다.
* (20, 20, 23)의 삼각형 유형은 'Isosceles'  → $A=B$
* (20,20,20)의 삼각형 유형은 'Equilateral' → $A=B=C$
* (20,21,22)의 삼각형 유형은 'Scalene' → $A\neq B\neq C$
* (13,14,30)의 삼각형 유형은 'Not A Triangle'→ $A+B<=C$

-------
<br>

'뭐야! 껌이네!'하고 아래의 코드로 돌려봤지만 Error가 떠서 당황했다.
```sql  
SELECT CASE WHEN A = B AND B = C THEN 'Equilateral'
            WHEN A = B OR B = C OR A = C THEN 'Isosceles'
            WHEN A + B <= C THEN 'Not A Triangle'
            ELSE 'Scalene'
       END
FROM TRIANGLES ;
```
<br>

문제에 사용된 TRIANGLES 테이블을 `SELECT * FROM TRIANGLES`를 통해 보니 아래와 같게 나온다.

![triangle table](https://user-images.githubusercontent.com/54492747/76059612-a4b76d00-5fc2-11ea-8800-ffba7339ce5a.png){: .align-center}

<br>

위의 코드 Output과 해당 테이블을 하나씩 비교해보니 (20,20,40)가 문제였다. (20,20,40)은 $A+B<=C$이므로 'Not A Triangle'이 되어야 하는데, 'Isosceles'으로 결과가 나온 것이다. 

위의 코드처럼 그냥 CASE WHEN문을 사용하여 순서 상관없이 조건문들을 걸면 된다고 생각했는데 그게 아니었다.

<br>

우선 CASE WHEN은 다음과 같이 작성되며, CASE 문 안에는 여러 개의 WHEN과 THEN을 추가할 수 있다.
* 데이터를 조회할 때의 조건은 WHERE문을 사용하여 조건 걸어 가져올 수 있음
* 하지만 가져온 값에 추가적인 변환이 필요할 경우에는 CASE WHEN문을 사용함
```sql
SELECT CASE <컬럼명> WHEN <값1> THEN <컬럼이 값1일 때 결과>
				    WHEN <값2> THEN <컬럼이 값2일 때 결과>
					WHEN <값3> THEN <컬럼이 값3일 때 결과>
					…
					ELSE <컬럼이 위 조건들과 매치된 것이 없었을 때 결과>
	   END
```

여기서 중요한 것은 만약 **컬럼명이 값1과 매치되면 값2와 값3에 대한 매치는 수행하지 않는다**는 것이다. 즉, 나의 위의 코드는 'Not A Triangle'보다 'Isosceles' 조건을 먼저 걸어주었기 때문에 (20,20,40)에 대한 결과가 'Isosceles'로 나온 것이다. <br>
CASE WHEN문은 나름 자주 쓴다고 생각했는데, **순서 조건**이 걸려있을 줄이야..! 새로운 것을 알게 된 것 같아 기분 좋았다. <br>

참고 : CASE WHEN에 대해 구글링 하다가 [leeoh04님 블로그 글](https://blog.naver.com/PostView.nhn?blogId=leeoh04&logNo=20099476933&proxyReferer=https%3A%2F%2Fwww.google.com%2F)에 많은 도움을 받았다. CASE WHEN문에 더 알고 싶으면 해당 링크를 참고하면 될 것 같다.

<br>

그리하여 최종적인 답은 아래와 같다.
```sql
SELECT CASE WHEN A + B <= C THEN 'Not A Triangle'
            WHEN A = B AND B = C THEN 'Equilateral'
            WHEN A = B OR B = C OR A = C THEN 'Isosceles'
            ELSE 'Scalene'
       END
FROM TRIANGLES
```
