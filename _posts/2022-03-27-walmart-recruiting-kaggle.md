---
layout: post
title: "WalMart Recruiting 데이터로 배우는 Kaggle 기초"
categories: data
tags: kaggle
comments: true
toc: true
toc_sticky: true
---


>
 
W
a
l
M
a
r
t
 
R
e
c
r
u
i
t
i
n
g
 
데
이
터
를
 
통
해
 
간
단
하
게
 
데
이
터
 
불
러
오
기
,
 
데
이
터
 
전
처
리
,
 
모
델
링
,
 
결
과
 
제
출
까
지
의
 
전
체
 
프
로
세
스
를
 
정
리
해
보
았
습
니
다
🐱
‍
👓




#
 
0
.
 
대
회
 
소
개
 
-
 
W
a
l
M
a
r
t
 
판
매
량
 
예
측


!
[
i
m
a
g
e
]
(
h
t
t
p
s
:
/
/
u
s
e
r
-
i
m
a
g
e
s
.
g
i
t
h
u
b
u
s
e
r
c
o
n
t
e
n
t
.
c
o
m
/
5
4
4
9
2
7
4
7
/
1
6
0
2
8
5
5
1
7
-
a
9
5
2
a
d
f
8
-
e
2
2
f
-
4
9
9
8
-
8
8
a
b
-
2
d
0
8
2
9
0
9
e
e
7
a
.
p
n
g
)




*
 
대
회
 
링
크
 
:
 
[
W
a
l
m
a
r
t
 
R
e
c
r
u
i
t
i
n
g
 
-
 
S
t
o
r
e
 
S
a
l
e
s
 
F
o
r
e
c
a
s
t
i
n
g
]
(
h
t
t
p
s
:
/
/
w
w
w
.
k
a
g
g
l
e
.
c
o
m
/
c
o
m
p
e
t
i
t
i
o
n
s
/
w
a
l
m
a
r
t
-
r
e
c
r
u
i
t
i
n
g
-
s
t
o
r
e
-
s
a
l
e
s
-
f
o
r
e
c
a
s
t
i
n
g
)


*
 
목
표
 
:
 
W
a
l
M
a
r
t
의
 
각
 
지
점
과
 
부
서
의
 
과
거
 
매
출
 
기
록
들
을
 
토
대
로
 
미
래
 
월
마
트
 
지
점
들
의
 
부
서
 
전
체
 
판
매
량
을
 
예
측
하
는
 
것


*
 
데
이
터
 
:
 
4
5
개
의
 
W
a
l
m
a
r
t
 
매
장
에
 
대
한
 
과
거
 
판
매
 
데
이
터
 
(
2
0
1
0
년
 
2
월
 
~
 
2
0
1
1
년
 
마
지
막
 
주
)


 
 
 
 
*
 
W
a
l
m
a
r
t
는
 
연
중
 
프
로
모
션
 
인
하
 
이
벤
트
를
 
운
영
하
는
데
,
 
이
런
 
이
벤
트
들
은
 
메
인
 
연
휴
를
 
앞
두
고
 
진
행
되
는
 
편
 
(
슈
퍼
볼
,
 
노
동
절
,
 
추
수
 
감
사
절
,
 
크
리
스
마
스
가
 
가
장
 
대
표
적
인
 
연
휴
)


 
 
 
 
*
 
위
의
 
메
인
 
공
휴
일
을
 
포
함
하
는
 
주
(
w
e
e
k
)
는
 
비
공
휴
일
보
다
 
판
매
량
에
 
더
 
영
향
을
 
주
지
 
않
을
 
것
인
가
?
 
라
고
 
생
각
하
고
 
진
행


-
-
-


#
 
1
.
 
D
a
t
a
 
L
o
a
d


*
 
이
번
 
n
o
t
e
b
o
o
k
에
서
는
 
t
r
a
i
n
과
 
t
e
s
t
 
d
a
t
a
만
을
 
불
러
와
서
 
진
행
하
였
습
니
다
.
 
(
s
t
o
r
e
s
,
 
f
e
a
t
u
r
e
s
 
d
a
t
a
 
사
용
 
안
함
)


*
 
`
p
d
.
r
e
a
d
_
c
s
v
(
)
`
 
함
수
를
 
사
용
하
면
 
c
s
v
 
형
식
의
 
파
일
을
 
읽
어
서
 
저
장
할
 
수
 
있
습
니
다
.


 
 
 
 
*
 
괄
호
 
안
에
는
 
해
당
 
파
일
의
 
경
로
를
 
넣
어
주
면
 
되
는
데
,
 
만
약
 
z
i
p
 
형
식
이
더
라
도
 
파
일
이
 
하
나
만
 
있
다
면
 
상
관
없
습
니
다
.


 
 
 
 
*
 
t
r
a
i
n
과
 
t
e
s
t
라
는
 
데
이
터
명
에
 
데
이
터
를
 
저
장
하
고
 
t
r
a
i
n
과
 
t
e
s
t
를
 
쳐
주
면
 
d
a
t
a
를
 
출
력
할
 
수
 
있
으
며
,
 
이
때
 
r
o
w
와
 
