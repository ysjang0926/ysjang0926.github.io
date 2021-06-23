---
layout: post
title:  "[백준] 입출력과 사칙연산 - 1000번 A+B"
subtitle:   "Bakejoon: python 1000"
categories: programming
tags: python
comments: true
---
##  <font color = "#EFC050"> 문제 </font>    
[문제 링크](https://www.acmicpc.net/problem/1000) <br>
두 정수 A와 B를 입력받은 다음, A+B를 출력하는 프로그램을 작성하시오.

### 입력
첫째 줄에 A와 B가 주어진다. (0 < A, B < 10)

###  출력
첫째 줄에 A+B를 출력한다.

--------

##  <font color = "#EFC050"> 해결 </font>  
```python
a, b = map(int, input().split())
print(a+b)
```

### 코드설명
정수인 숫자 두 개가 한줄에 입력 받기 위해선 `map`함수를 사용한다. <br> 
`map`에 `int`와 `input().split()`를 넣으면 split의 결과를 모두 int로 변환해준다. <br>
또한 기본적인 사칙연산(덧셈)이므로 print문 안에 +를 통해 계산하면 된다.

<br>

## <font color = "#EFC050"> 다른 사람들 답 </font>  
```python
print(eval('+'.join(input().split())))
```
다른 사람들 답을 찾아보았는데 이걸 파이토닉(pythonic, 파이썬스럽게 만드는 것)하게 만든 것이라고 한다. <br>
파이썬의 `eval`함수를 사용하는데, `eval`함수는 연산식을 인자로 넘겨주며 결과를 반환하는 역할을 한다고 한다. (ex. eval('1==2')는 FALSE, eval('1*2')는 2) <br>
`join`은 함수를 부른 문자열을 join의 파라미터의 각 인덱스 사이에 삽입한 결과를 문자열로 반환한다. (ex. 'x'.join('yyy')는 'yxyxy', 'x'.join(['a','b','c'])는 'axbxcx')

```python
# sum 함수 사용
print(sum(map(int, input().split())))
```
