---
layout: post
title:  "[백준] 입출력과 사칙연산 - 10430번 곱셈"
subtitle:   "Bakejoon: python 10430"
categories: programming
tags: python_baekjoon
comments: true
---
##  <font color = "#EFC050"> 문제 </font>    
[문제 링크](https://www.acmicpc.net/problem/10430) <br>
(A+B)%C는 ((A%C) + (B%C))%C 와 같을까? <br>
(A×B)%C는 ((A%C) × (B%C))%C 와 같을까? <br>
세 수 A, B, C가 주어졌을 때, 위의 네 가지 값을 구하는 프로그램을 작성하시오.

### 입력
첫째 줄에 A, B, C가 순서대로 주어진다. (2 ≤ A, B, C ≤ 10000)

###  출력
첫째 줄에 (A+B)%C, 둘째 줄에 ((A%C) + (B%C))%C, 셋째 줄에 (A×B)%C, 넷째 줄에 ((A%C) × (B%C))%C를 출력한다.

--------

##  <font color = "#EFC050"> 해결 </font>  
```python
a, b, c = map(int, input().split())

print((a+b)%c)
print(((a+c)+(b+c))%c)
print((a*b)%c)
print(((a%c)*(b%c))%c)
```

### 코드설명
출력 조건처럼 3개의 정수로 만들어진 4가지의 값을 출력하기만 하면 된다.