c
o
l
u
m
n
 
갯
수
를
 
확
인
할
 
수
 
있
습
니
다
.



```python
#
 
T
h
i
s
 
P
y
t
h
o
n
 
3
 
e
n
v
i
r
o
n
m
e
n
t
 
c
o
m
e
s
 
w
i
t
h
 
m
a
n
y
 
h
e
l
p
f
u
l
 
a
n
a
l
y
t
i
c
s
 
l
i
b
r
a
r
i
e
s
 
i
n
s
t
a
l
l
e
d

#
 
I
t
 
i
s
 
d
e
f
i
n
e
d
 
b
y
 
t
h
e
 
k
a
g
g
l
e
/
p
y
t
h
o
n
 
D
o
c
k
e
r
 
i
m
a
g
e
:
 
h
t
t
p
s
:
/
/
g
i
t
h
u
b
.
c
o
m
/
k
a
g
g
l
e
/
d
o
c
k
e
r
-
p
y
t
h
o
n

#
 
F
o
r
 
e
x
a
m
p
l
e
,
 
h
e
r
e
'
s
 
s
e
v
e
r
a
l
 
h
e
l
p
f
u
l
 
p
a
c
k
a
g
e
s
 
t
o
 
l
o
a
d


i
m
p
o
r
t
 
n
u
m
p
y
 
a
s
 
n
p
 
#
 
l
i
n
e
a
r
 
a
l
g
e
b
r
a

i
m
p
o
r
t
 
p
a
n
d
a
s
 
a
s
 
p
d
 
#
 
d
a
t
a
 
p
r
o
c
e
s
s
i
n
g
,
 
C
S
V
 
f
i
l
e
 
I
/
O
 
(
e
.
g
.
 
p
d
.
r
e
a
d
_
c
s
v
)


#
 
I
n
p
u
t
 
d
a
t
a
 
f
i
l
e
s
 
a
r
e
 
a
v
a
i
l
a
b
l
e
 
i
n
 
t
h
e
 
r
e
a
d
-
o
n
l
y
 
"
.
.
/
i
n
p
u
t
/
"
 
d
i
r
e
c
t
o
r
y

#
 
F
o
r
 
e
x
a
m
p
l
e
,
 
r
u
n
n
i
n
g
 
t
h
i
s
 
(
b
y
 
c
l
i
c
k
i
n
g
 
r
u
n
 
o
r
 
p
r
e
s
s
i
n
g
 
S
h
i
f
t
+
E
n
t
e
r
)
 
w
i
l
l
 
l
i
s
t
 
a
l
l
 
f
i
l
e
s
 
u
n
d
e
r
 
t
h
e
 
i
n
p
u
t
 
d
i
r
e
c
t
o
r
y


i
m
p
o
r
t
 
o
s

f
o
r
 
d
i
r
n
a
m
e
,
 
_
,
 
f
i
l
e
n
a
m
e
s
 
i
n
 
o
s
.
w
a
l
k
(
'
/
k
a
g
g
l
e
/
i
n
p
u
t
'
)
:

 
 
 
 
f
o
r
 
f
i
l
e
n
a
m
e
 
i
n
 
f
i
l
e
n
a
m
e
s
:

 
 
 
 
 
 
 
 
p
r
i
n
t
(
o
s
.
p
a
t
h
.
j
o
i
n
(
d
i
r
n
a
m
e
,
 
f
i
l
e
n
a
m
e
)
)


#
 
Y
o
u
 
c
a
n
 
w
r
i
t
e
 
u
p
 
t
o
 
2
0
G
B
 
t
o
 
t
h
e
 
c
u
r
r
e
n
t
 
d
i
r
e
c
t
o
r
y
 
(
/
k
a
g
g
l
e
/
w
o
r
k
i
n
g
/
)
 
t
h
a
t
 
g
e
t
s
 
p
r
e
s
e
r
v
e
d
 
a
s
 
o
u
t
p
u
t
 
w
h
e
n
 
y
o
u
 
c
r
e
a
t
e
 
a
 
v
e
r
s
i
o
n
 
u
s
i
n
g
 
"
S
a
v
e
 
&
 
R
u
n
 
A
l
l
"
 

#
 
Y
o
u
 
c
a
n
 
a
l
s
o
 
w
r
i
t
e
 
t
e
m
p
o
r
a
r
y
 
f
i
l
e
s
 
t
o
 
/
k
a
g
g
l
e
/
t
e
m
p
/
,
 
b
u
t
 
t
h
e
y
 
w
o
n
'
t
 
b
e
 
s
a
v
e
d
 
o
u
t
s
i
d
e
 
o
f
 
t
h
e
 
c
u
r
r
e
n
t
 
s
e
s
s
i
o
n
```

#
#
#
 
t
r
a
i
n
 
출
력
 
결
과


*
 
4
2
1
5
7
0
 
r
o
w
s
 
×
 
5
 
c
o
l
u
m
n
s


*
 
변
수
(
5
개
)
 
:
 
지
점
번
호
 
/
 
부
서
 
번
호
 
/
 
날
짜
 
/
 
그
 
주
의
 
매
출
 
/
 
