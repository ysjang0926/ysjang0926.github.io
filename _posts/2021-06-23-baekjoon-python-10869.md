---
layout: post
title:  "[백준] 입출력과 사칙연산 - 10869번 사칙연산"
subtitle:   "Bakejoon: python 10869"
categories: programming
tags: python
comments: true
---
##  <font color = "#EFC050"> 문제 </font>    
[문제 링크](https://www.acmicpc.net/problem/10869) <br>
두 자연수 A와 B가 주어진다. 이때, A+B, A-B, A*B, A/B(몫), A%B(나머지)를 출력하는 프로그램을 작성하시오. 

### 입력
두 자연수 A와 B가 주어진다. (1 ≤ A, B ≤ 10,000)

###  출력
첫째 줄에 A+B, 둘째 줄에 A-B, 셋째 줄에 A*B, 넷째 줄에 A/B, 다섯째 줄에 A%B를 출력한다.

--------

##  <font color = "#EFC050"> 해결 </font>  
```python
a, b = map(int, input().split())

print(a+b)
print(a-b)
print(a*b)
print(a//b)
print(a%b)
```

### 코드설명
1000번 문제와 비슷한 형식이다.


## <font color = "#EFC050"> 다른 사람 답안 </font>  
```python
numbers = input().split()
operators = ['+', '-', '*', '//', '%']
for operator in operators:
	print(eval(operator.join(numbers)))
```

