---
layout: post
title:  "[백준] 입출력과 사칙연산 - 10718번 We love kriii"
subtitle:   "HackerRank: SQL Basic Select"
categories: programming
tags: python_baekjoon
comments: true
---
##  <font color = "#EFC050"> 문제 </font>    
[문제 링크](https://www.acmicpc.net/problem/10718) <br>
ACM-ICPC 인터넷 예선, Regional, 그리고 World Finals까지 이미 2회씩 진출해버린 kriii는 미련을 버리지 못하고 왠지 모르게 올해에도 파주 World Finals 준비 캠프에 참여했다. <br>
대회를 뜰 줄 모르는 지박령 kriii를 위해서 격려의 문구를 출력해주자.

###  출력
두 줄에 걸쳐 "강한친구 대한육군"을 한 줄에 한 번씩 출력한다.

--------

##  <font color = "#EFC050"> 해결 </font>  
```python
print("강한친구 대한육군\n"*2)
```

### 코드설명
print("~")에서 "~"뒤에 `*숫자`를 문자열에 적용하게 되면 입력한 숫자만큼 문자열을 반복하여 출력할 수 있다.<br>
또한 `\n`은 줄바꿈 문자로 다음 출력 위치를 다음 줄로 이동시킨다.