휴
일
 
유
무



```python
t
r
a
i
n
 
=
 
p
d
.
r
e
a
d
_
c
s
v
(
'
/
k
a
g
g
l
e
/
i
n
p
u
t
/
w
a
l
m
a
r
t
-
r
e
c
r
u
i
t
i
n
g
-
s
t
o
r
e
-
s
a
l
e
s
-
f
o
r
e
c
a
s
t
i
n
g
/
t
r
a
i
n
.
c
s
v
.
z
i
p
'
)

t
r
a
i
n
```

*
 
`
i
s
n
a
(
)
.
s
u
m
(
)
`
을
 
통
해
 
결
측
치
 
확
인
을
 
할
 
수
 
있
습
니
다
.


 
 
 
 
*
 
t
r
a
i
n
 
데
이
터
는
 
모
두
 
0
이
라
고
 
결
과
가
 
나
오
니
 
결
측
치
가
 
없
는
 
것
을
 
알
 
수
 
있
습
니
다
.



```python
t
r
a
i
n
.
i
s
n
a
(
)
.
s
u
m
(
)
```

#
#
#
 
t
e
s
t
 
출
력
 
결
과


*
 
1
1
5
0
6
4
 
r
o
w
s
 
×
 
4
 
c
o
l
u
m
n
s



```python
t
e
s
t
 
=
 
p
d
.
r
e
a
d
_
c
s
v
(
'
/
k
a
g
g
l
e
/
i
n
p
u
t
/
w
a
l
m
a
r
t
-
r
e
c
r
u
i
t
i
n
g
-
s
t
o
r
e
-
s
a
l
e
s
-
f
o
r
e
c
a
s
t
i
n
g
/
t
e
s
t
.
c
s
v
.
z
i
p
'
)

t
e
s
t
```


```python
t
e
s
t
.
i
s
n
a
(
)
.
s
u
m
(
)
```

-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-


#
 
2
.
 
D
a
t
a
 
P
r
e
p
r
o
c
e
s
s
i
n
g


*
 
데
이
터
 
전
처
리
 
과
정
에
서
는
 
간
단
하
게
 
두
가
지
 
과
정
만
 
거
쳤
습
니
다
.


 
 
 
 
1
.
 
D
a
t
a
 
c
o
l
u
m
n
를
 
d
a
t
e
t
i
m
e
 
타
입
으
로
 
변
환
하
여
 
Y
e
a
r
,
 
M
o
n
t
h
 
변
수
를
 
새
로
 
생
성


 
 
 
 
2
.
 
t
r
a
i
n
 
d
a
t
a
와
 
t
e
s
t
 
d
a
t
a
의
 
c
o
l
u
m
n
 
개
수
 
맞
추
기
 
(
필
요
없
는
 
c
o
l
u
m
n
 
제
거
)


#
#
 
1
.
 
D
a
t
a
 
c
o
l
u
m
n
 
전
처
리


#
#
#
 
C
o
l
u
m
n
 
T
y
p
e
 
확
인


*
 
`
.
d
t
y
p
e
s
`
를
 
통
해
 
c
o
l
u
m
n
 
t
y
p
e
을
 
확
인
할
 
수
 
있
습
니
다
.


*
 
여
기
서
 
D
a
t
e
 
c
o
l
u
m
n
은
 
o
b
j
e
c
t
 
타
입
으
로
 
나
왔
기
 
때
문
에
,
 
d
a
t
e
t
i
m
e
 
타
입
으
로
 
변
환
이
 
필
요
합
니
다
.



```python
t
r
a
i
n
.
d
t
y
p
e
s
```

#
#
#
 
d
a
t
e
t
i
m
e
 
타
입
으
로
 
변
환


*
 
D
a
t
e
 
c
o
l
u
m
n
에
서
 
숫
자
만
 
추
출
하
여
 
M
o
n
t
h
,
 
Y
e
a
r
 
등
으
로
 
추
출
하
기
 
위
해
서
 
진
행
하
였
습
니
다
.


 
 
 
 
*
 
p
a
n
d
a
s
의
 
`
t
o
_
d
a
t
e
t
i
m
e
(
)
`
함
수
를
 
이
용
해
 
d
a
t
e
t
i
m
e
 
타
입
으
로
 
바
꾸
어
주
었
습
니
다
.


 
 
 
 
*
 
Y
e
a
r
,
 
M
o
n
t
h
,
 
D
a
t
e
를
 
추
출
하
기
 
위
해
 
진
행
하
였
습
니
다
.


*
 
Y
e
a
r
 
:
 
D
a
t
e
 
중
 
년
도
 
추
출
 
/
 
M
o
n
t
h
 
:
 
D
a
t
e
 
중
 
월
 
추
출


*
 
t
r
a
i
n
을
 
출
력
하
면
 
c
o
l
u
m
n
이
 
2
개
(
Y
e
a
r
,
 
M
o
n
t
h
)
 
추
가
되
어
있
는
 
것
을
 
확
인
할
 
수
 
있
습
니
다
.



