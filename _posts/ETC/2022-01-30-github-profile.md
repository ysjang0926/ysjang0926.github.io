---
layout: post
title:  "[Github] 우당탕탕 Github Profile 꾸미기 (feat. README.md 작성)"
subtitle:   "github readme"
categories: etc
tags: etc_til github profile
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
먼저 [Simple Icons](https://simpleicons.org/)에서 `python`을 검색하여 색상코드를 확인합니다.
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

<br>
(+추가) <br>
구글링을 해보니 [해당 깃허브](https://github.com/Ileriayo/markdown-badges)에 정리가 잘되어있더라구요! 원하는 것을 골라서 복붙하시기만 하면 됩니다!
* 깃허브 주소 : [https://github.com/Ileriayo/markdown-badges](https://github.com/Ileriayo/markdown-badges)
![image](https://user-images.githubusercontent.com/54492747/151701725-47b91b1a-334a-44cc-9316-bc744fc5b2db.png)

---

### 2. 프로필 꾸미기(2) - 방문자수(조회수) 표시
> hits를 표시하여 방문자 수 확인하기

한국인이란 자고로 기록의 민족 아니겠습니까🔥 <br>
방문자수(조회수)를 확인하는 기능(`hits`)을 추가하면, Github에 방문한 사람수를 확인할 수 있습니다. <br>

여러 hits 옵션이 있는데, 저는 대표적으로 쓰이는 옵션을 사용하였으며 [TODAY/TOTAL]을 확인할 수 있습니다. <br>

먼저 [hits 홈페이지](https://hits.seeyoufarm.com/)로 접속한 후, Github 주소(URL)를 복사하여 **TARGET URL**에 입력해줍니다.<br>
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

### 2. 프로필 꾸미기(3) - 문구 쓰기
> 가운데 정렬 + 글자크기 조절

내가 작성하고 싶은 글을 쓰고 싶을 때는 html 또는 markdown 쓰는 것처럼 작성하면 됩니다.<br>
하지만 저는 이와 같은 기본적인 내용을 몰라서 버벅거렸는데, HOXY 저와 같이 가운데 정렬 또는 header 기능을 쓰고 싶으신 분들을 위해 간단히 설명을 적어보겠습니다.

#### 1) 가운데 정렬하기
태그에 속성이라는 것을 이용해서 여러가지 특성을 부여할 수 있으며, `<태그속성 = 값>` 형식으로 사용됩니다. <br>
문자를 정렬하려면 align 속성을 사용하면 되고 align은 각 값에 따로 가운데, 오른쪽, 왼쪽으로 정렬하도록 `center`, `right`, `left`를 사용할 수 있습니다. <br> 
가운데 정렬을 할 때에는 <p> 태그에 center 정렬 속성을 줘서 내가 원하는 글을 추가하면 되며, 예시는 아래와 같습니다. 
```
<p align="center">
🚀 저는 4년차 분석가이며, 항상 우주의 별 먼지와 같은 존재라고 생각합니다. 👩‍🚀
</p>
```

위와 같이 작성했다면 다음과 같이 가운데 정렬 형식으로 글이 작성된 것을 확인할 수 있습니다.
![image](https://user-images.githubusercontent.com/54492747/151700608-8cfbce3a-35b8-4b39-bb25-22da72cbaccc.png)


#### 2) 글자크기 조절
글자 크기를 조절하는 <H> 태그는 총 6단계로 설정되어 있으며, 태그 안의 숫자가 커질수록 글자 크기가 작아집니다. <br>
또한 <H> 태그에 center 정렬 속성을 줘서 내가 원하는 글을 추가하면 되며, 예시는 아래와 같습니다. 
```
<h1 align="center"> 🛠 Tech Stack 🛠 </h1>
<h2 align="center"> 🛠 Tech Stack 🛠 </h2>
<h3 align="center"> 🛠 Tech Stack 🛠 </h3>
<h4 align="center"> 🛠 Tech Stack 🛠 </h4>
<h5 align="center"> 🛠 Tech Stack 🛠 </h5>
<h6 align="center"> 🛠 Tech Stack 🛠 </h6>
```

위와 같이 작성했다면 다음과 같이 6단계에 맞게 글이 작성된 것을 확인할 수 있습니다. (크기 비교!)
![image](https://user-images.githubusercontent.com/54492747/151701051-838bc562-84b0-499a-a266-dfd38bc9ff8d.png)


---

### 2. 프로필 꾸미기(4) - Github Stats
> 나의 Github Stats 확인하기 (feat.B+부터 시작하는 후한 등급 순서)

[Github Stats Card](https://github.com/anuraghazra/github-readme-stats)를 통해서 Stats를 추가하는 방법도 있습니다. <br>
아래의 깃허브를 클릭하여 원하는 부분을 골라서 복사해서 붙여넣기를 해주면 됩니다.
* 깃허브 주소 : [https://github.com/anuraghazra/github-readme-stats](https://github.com/anuraghazra/github-readme-stats)

<br>

이때 해야하는 것은 1) `username`부분을 본인의 Github 계정명으로 바꿔주고, 2) `theme` 부분에는 원하는 테마 옵션을 넣는 것입니다.

#### 1) Github 사용자명 변경
아래 코드를 복사해서 markdown 파일에 붙여넣으면 되는 아주 간단한 방법입니다. `?username=` 속성의 값을 Github 계정의 사용자명(닉네임)으로 바꿔줍니다.
```
[![Anurag's GitHub stats](https://github-readme-stats.vercel.app/api?username=anuraghazra)](https://github.com/anuraghazra/github-readme-stats)
```

#### 2) 테마 화면 설정
다양한 테마별 화면이 제공되니, 마음에 드는 것을 골라서 `theme=`부분에 넣어주면 됩니다.
* 테마 : [https://github.com/anuraghazra/github-readme-stats/blob/master/themes/README.md](https://github.com/anuraghazra/github-readme-stats/blob/master/themes/README.md)
![image](https://user-images.githubusercontent.com/54492747/151702823-45bc11ee-2e98-4837-956f-648df7b47510.png)

```
![Anurag's GitHub stats](https://github-readme-stats.vercel.app/api?username=anuraghazra&show_icons=true&theme=radical)
```

<br>

제 껄로 적용하면 다음과 같이 제 Github의 Stats가 표시되는 것을 확인할 수 있습니다. 부끄럽게도 18번 밖에 커밋을 안했네요😂 앞으로 더욱더 열심히 쓰는 것을 다짐하며...!
```
<p align="center"> 
  <img src="https://github-readme-stats.vercel.app/api?username=ysjang0926&theme=vue&show_icons=true"/></a>
</p>
```
![image](https://user-images.githubusercontent.com/54492747/151701820-f5ac5bba-1f4e-46f0-a99c-183800f119a9.png)

랭크는 **S+**(상위 1%), **S**(상위 25%), **A++**(상위 45%), **A+**(상위 60%), 그리고 **B+**(전체)로 구성되어 있습니다. (B+부터 시작하는 매우 후한 등급 매기기 아닌가요?🤣) <br>
랭크의 경우 커밋의 수(commits), 기여도(contribution), 이슈의 수(issues), 즐겨찾기(star), 작업내용 반영 요청(Pull Request), 팔로워 수, 그리고 보유 중인 저장소 등의 항목들에 대해 누적 분포 함수를 이용해 계산된다고 합니다. <br>

----

### 3. 그 외 옵션
제 Github Profile에 적용한 내용은 위와 같은데, 이것 말고도 정말 많은 옵션들이 있더라구요! <br>
혹시 도움되실까하여 제가 찾은 선에서 공유드립니다!

#### 1) solved.ac 티어 표시
만약 백준을 자주 쓴다면, 아래 라이브러리를 활용해서 solved.ac를 백준과 연동하여 자신의 등급을 표시해줄 수도 있습니다.
* Github 링크 : [https://github.com/mazassumnida/mazassumnida](https://github.com/mazassumnida/mazassumnida)
* 참고 블로그 : [https://madplay.github.io/post/design-github-profile-using-readme-md](https://madplay.github.io/post/design-github-profile-using-readme-md)<br>
예시는 아래와 같습니다.<br>
![image](https://user-images.githubusercontent.com/54492747/151703470-5b965c8e-6f5e-4fa2-b8f5-99e1fe840d29.png)

#### 2) Daily 코딩 시간 기록
내가 아침, 오전, 오후, 밤 중 어떤 시간에 가장 커밋을 많이 하는지 손쉽게 기록할 수 있는 오픈소스입니다. 하지만 이것은 Github Profile에서 적용하는 것이 아니라 pinned repo로 추가할 수 있다고 하니 참고 부탁드립니다.
* Github 링크 : [https://github.com/techinpark/productive-box](https://github.com/techinpark/productive-box)
* 참고 블로그 : [https://fernando.kr/develop/2020-05-02-github-gist-posting/](https://fernando.kr/develop/2020-05-02-github-gist-posting/)<br>
예시는 아래와 같습니다.<br>
![image](https://user-images.githubusercontent.com/54492747/151703640-6960c454-704c-407e-b97c-6e93f09f579e.png)


---

간단한 Profile 작성에도 이렇게 시간이 오래 걸릴줄이야😅 부디 이 포스팅이 Github Profile 작성을 하시는데 도움이 되었으면 좋겠네요! <br>
이 글을 읽으시는 분들 모두 맘에 드시는 Github Profile 만드시길 바라며 이만..!🐱‍👓

----

### ✔ 참고한 블로그
* [Github Profile 꾸미기](http://blog.cowkite.com/blog/2102241544/)
* [[Github] README.md로 Github 프로필 꾸미기](https://excited-hyun.tistory.com/132)
* [[GitHub] 내가 보려고 정리하는 GitHub README 작성법 + 꾸미기 꿀팁](https://mini-min-dev.tistory.com/56)
