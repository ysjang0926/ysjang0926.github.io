---
layout: post
title:  "[Github] 우당탕탕 Github Profile 꾸미기 (feat. README.md 작성)"
subtitle:   "github readme"
categories: etc
tags: github profile
comments: true
---

* 깃헙을 쓰는 사람이라면, "깃헙 이쁘게 꾸며보고 싶다!"라는 생각을 한번 정도는 하지 않나요?
* 깔끔하고 심플하게 만드는 것이 목표라서 제 취향으로 간단하게 정리해보았습니다! 생각보다 너무 쉬워요!

----

오랜만에 깃헙 블로그 글을 쓰기 위해서 깃헙을 들어왔더니, 갑자기 텅 비어있는 제 메인 화면을 꾸미고 싶은 생각이 들어서 github profile 만드는 법을 찾아 정리해보았습니다. <br>
(원래 할일이 있지만 하기 싫어서 이런 소소한 작업 재밌어하는거 저만 그런거 아니죠?👀)

<br>

### Github Profile
일반적으로 **README.md**로 Github Profile을 작성하며, README는 자신의 프로젝트를 소개하는 문서를 의미합니다. <br>
md는 markdown의 약자이다보니, README.md 파일은 markdown 문법으로 작성하게 됩니다.

Github Profile에는 사람마다 다르지만, 보통 자신을 소개하는 내용을 넣게 되며 자신에 대한 간단한 소개, 스킬셋 기술, 커밋 통계 등을 넣는 편입니다. <br>
나를 대표할 수 있는 또다른 얼굴(?)이라 할 수 있는 Github이기에, 이러한 Github Profile을 이용해서 자신을 소개하는 것도 참 좋은 것 같습니다.

전 극한의 J(MBTI)라 이런 기록들을 너무 좋아하기도 하고, <br>
게을러서 작성을 많이 하지는 않는지라(😭) 이리저리 찾아본 괜찮았던 것들 중 저한테 필요한 부분 위주로 들고와서 꾸며보았습니다. 