```python
t
r
a
i
n
[
'
D
a
t
e
'
]
 
=
 
p
d
.
t
o
_
d
a
t
e
t
i
m
e
(
t
r
a
i
n
[
'
D
a
t
e
'
]
)
 
#
 
t
r
a
i
n
[
"
D
a
t
e
"
]
 
=
 
t
r
a
i
n
[
"
D
a
t
e
"
]
.
a
s
t
y
p
e
(
"
d
a
t
e
t
i
m
e
6
4
"
)

t
r
a
i
n
[
'
Y
e
a
r
'
]
 
=
 
t
r
a
i
n
[
'
D
a
t
e
'
]
.
d
t
.
y
e
a
r

t
r
a
i
n
[
'
M
o
n
t
h
'
]
 
=
 
t
r
a
i
n
[
'
D
a
t
e
'
]
.
d
t
.
m
o
n
t
h

t
r
a
i
n
```

*
 
`
.
d
t
y
p
e
s
`
로
 
확
인
을
 
하
면
 
D
a
t
e
는
 
d
a
t
e
t
i
m
e
 
타
입
으
로
 
변
했
고
,
 
Y
e
a
r
과
 
M
o
n
t
h
 
c
o
l
u
m
n
은
 
정
수
(
i
n
t
6
4
)
 
형
태
로
 
추
가
된
 
것
을
 
알
 
수
 
있
습
니
다
.



```python
t
r
a
i
n
.
d
t
y
p
e
s
```

*
 
t
e
s
t
도
 
t
r
a
i
n
과
 
같
은
 
방
식
으
로
 
데
이
터
 
타
입
을
 
바
꾸
고
 
c
o
l
u
m
n
을
 
추
가
해
주
었
습
니
다
.
 
(
t
r
a
i
n
과
 
t
e
s
t
의
 
c
o
l
u
m
n
 
개
수
는
 
동
일
해
야
하
기
 
때
문
)



```python
t
e
s
t
[
'
D
a
t
e
'
]
 
=
 
p
d
.
t
o
_
d
a
t
e
t
i
m
e
(
t
e
s
t
[
'
D
a
t
e
'
]
)

t
e
s
t
[
'
Y
e
a
r
'
]
 
=
 
t
e
s
t
[
'
D
a
t
e
'
]
.
d
t
.
y
e
a
r

t
e
s
t
[
'
M
o
n
t
h
'
]
 
=
 
t
e
s
t
[
'
D
a
t
e
'
]
.
d
t
.
m
o
n
t
h

t
e
s
t
```

#
#
 
2
.
 
필
요
없
는
 
c
o
l
u
m
n
 
제
거


*
 
t
r
a
i
n
 
d
a
t
a
에
 
W
e
e
k
l
y
_
S
a
l
e
s
 
c
o
l
u
m
n
 
제
거


 
 
 
 
*
 
t
e
s
t
 
d
a
t
a
를
 
보
면
 
W
e
e
k
l
y
_
S
a
l
e
s
 
c
o
l
u
m
n
이
 
존
재
하
지
 
않
습
니
다
.
 
(
구
하
고
자
하
는
 
판
매
량
 
c
o
l
u
m
n
이
기
 
때
문
)


*
 
t
r
a
i
n
 
d
a
t
a
와
 
t
e
s
t
 
d
a
t
a
에
 
D
a
t
e
 
c
o
l
u
m
n
 
제
거


 
 
 
 
*
 
분
석
에
 
필
요
한
 
Y
e
a
r
과
 
M
o
n
t
h
 
c
o
l
u
m
n
을
 
추
출
하
였
으
니
,
 
필
요
없
는
 
D
a
t
e
 
c
o
l
u
m
n
은
 
제
거
하
였
습
니
다
.


 
 
 
 
*
 
즉
,
 
d
a
t
e
t
i
m
e
 
타
입
으
로
 
변
환
한
 
것
은
 
Y
e
a
r
와
 
M
o
n
t
h
 
c
o
l
u
m
n
을
 
쉽
게
 
정
수
로
 
변
형
하
기
 
위
함
이
었
습
니
다
.
 
(
d
a
t
e
t
i
m
e
 
타
입
만
으
론
 
분
석
에
 
활
용
하
기
에
 
어
렵
다
고
 
판
단
)


*
 
`
.
d
r
o
p
(
c
o
l
u
m
n
s
=
[
.
.
.
]
)
`
를
 
사
용
하
여
 
c
o
l
u
m
n
 
제
거


 
 
 
 
*
 
c
o
l
u
m
n
 
여
러
개
를
 
삭
제
하
고
 
싶
으
면
 
`
[
]
`
를
 
사
용
하
여
 
적
어
주
면
 
됩
니
다
.
 
(
여
러
 
c
o
l
u
m
n
 
호
출
 
또
는
 
특
정
 
c
o
l
u
m
n
 
호
출
하
기
 
위
함
)


*
 
t
r
a
i
n
2
와
 
t
e
s
t
2
로
 
새
로
운
 
데
이
터
명
으
로
 
저
장


 
 
 
 
*
 
왜
냐
하
면
 
기
존
 
t
r
a
i
n
,
 
t
e
s
t
 
데
이
터
에
 
c
o
l
u
m
n
을
 
