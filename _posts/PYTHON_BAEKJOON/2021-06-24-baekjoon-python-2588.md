---
layout: post
title:  "[백준] 입출력과 사칙연산 - 2588번 곱셈"
subtitle:   "Bakejoon: python 2588"
categories: programming
tags: python_baekjoon
comments: true
---
##  <font color = "#EFC050"> 문제 </font>    
[문제 링크](https://www.acmicpc.net/problem/2588) <br>
(세 자리 수) × (세 자리 수)는 다음과 같은 과정을 통하여 이루어진다. <br>
![image](https://user-images.githubusercontent.com/54492747/123267383-e9f50a80-d537-11eb-8ed6-685df70abfb0.png) <br>
(1)과 (2)위치에 들어갈 세 자리 자연수가 주어질 때 (3), (4), (5), (6)위치에 들어갈 값을 구하는 프로그램을 작성하시오.

### 입력
첫째 줄에 (1)의 위치에 들어갈 세 자리 자연수가, 둘째 줄에 (2)의 위치에 들어갈 세자리 자연수가 주어진다.

###  출력
첫째 줄부터 넷째 줄까지 차례대로 (3), (4), (5), (6)에 들어갈 값을 출력한다.

--------

##  <font color = "#EFC050"> 해결 </font>  
```python
A = int(input())
B = input()     

AxB2 = A * int(B[2])
AxB1 = A * int(B[1])
AxB0 = A * int(B[0])
AxB = A * int(B)

print(AxB2, AxB1, AxB0, AxB, sep='\n')
```

### 코드설명
첫번째 입력받은 문자는 숫자로 변환하고, 두번째 입력받은 문자는 문자열 그대로 둔다. <br>
문자열의 인덱스를 이용하여 두번째 입력 받은 문자를 하나씩 숫자로 반환하고 A와 곱한다. <br>
마지막으로 `sep='\n'`로 줄바꿈을 해준다.