제 최종 프로필은 아래와 같으며, 우측 공간에 작성한 Profile이 생기게 됩니다.
* 제 깃헙 주소 : [https://github.com/ysjang0926](https://github.com/ysjang0926)

![image](https://user-images.githubusercontent.com/54492747/151700036-22a4cea4-50ff-4ec8-bf6a-75c88e58b1bd.png)

---

### 1. 프로필 생성하기
> 내 username과 동일한 새로운 repository 생성

자신의 Github username과 동일한 이름의 repository를 생성하면, Github Profile을 작성할 수 있습니다. (GitHub 이스터에그 중 하나라고 하네요!)<br>

**Github 접속 > Repository > New 클릭 > 신규 repository 생성 > 본인의 username과 동일하게 입력 > Create a new repository 클릭**

위의 순서로 repository를 만들면, 아래와 같이 "You found a secret!"라는 문구와 함께 README.md 파일로 Github Profile을 작성할 수 있음을 알려줍니다.<br>
* Profile 입력을 위해 **Public**으로 지정해야한다고 합니다.
* 저는 `Add a README file` 옵션을 활성화하여 README.md 파일이 함께 생성되도록 하였습니다. (직접 만들어도 된다고 하니 참고 부탁드립니다.) <br>
![image](https://user-images.githubusercontent.com/54492747/148634844-cf9b871a-03fe-4fba-9ab5-4f80dda0c67b.png)<br>

위와 같이 입력한 다음 [Create a new repository]를 클릭하여 repository를 생성하면, 위와 같이 기본으로 "Hi there 👋"가 입력된 README 파일이 자동 생성됩니다.<br>
우측을 보면 "ysjang0926/ysjang0926 is a special repository. Its README.md will appear on your public profile!"라는 문구와 함께 [Edit Readme]와 [Visit Profile] 버튼이 있습니다. <br>
![image](https://user-images.githubusercontent.com/54492747/148634929-f973fda5-1801-4a07-ba6c-6deb0bdf30de.png)<br>

[Visit Profile]를 클릭하면, 나의 메인 화면으로 가게 되어 위의 README.md 파일과 같은 내용으로 프로필 영역이 생성된 것을 볼 수 있습니다. <br>
![image](https://user-images.githubusercontent.com/54492747/148635069-c511bb2a-04bc-4171-b8c7-7793ce5100f5.png)<br>

이제 생성된 README.md 파일을 자신의 취향에 맞게 수정하여 Girhub Prifile을 작성하면 됩니다.🤟

---

### 2. 프로필 꾸미기(1) - 뱃지 삽입
> 간단한 소개와 스킬셋 기술을 위해 `Shields.io`를 사용해서 뱃지(Badge) 삽입하기

본문 내용으로 먼저 자신에 대한 간단한 소개, 스킬셋 기술을 작성하기 위해 뱃지를 넣었습니다.<br>

보통 두가지 방법이 있다고 하는데(이미 만들어져 있는 깃허브 링크에서 그대로 들고오는 방법 & Shields.it를 사용하는 방법), <br>
저는 아이콘과 컬러를 커스터마이징하고 싶어서 [Shields.io](https://shields.io/)를 사용하였습니다! <br>

`https://img.shields.io/badge/<LABEL>-<MESSAGE>-<COLOR>` 형태로 파라미터 값을 넣으면 원하는 모양의 뱃지 이미지가 생성되며, 이때 `logo`, `logoColor`, `labelColor` 등을 커스터마이징할 수 있습니다.<br>
무슨 말인지 모르겠죠? 저도 그랬습니다😂 포기하지말고 저와 함께 천천히 따라해보아요!

<br>

`https://img.shields.io/badge/`뒤에 `작성하고싶은 단어-색상?-스타일옵션` 순으로 입력하면 됩니다.
* 색상코드와 사용가능한 로고는 [Simple Icons](https://simpleicons.org/)에서 확인하여 손쉽게 사용할 수 있습니다.<br>

예를 들어 파이썬 로고와 ‘Python’ 문구가 들어간 파란색의 뱃지를 생성하고 싶으면,<br>
먼저 Simple Icons(https://simpleicons.org/)에서 `python`을 검색하여 색상코드를 확인합니다.
* 색상코드 : #3766AB
* 로고 : Python <br>
![image](https://user-images.githubusercontent.com/54492747/148636281-4e85c6f7-3ec3-4943-8bf3-6a92e87ca820.png) <br>

그런 다음 자신이 원하는 형식으로 아래와 같이 작성하면 됩니다.
* 작성하고싶은 단어(=Python) : `Python`
* 색상(=파이썬 파란색) : `3766AB`
* 스타일옵션(=일반) : `flat-square`
* 로고(=Python로고) : `Python`
* 로고색(=흰색) : `white` <br>
![image](https://user-images.githubusercontent.com/54492747/151700073-94f78a9a-80ec-4b23-aa4a-7cef76ee756d.png)

```
<img src="https://img.shields.io/badge/Python-3766AB?style=flat-square&logo=Python&logoColor=white"/>
```

<br>
위와 같이 작성했다면 다음과 같이 원하는 형식의 Python 뱃지를 성공적으로 생성할 수 있습니다.<br>
<img src="https://img.shields.io/badge/Python-3766AB?style=flat-square&logo=Python&logoColor=white"/>

---

### 2. 프로필 꾸미기(2) - 방문자수(조회수) 표시
> hits를 표시하여 방문자 수 확인하기

한국인이란 자고로 기록의 민족 아니겠습니까🔥 <br>
방문자수(조회수)를 확인하는 기능(`hits`)을 추가하면, Github에 방문한 사람수를 확인할 수 있습니다. <br>

여러 hits 옵션이 있는데, 저는 대표적으로 쓰이는 옵션을 사용하였으며 [TODAY/TOTAL]을 확인할 수 있습니다. <br>

먼저 hits 홈페이지(https://hits.seeyoufarm.com/)로 접속한 후, Github 주소(URL)를 복사하여 **TARGET URL**에 입력해줍니다.<br>
그런 다음 원하는 옵션(title, count 색상)을 설정하면 아래에 자동으로 MARKDOWN, HTML LINK 등이 생성됩니다. <br>
![image](https://user-images.githubusercontent.com/54492747/148637495-cc08753f-e8c4-4ba0-94a3-94e9411f1699.png) <br>

제공된 MARKDOWN을 복사하여 README.md에 그대로 복붙해주면 아래와 같이 생성됩니다. <br>
[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Fysjang0926&count_bg=%23D7D265&title_bg=%23252222&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com) <br>

이때 가운데에 위치하고 싶다면, `div align=center`를 사용하면 됩니다.
```
<div align=center>	
  
[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Fysjang0926&count_bg=%23D7D265&title_bg=%23252222&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com)
  
</div>
```

---

### 2. 프로필 꾸미기(3) - 방문자수(조회수) 표시