제
거
하
면
,
 
제
거
한
 
상
태
로
 
저
장
이
 
되
기
 
때
문
에
 
필
요
할
 
때
 
다
시
 
불
러
올
 
수
 
없
기
 
때
문
입
니
다
.


 
 
 
 
*
 
t
r
a
i
n
 
데
이
터
의
 
W
e
e
k
l
y
_
S
a
l
e
s
 
c
o
l
u
m
n
이
 
필
요
하
기
 
때
문
에
 
새
로
운
 
데
이
터
명
으
로
 
저
장
하
였
습
니
다
.



```python
t
r
a
i
n
2
 
=
 
t
r
a
i
n
.
d
r
o
p
(
c
o
l
u
m
n
s
=
[
'
D
a
t
e
'
,
 
'
W
e
e
k
l
y
_
S
a
l
e
s
'
]
)
 
#
 
D
a
t
e
,
W
e
e
k
l
y
_
S
a
l
e
s
 
제
거

t
r
a
i
n
2
```


```python
t
e
s
t
2
 
=
 
t
e
s
t
.
d
r
o
p
(
c
o
l
u
m
n
s
=
[
'
D
a
t
e
'
]
)
 
#
 
D
a
t
e
 
제
거

t
e
s
t
2
```

-
-
-
-
-
-
-
-
-
-
-
-
-
-
-


#
 
3
.
 
E
D
A
 
(
M
o
n
t
h
 
v
s
 
W
e
e
k
l
y
_
S
a
l
e
s
)


*
 
B
o
x
-
p
l
o
t
을
 
통
해
 
M
o
n
t
h
 
c
o
l
u
m
n
이
 
W
e
e
k
l
y
_
S
a
l
e
s
 
c
o
l
u
m
n
과
 
유
의
미
한
 
관
계
가
 
존
재
하
는
지
 
확
인
하
였
습
니
다
.


 
 
 
 
*
 
s
e
a
b
o
r
n
과
 
m
a
t
p
l
o
t
l
i
b
.
p
y
p
l
o
t
을
 
i
m
p
o
r
t
하
고
,
 
`
p
l
t
.
f
i
g
u
r
e
(
)
`
과
 
`
s
n
s
.
b
o
x
p
l
o
t
(
)
`
을
 
사
용
하
면
 
B
o
x
-
p
l
o
t
을
 
그
릴
 
수
 
있
습
니
다
.


 
 
 
 
*
 
`
p
l
t
.
f
i
g
u
r
e
(
)
`
 
함
수
에
 
`
f
i
g
s
i
z
e
 
=
 
(
가
로
,
 
세
로
)
`
 
파
라
미
터
를
 
넣
어
주
면
 
그
림
 
크
기
를
 
조
정
할
 
수
 
있
습
니
다
.


 
 
 
 
*
 
이
때
 
M
o
n
t
h
 
c
o
l
u
m
n
과
 
W
e
e
k
l
y
_
S
a
l
e
s
 
c
o
l
u
m
n
 
사
이
의
 
유
의
미
한
 
관
계
가
 
존
재
하
는
를
 
알
아
보
기
 
위
함
이
니
,
 
`
s
n
s
.
b
o
x
p
l
o
t
(
)
`
 
함
수
의
 
파
라
미
터
로
 
M
o
n
t
h
 
c
o
l
u
m
n
과
 
W
e
e
k
l
y
_
S
a
l
e
s
 
c
o
l
u
m
n
을
 
넣
어
주
었
습
니
다
.



```python
i
m
p
o
r
t
 
s
e
a
b
o
r
n
 
a
s
 
s
n
s

i
m
p
o
r
t
 
m
a
t
p
l
o
t
l
i
b
.
p
y
p
l
o
t
 
a
s
 
p
l
t

p
l
t
.
f
i
g
u
r
e
(
f
i
g
s
i
z
e
 
=
 
(
1
4
,
 
8
)
)

s
n
s
.
b
o
x
p
l
o
t
(
t
r
a
i
n
[
'
M
o
n
t
h
'
]
,
 
t
r
a
i
n
[
'
W
e
e
k
l
y
_
S
a
l
e
s
'
]
)
```

*
 
o
u
t
l
i
e
r
를
 
제
거
하
여
 
다
시
 
B
o
x
-
p
l
o
t
을
 
그
렸
습
니
다
.
 
(
o
u
t
l
i
e
r
가
 
존
재
해
서
 
한
눈
에
 
B
o
x
-
p
l
o
t
을
 
파
악
하
기
 
어
렵
기
 
때
문
)


 
 
 
 
*
 
`
s
n
s
.
b
o
x
p
l
o
t
(
)
`
 
함
수
에
서
 
`
s
h
o
w
f
l
i
e
r
s
`
 
옵
션
을
 
`
F
a
l
s
e
`
로
 
설
정
하
여
 
o
u
t
l
i
e
r
를
 
제
거
해
주
었
습
니
다
.



