---
layout: post
title:  "[백준] 입출력과 사칙연산 - 1008번 A/B"
subtitle:   "Bakejoon: python 1008"
categories: programming
tags: python
comments: true
---
##  <font color = "#EFC050"> 문제 </font>    
[문제 링크](https://www.acmicpc.net/problem/1008) <br>
두 정수 A와 B를 입력받은 다음, A/B를 출력하는 프로그램을 작성하시오.

### 입력
첫째 줄에 A와 B가 주어진다. (0 < A, B < 10)

###  출력
첫째 줄에 A/B를 출력한다. 실제 정답과 출력값의 절대오차 또는 상대오차가 10^-9 이하이면 정답이다.

--------

##  <font color = "#EFC050"> 해결 </font>  
```python
a, b = map(int, input().split())
print(a/b)
```

```python
print(eval('/'.join(input().split())))
```

### 코드설명
1000번 문제와 같은 형식이다.
