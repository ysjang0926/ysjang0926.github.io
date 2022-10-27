---
layout: post
title:  "[백준] 입출력과 사칙연산 - 10171번 고양이"
subtitle:   "Baekjoon: Python 10171"
categories: programming
tags: python_baekjoon
comments: true
---
##  <font color = "#EFC050"> 문제 </font>    
[문제 링크](https://www.acmicpc.net/problem/10171) <br>
아래 예제와 같이 고양이를 출력하시오.

###  출력
고양이를 출력한다.

--------

##  <font color = "#EFC050"> 해결 </font>  
```python
print("\\    /\\")
print(" )  ( ')")
print("(  /  )")
print(" \\(__)|")
```

### 코드설명
아무 생각 없이 복붙하다가 안되어서 당황했다. <br>
검색해보니 `\`는 이스케이프 문자를 표시한다고 한다. 예를 들면, `\n`는 줄바꿈, `\t`는 탭, `\\`는 `\`를 출력한다. <br>
위의 고양이에서 `\`를 표시하기 위해서는 `\\`를 써야한다.