```python
p
l
t
.
f
i
g
u
r
e
(
f
i
g
s
i
z
e
 
=
 
(
1
4
,
 
8
)
)

s
n
s
.
b
o
x
p
l
o
t
(
t
r
a
i
n
[
'
M
o
n
t
h
'
]
,
 
t
r
a
i
n
[
'
W
e
e
k
l
y
_
S
a
l
e
s
'
]
,
 
s
h
o
w
f
l
i
e
r
s
 
=
 
F
a
l
s
e
)
```

*
 
B
o
x
-
p
l
o
t
 
해
석


 
 
 
 
*
 
b
o
x
의
 
가
운
데
 
선
 
:
 
중
앙
값
 
/
 
b
o
x
의
 
윗
 
선
 
:
 
상
위
 
2
5
%
에
 
해
당
하
는
 
값
 
/
 
b
o
x
의
 
아
랫
선
 
:
 
7
5
%
에
 
해
당
하
는
 
값
 
/
 
맨
 
위
의
 
선
 
:
 
최
댓
값
 
/
 
맨
 
아
래
의
 
선
 
:
 
최
솟
값


 
 
 
 
*
 
월
별
로
 
꽤
 
유
의
미
한
 
차
이
가
 
존
재
한
다
는
 
것
을
 
알
 
수
 
있
습
니
다
.


 
 
 
 
*
 
또
한
 
최
솟
값
 
중
에
서
 
마
이
너
스
인
 
값
들
이
 
일
부
 
존
재
하
는
데
,
 
실
제
 
판
매
량
보
다
 
반
품
 
또
는
 
환
불
이
 
많
아
 
수
익
이
 
나
지
 
않
은
 
경
우
 
마
이
너
스
 
값
으
로
 
들
어
왔
을
 
것
이
라
 
추
측
하
였
습
니
다
.


<
b
r
>


※
 
O
v
e
r
v
i
e
w
에
서
 
[
E
v
a
l
u
a
t
i
o
n
]
(
h
t
t
p
s
:
/
/
w
w
w
.
k
a
g
g
l
e
.
c
o
m
/
c
o
m
p
e
t
i
t
i
o
n
s
/
w
a
l
m
a
r
t
-
r
e
c
r
u
i
t
i
n
g
-
s
t
o
r
e
-
s
a
l
e
s
-
f
o
r
e
c
a
s
t
i
n
g
/
o
v
e
r
v
i
e
w
/
e
v
a
l
u
a
t
i
o
n
)
을
 
보
면
,
 
휴
일
 
주
의
 
가
중
치
를
 
다
른
 
주
의
 
가
중
치
보
다
 
5
배
 
더
 
높
게
 
부
여
한
 
것
을
 
확
인
할
 
수
 
있
습
니
다
.


!
[
i
m
a
g
e
]
(
h
t
t
p
s
:
/
/
u
s
e
r
-
i
m
a
g
e
s
.
g
i
t
h
u
b
u
s
e
r
c
o
n
t
e
n
t
.
c
o
m
/
5
4
4
9
2
7
4
7
/
1
6
0
2
8
5
7
1
7
-
2
a
a
6
7
9
4
f
-
1
0
6
c
-
4
3
c
e
-
b
f
9
3
-
9
7
4
c
7
e
f
6
8
8
9
6
.
p
n
g
)




*
 
즉
,
 
휴
일
의
 
판
매
량
을
 
정
확
하
게
 
예
측
할
수
록
 
정
확
도
가
 
더
 
높
은
 
것
으
로
 
판
단
하
겠
다
는
 
의
미
입
니
다
.


*
 
그
렇
기
 
때
문
에
 
어
떤
 
월
의
 
휴
일
에
 
판
매
량
이
 
더
 
높
고
 
낮
은
지
를
 
판
단
할
 
수
 
있
는
 
M
o
n
t
h
 
c
o
l
u
m
n
이
 
중
요
한
 
역
할
을
 
하
게
 
됩
니
다
.


*
 
대
회
마
다
 
다
르
겠
지
만
 
W
a
l
M
a
r
t
 
대
회
처
럼
 
특
정
 
조
건
의
 
점
수
 
산
정
 
방
식
이
 
다
를
 
수
 
있
기
 
때
문
에
,
 
대
회
에
서
 
좋
은
 
점
수
를
 
받
고
 
싶
다
면
 
점
수
 
산
정
 
방
식
을
 
고
려
하
여
 
어
떻
게
 
데
이
터
를
 
전
처
리
하
고
 
분
석
하
면
 
더
 
높
은
 
점
수
를
 
받
을
 
수
 
있
을
지
에
 
대
한
 
고
민
이
 
필
요
합
니
다
.




-
-
-
-
-
-
-
-
-
-
-


#
 
4
.
 
M
o
d
e
l
i
n
g


*
 
R
a
n
d
o
m
F
o
r
e
s
t
 
모
델
 
사
용


 
 
 
 
*
 
R
a
n
d
o
m
F
o
r
e
s
t
에
는
 
크
게
 
두
 
가
지
 
종
류
가
 
있
으
며
,
 
R
a
n
d
o
m
F
o
r
e
s
t
C
l
a
s
s
i
f
i
e
r
와
 
R
a
n
d
o
m
F
o
r
e
s
t
R
e
g
r
e
s
s
o
r
가
 
있
습
니
다
.


 
 
 
 
*
 
R
a
n
d
o
m
F
o
r
e
s
t
C
l
a
s
s
i
f
i
e
r
 
:
 
분
류
를
 
할
 
때
 
사
용
 
/
 
R
a
n
d
o
m
F
o
r
e
s
t
R
e
g
r
e
s
s
o
r
 
:
 
숫
자
 
예
측
할
 
때
 
사
용


 
 
 
 
*
 
여
기
서
는
 
예
측
하
고
자
 
하
는
 
결
과
값
이
 
한
 
주
의
 
판
매
량
이
었
기
 
때
문
에
 
R
a
n
d
o
m
F
o
r
e
s
t
R
e
g
r
e
s
s
o
r
를
 
사
용
하
였
습
니
다
.


#
#
 
(
1
)
 
모
델
 
가
져
오
기



```python
f
r
o
m
 
s
k
l
e
a
r
n
.
e
n
s
e
m
b
l
e
 
i
m
p
o
r
t
 
R
a
n
d
o
m
F
o
r
e
s
t
R
e
g
r
e
s
s
o
r
 
```

#
#
 
(
2
)
 
모
델
 
적
용


*
 
`
?
R
a
n
d
o
m
F
o
r
e
s
t
R
e
g
r
e
s
s
o
r
`
를
 
통
해
 
모
델
을
 
구
성
할
 
때
 
필
요
한
 
옵
션
들
을
 
확
인
할
 
수
 
있
으
며
,
 
이
번
에
는
 
`
n
_
j
o
b
s
`
 
옵
션
만
 
사
용
하
였
습
니
다
.


*
 
`
n
_
j
o
b
s
`
 
:
 
사
용
할
 
코
어
수
(
C
P
U
의
 
c
o
r
e
개
수
)
 
지
정
하
며
,
 
사
용
하
는
 
C
P
U
 
코
어
 
개
수
에
 
비
례
해
서
 
속
도
도
 
빨
라
집
니
다
.


 
 
 
 
*
 
`
n
_
j
o
b
s
=
-
1
`
로
 
지
정
하
면
 
컴
퓨
터
의
 
모
든
 
코
어
를
 
사
용
하
는
 
것
 
(
U
s
e
 
a
l
l
 
a
v
a
i
l
a
b
l
e
 
c
o
r
e
s
 
o
n
 
t
h
e
 
m
a
c
h
i
n
e
)


 
 
 
 
*
 
k
a
g
g
l
e
에
서
는
 
사
용
자
 
당
 
최
대
 
4
개
의
 
C
P
U
를
 
지
원
해
주
며
,
 
제
일
 
많
이
 
사
용
하
고
 
싶
다
면
 
4
를
 
적
어
도
 
되
고
 
최
댓
값
을
 
의
미
하
는
 
-
1
을
 
적
어
도
 
됩
니
다
.
 
(
코
랩
의
 
경
우
 
최
대
 
2
개
의
 
C
P
U
를
 
지
원
)


*
 
`
.
f
i
t
(
)
`
 
:
 
모
델
 
훈
련


 
 
 
 
*
 
이
때
 
왼
쪽
에
는
 
학
습
 
데
이
터
,
 
오
른
쪽
에
는
 
구
하
고
자
하
는
 
c
o
l
u
m
n
을
 
넣
어
줘
야
 
합
니
다
.


 
 
 
 
*
 
학
습
 
데
이
터
 
:
 
전
처
리
한
 
t
r
a
i
n
2
 
데
이
터
 
/
 
구
하
고
자
하
는
 
c
o
l
u
m
n
 
:
 
W
e
e
k
l
y
_
S
a
l
e
s
 
c
o
l
u
m
n


*
 
`
%
%
t
i
m
e
`
 
:
 
코
드
를
 
수
행
하
는
데
 
걸
린
 
시
간
 
측
정



```python
%
%
t
i
m
e

r
f
r
 
=
 
R
a
n
d
o
m
F
o
r
e
s
t
R
e
g
r
e
s
s
o
r
(
n
_
j
o
b
s
 
=
 
-
1
)

r
f
r
.
f
i
t
(
t
r
a
i
n
2
,
 
t
r
a
i
n
[
'
W
e
e
k
l
y
_
S
a
l
e
s
'
]
)
```

#
#
 
(
3
)
 
값
 
예
측


*
 
`
.
p
r
e
d
i
c
t
(
)
`
 
:
 
값
 
예
측


 
 
 
 
*
 
모
델
을
 
학
습
시
키
고
 
나
서
 
t
e
s
t
 
데
이
터
에
 
대
한
 
예
측
을
 
하
기
 
위
해
 
r
e
s
u
l
t
에
 
t
e
s
t
2
에
 
대
한
 
예
측
 
결
과
를
 
저
장
합
니
다
.



```python
r
e
s
u
l
t
 
=
 
r
f
r
.
p
r
e
d
i
c
t
(
t
e
s
t
2
)

r
e
s
u
l
t
 
#
 
a
r
r
a
y
 
형
식
```

-
-
-
-
-
-
-
-
-
-
-
-


#
 
5
.
 
S
u
b
m
i
t
 
R
e
s
u
l
t


*
 
예
측
한
 
결
과
값
을
 
제
출
 
데
이
터
 
형
태
에
 
맞
게
 
저
장
한
 
후
 
제
출
하
면
 
됩
니
다
.


 
 
 
 
*
 
k
a
g
g
l
e
에
서
는
 
제
출
 
데
이
터
의
 
경
우
 
s
a
m
p
l
e
S
u
b
m
i
s
s
i
o
n
 
파
일
을
 
통
해
 
확
인
할
 
수
 
있
고
 
기
본
으
로
 
제
공
되
며
,
 
t
r
a
i
n
과
 
t
e
s
t
 
d
a
t
a
와
 
같
이
 
위
에
서
 
데
이
터
 
경
로
를
 
알
 
수
 
있
습
니
다
.


#
#
#
 
제
출
 
데
이
터
 
형
태


*
 
제
출
해
야
하
는
 
데
이
터
는
 
아
래
와
 
같
은
 
형
식
이
고
,
 
W
e
e
k
l
y
_
S
a
l
e
s
 
부
분
에
 
예
측
한
 
결
과
값
을
 
넣
어
주
면
 
됩
니
다
.



```python
s
u
b
 
=
 
p
d
.
r
e
a
d
_
c
s
v
(
'
/
k
a
g
g
l
e
/
i
n
p
u
t
/
w
a
l
m
a
r
t
-
r
e
c
r
u
i
t
i
n
g
-
s
t
o
r
e
-
s
a
l
e
s
-
f
o
r
e
c
a
s
t
i
n
g
/
s
a
m
p
l
e
S
u
b
m
i
s
s
i
o
n
.
c
s
v
.
z
i
p
'
)
 
#
 
제
출
양
식

s
u
b
```

#
#
#
 
예
측
한
 
결
과
값
 
넣
기


*
 
s
u
b
 
d
a
t
a
의
 
W
e
e
k
l
y
 
S
a
l
e
s
 
c
o
l
u
m
n
에
 
r
e
s
u
l
t
값
(
예
측
값
)
을
 
넣
어
주
고
 
출
력
하
면
 
값
이
 
무
사
히
 
들
어
간
 
것
을
 
확
인
할
 
수
 
있
습
니
다
.



```python
s
u
b
[
'
W
e
e
k
l
y
_
S
a
l
e
s
'
]
 
=
 
r
e
s
u
l
t

s
u
b
```

#
#
#
 
c
s
v
 
f
i
l
e
 
저
장


*
 
`
.
t
o
_
c
s
v
`
 
함
수
를
 
통
해
 
s
u
b
 
d
a
t
a
를
 
다
시
 
c
s
v
 
파
일
로
 
저
장
한
 
후
에
 
제
출
을
 
하
면
 
됩
니
다
.


 
 
 
 
*
 
왼
쪽
에
 
파
일
 
이
름
,
 
오
른
쪽
에
 
`
i
n
d
e
x
 
=
 
F
a
l
s
e
`
 
라
고
 
기
입


 
 
 
 
*
 
i
n
d
e
x
 
옵
션
은
 
왼
쪽
의
 
쭉
 
적
혀
있
는
 
0
~
1
1
5
0
6
3
 
숫
자
들
을
 
의
미
하
며
,
 
이
 
숫
자
를
 
c
o
l
u
m
n
에
 
포
함
할
건
지
에
 
대
한
 
부
분
입
니
다
.


 
 
 
 
*
 
F
a
l
s
e
로
 
설
정
하
지
 
않
으
면
 
d
e
f
a
u
l
t
로
 
`
i
n
d
e
x
 
=
 
T
r
u
e
`
로
 
설
정
되
어
 
있
기
 
때
문
에
 
i
n
d
e
x
가
 
c
o
l
u
m
n
으
로
 
포
함
되
게
 
됩
니
다
.


 
 
 
 
*
 
해
당
 
대
회
의
 
제
출
 
양
식
에
 
i
n
d
e
x
는
 
포
함
되
지
 
않
았
으
므
로
 
i
n
d
e
x
를
 
만
들
지
 
않
기
 
위
해
 
`
i
n
d
e
x
 
=
 
F
a
l
s
e
`
라
는
 
옵
션
을
 
넣
어
줍
니
다
.



```python
s
u
b
.
t
o
_
c
s
v
(
'
s
u
b
.
c
s
v
'
,
 
i
n
d
e
x
 
=
 
F
a
l
s
e
)
 
#
 
i
n
d
e
x
 
=
 
0
```

-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-
-




다
음
 
포
스
팅
에
서
는
 
유
명
한
 
대
회
 
중
 
하
나
인
 
[
B
i
k
e
 
S
h
a
r
i
n
g
 
D
e
m
a
n
d
]
(
h
t
t
p
s
:
/
/
w
w
w
.
k
a
g
g
l
e
.
c
o
m
/
c
/
b
i
k
e
-
s
h
a
r
i
n
g
-
d
e
m
a
n
d
/
o
v
e
r
v
i
e
w
)
를
 
다
뤄
보
도
록
 
하
겠
습
니
다
.

