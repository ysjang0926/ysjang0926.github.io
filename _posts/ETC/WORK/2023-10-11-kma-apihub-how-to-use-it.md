---
layout: post
title:  "[기상청 API허브 사용법] 과거 기상 관측 데이터 가져오기 (feat. Python) "
subtitle:   "Load historical weather observation data"
categories: etc
tags: etc_work
comments: true
use_math: true
---

- 이번 글에서는 기상청 API허브를 통해 데이터를 로드하는 방법을 정리해보았습니다.
- 아무래도 설비 장비가 외부에 있어서 외기온도에 영향을 많이 받기 때문에, 기상청 데이터가 매 분석마다 필요한 상황입니다.⛅
- 비록 최소 단위가 '시간'이지만 n년 이상의 데이터를 볼 때 유용하게 쓰이기에, Data I/F 하는 과정을 기록해보았으니 참고 부탁드립니다.

----------

제조 데이터 분석을 할 때 가장 활용도가 높은 공공 데이터는 무엇일까요?<br>
바로 **기상청 데이터**입니다!

제조 설비의 경우 외부에 위치해있는 케이스가 많이 때문에, 상온의 영향을 받기 쉽습니다. 그렇기 때문에 계절별, 분기별 외기온도에 따라 설비의 태그(센서)가 패턴을 가질 것이라고 추측할 수 있습니다. <br>
그렇기 때문에 분석 프로젝트가 진행될 때 설비 인자들 뿐만 아니라 외기온도, 습도, 대기압 등의 기상청 정보가 추가로 필요한 케이스가 많습니다.

이때 저희 팀의 경우, 2가지 케이스로 데이터를 적재하는 편인데요! (실제로 기상청에서도 다음의 두 형태로 데이터를 제공함) <br>
2가지 케이스의 장단점은 다음과 같습니다.

1. file(CSV)
	* 장점 : 원하는 기간을 설정하여 CSV 파일을 다운받아서 분석에 활용할 수 있음 (분당 데이터 가능)
	* 단점 : 한번에 받을 수 있는 데이터수가 한정적이기 때문에, 기간을 쪼개서 여러 개의 CSV 파일을 다운 받아야함
2. API(JSON, XML)
	* 장점 : 원하는 기간을 설정하여 DB에 바로 적재할 수 있음
	* 단점 : 최소 단위가 '시간' 데이터임 (분당 데이터 들고올 수 없음)

보통은 분석 데이터 기간이 짧을 시에는 분당 데이터를 활용하기 때문에 CSV file을 다운받아서 loop문을 통해 DB에 적재하는 편인데, <br>
2~3년 이상의 데이터의 경우에는 분당 데이터보다는 시간 or 일 단위 데이터를 활용하는 케이스도 있어서 API를 통해 DB를 적재하려고 합니다.

이번 포스팅에서는 데이터를 실시간으로 가져올 수 있는 방법인 API에 대해서 알아보도록 하겠습니다.

그렇다면 먼저 API란 무엇인지 간단하게 알아보도록 하겠습니다. <br>
API란 Application Programming Interface의 약어로, 간단히 말하자면 기기 간 통신을 통하여 데이터나 정보를 주고 받을 수 있는 것입니다. 데이터 전송 시에는 흔히 많이 쓰이는 구조로는 JSON과 XML이 있습니다.
* JSON(JavaScript Object Notation) : key-value 형태로 tree 구조를 이루고 있으며, 대부분의 데이터 형식을 다 담을 수 있다는 장점이 있어 아마 가장 많이 활용되는 자료구조임
* XML(Extensible Markup Language) : 다른 종류의 시스템, 특히 인터넷에 연결된 시스템끼리 데이터를 쉽게 주고받을 수 있게 한 HTML의 한계를 극복할 목적으로 만들어짐

그중에서 Open API는 누구나 사용할 수 있도록 공개된 API를 말하며, 하나의 웹사이트에서 자신이 가지고 있는 기능을 이용할 수 있도록 공개한 것입니다.
* 예시 : 네이버 지도, 구글 맵 등

그럼 이제 Python으로 기상청 API허브를 통해 데이터를 로드하는 작업을 진행해보겠습니다.

<br>

## 1️⃣ 기상청 API허브 소개
기상청은 누구나 손쉽게 방대한 기상기후데이터를 활용하여 새로운 가치를 창출하고자 API허브([(https://apihub.kma.go.kr/)](https://apihub.kma.go.kr/))를 운영하고 있습니다.

### ⛅ 콘텐츠 종류
기상청 API허브 홈페이지 메인 좌측을 보면 지상 관측자료, 해양 종합관측자료, 천리한 1호 데이터 파일 목록 등 다양한 날씨 정보가 제공됩니다. <br>
이때 과거 지상에서 관측되었던 일기현상, 기온, 강수, 바람 등의 날씨 데이터가 필요하다면 홈페이지에서 **지상관측**을 눌러 들어가면 됩니다.
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/7d671a70-f78f-4e5a-bfa2-ba71057eec8d)

지상 관측자료에도 다양한 관측 정보가 존재하며, 각 탭을 누르게 되면 설명 및 보유기간이 상세히 적혀있습니다.
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/2cc07347-2851-4fde-9b0e-be2809e87f08)

이번 예시에서는 **종관기상관측(ASOS)**로 진행하도록 하겠습니다.

<br>

## 2️⃣ 회원가입 및 사용자 API키
기상청 API허브에서 제공하는 API를 이용하기 위해서는 회원가입 후 인증키를 발급받아야 합니다.
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/93c2249d-8200-4086-ac30-cc9ece780fc3)

### 🔑 사용자 API Key
데이터를 받아오기 위해서는 사용자에 대한 정보가 필요하며, 이때 사용하는 것이 바로 사용자 개별로 부여되는 API Key입니다. <br>
발급받은 인증키는 로그인 후 우측 상단에 있는 **마이페이지**에서 확인할 수 있습니다. <br>
인증키는 가입회원 본인만 사용할 수 있으며, 양도 및 대여가 금지되기 때문에 외부로 유출되지 않도록 조심하는 것이 좋습니다.

![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/63aa8491-2797-4779-89b7-2c4ce5014213)

위 사진과 같이 개인 회원은 하루 최대 **2,000건** 호출할 수 있으며, 현재 몇 회 호출했는지도 확인 가능합니다. (기업 회원은 하루 최대 3,000건)

<br>

## 3️⃣ 콘텐츠 및 API 호출 URL 구조

제공하는 콘텐츠는 크게 3가지로 구성되어 있습니다.
1. 데이터 설명 part
	* 요소, 지점수, 보유기간, 생산주기 등에 대한 설명
	* URL 발행 및 API 샘플코드 열람 가능
2. API 호출 URL 정보 part
	* 데이터를 호출하기 위해 필요한 API 호출 URL
	* 'API 활용신청' 후 API 사용 가능
3. API 호출 요청인자 및 출력결과 part
	* 필수 입력 요청인자 설명
	* 테이블 변수 설명

![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/62190d45-4cee-4ee9-a0f2-bad08c0a9210)

시간대별 관측자료, 일별 관측자료 등 원하는 데이터의 API 호출 URL 정보를 사용하면 됩니다. <br>
이때 API 호출 URL 구조는 다음과 같습니다. 호출 입력인자와 사용자 인증키를 추가 파라미터로 입력해서 API를 요청하면 됩니다.
1. 도메인주소 및 API소스코드
	* 고정항목으로 변하지 않음
	* URL 중 '**?**' 바로 직전(**.php**)까지이며, 호출 입력인자와 '?'를 기준으로 구분함
2. 호출 입력인자(input parameter)
	* API 호출을 위한 필수 호출인자 입력 부분
	* 인자와 인자 사이는 '&'를 기준으로 구분
	* 아래 예시는 tm1에 201512110100, tm2에 201512140000, stn에 104, help에 1을 입력함
3. 사용자 인증키
	* API 서비스를 이용하기 위한 인증키

![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/f3dfe916-7a98-4d75-b62c-1b2303520b55)

호출 입력인자의 경우, 기상청 API허브에 잘 설명이 되어있기 때문에 참고하여 작성하면 됩니다. <br>
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/be936a13-a9b2-483c-8c3a-6e86d57a93ee)

<br>

## 4️⃣ 데이터 로드

파이썬(Python)으로 종관기상관측(ASOS)의 **시간자료(기간 조회)** API를 호출하여 **과거 한 시간 단위 지상 관측 날씨 데이터**를 로드해오겠습니다.

### ✅ Import Module
먼저 필요한 라이브러리를 호출합니다.
```python
import warnings
warnings.filterwarnings('ignore')

import pandas as pd
import requests
from datetime import datetime, timedelta
```

기본적으로 API를 호출하기 위하여 **requests**라는 python용 HTTP 모듈이 필요합니다. requests는 어떤 방식(method)의 HTTP 요청을 하느냐에 따라서 해당하는 이름의 method를 사용하면 됩니다. <br>
* `requests.get()` : GET Method
* `requests.post()` : POST Method
* `requests.put()` : PUT Method
* `requests.delete()` : DELETE Method

### ✅ Weather Function 
데이터 컬럼명을 지정해주고, 호출 입력인자인 시작시간/종료시간/지점번호/도움말/API 인증키를 입력하면 해당 기간의 날씨 데이터를 불러오는 function을 만듭니다.
* tm1 : 시작시간 또는 시작일 → **년월일시분*을 이어서 씀 (ex. 2023-01-01 10시 = 2301011000)
* tm2 : 종료시간 또는 종료일
* stn : 국내 지역 지점번호 (ex. 지점번호 152 = 울산 지역)

```python
# 데이터 컬럼명 지정 : Column Description 그대로 가져옴
col_name = ["TM","STN","WD","WS","GST_WD","GST_WS","GST_TM"
            ,"PA","PS","PT","PR","TEMP","TD","HM","PV"
            ,"RN","RN_DAY","RN_JUN","RN_INT","SD_HR3","SD_DAY","SD_TOT"
            ,"WC","WP","WW","CA_TOT","CA_MID","CH_MIN"
            ,"CT","CT_TOP","CT_MID","CT_LOW","VS","SS","SI","ST_GD"
            ,"TS","TE_005","TE_01","TE_02","TE_03","ST_SEA","WH","BF","IR","IX"]

# 날씨 데이터 function (load_weather)
def load_weather(tm1, tm2, stn):
    domain = "https://apihub.kma.go.kr/api/typ01/url/kma_sfctm3.php?"
    auth_key = "XXXXXXXXXXXXXXXX" # 부여받은 API Key 입력
    params = {'tm1' : tm1,
              'tm2' : tm2,
              'stn' : stn,
              'help' : 0, # 도움말 없음
              'authKey' : auth_key}

    response = requests.get(domain, params=params, verify=False)

    text = response.text
    text = text.split("\n")[4:-2]

    df = pd.DataFrame(text)[0].str.split(expand=True)
    df.columns = col_name

    return df
```

이 코드는 지역번호 108번, 2021년 3월 8일 00시부터 05시까지 발생한 ASOS데이터를 받아오라는 의미이고, 아래와 같이 무사히 실행됨을 확인할 수 있습니다.
```python
load_weather(202103080000, 202103080500, 108)
```
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/f8079835-2897-4a22-956e-5236cb666e99)

<br>

### ✅ 변수들 설명(헤더정보)
호출된 API의 출력 변수들의 설명, 즉 헤더정보를 보고 싶을 때는 `help` 옵션을 조정하면 됩니다. <br>
* help = 0 설정 : 헤더정보 미표출
* help = 1 설정 : 헤더정보 표출
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/310197ca-6327-4b6a-8135-15e8b7bf24d7)


<br>

### ✅ 지상관측 지점정보
`stn`은 국내 지점번호이며, `tn=152`는 지점번호가 152인 울산 지역에 대한 날씨를 불러오는 코드입니다. <br>
만약 전체 지역을 모두 불러오고 싶다면 `stn=''`로 전달하면 되며, 전체 97개 지점에 대한 날씨정보가 모두 return 됩니다.
* 98x6 = 582 rows
```python
load_weather(202303080000, 202303080500, '')
```
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/bd95e2e4-b04b-45b7-9ad9-f1bcf002ee71)

전체 지역이 아니라 특정 지역의 stn 지점번호를 알고 싶을 때는, 지상관측의 가장 마지막 TAB인 **지상관측 지점정보**를 클릭하여 API 호출을 통해 확인하면 됩니다.
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/b0bc8365-8d99-4c66-8610-ccee87cd2c35)

```python
url = "https://apihub.kma.go.kr/api/typ01/url/stn_inf.php"
auth_key = "y1b1RctfSY-W9UXLXzmP0A"
params = {'inf' : 'SFC',
          'stn' : '',
          'authKey' : auth_key}

response = requests.get(url, params=params, verify=False)

text = response.text
text = text.replace("#","").split("\n")
text = text[1:-2]

for i, l in enumerate(text):
    l = l.strip().replace(" ",",").split(',')
    l = [i for i in l if i != '']
    l = ",".join(l)
    text[i] = l


col_name = text[0].split(',')

df_stn = pd.DataFrame(text[2:])[0].str.split(",",expand=True)
df_stn = df_stn.drop(14, axis=1)
df_stn.columns = col_name
df_stn
```

![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/0f78f750-892c-4e77-86e1-2d76c043a80d)

총 97개의 지역명과 지점번호이 존재하니, 위의 코드를 복사하여 실행시킨 뒤 보고 싶은 지역의 지점번호를 찾아서 날씨 데이터를 불러옵니다. <br>
저의 경우에는 특정 지역들 위주로만 사용해서 지점번호를 외우고 있거나, csv 파일로 저장해서 열람하고 있습니다.
```python
df_stn[df_stn["STN_KO"] == '구미']
```
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/a6fdac56-ee9f-47a0-9ca4-18e649b63c85)

<br>

### ✅ 720시간 이상의 데이터 생성 (feat. While Loop문)
이때 주의해야할 점은 데이터를 호출할 때 tm2(종료시간)으로부터 **최대 720시간 전**까지만 불러올 수 있습니다.
그렇기 때문에 tm1을 21년, tm2를 23년으로 설정하고 데이터를 불러도 tm2 기준으로 720개만 데이터가 호출되는 것을 확인할 수 있습니다.
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/1b7208a3-c1b0-4c90-9250-c4cb07c74964)

720시간 이상 기간의 날씨 데이터를 호출하고 싶다면, loop문을 통해 720시간 단위로 기간을 쪼개어 불러오면 됩니다. <br>
위에서 만든 날씨 데이터 function(load_weather)를 활용하여 2021-03-08 00:00:00부터 2023-05-15 09:00:00까지 구미지역 날씨 데이터를 불러오는 예시 코드는 다음과 같습니다.
```python
dates = pd.date_range('2021-03-08 00:00:00', '2023-05-15 09:00:00', freq = 'H')
dates = [str(d).replace("-","").replace(" ","").replace(":","")[:-2] for d in dates]

df_all = pd.DataFrame()
d_i = 0
while d_i <= len(dates):
    start_time = dates[d_i]
    if d_i + 719 >= len(dates):
        end_time = dates[-1]
    else:
        end_time = dates[d_i+719]
    d_i += 720

    df = load_weather(start_time, end_time, 279)
    df_all = pd.concat([df_all, df])

df_all
```
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/e4ce2dc0-7eee-474d-9192-1283058716c6)

### ✅ 데이터 전처리




## 5️⃣ DB 적재










<br>

### Reference
* [[Python] 공공데이터 오픈 API를 활용한 기상청 ASOS 데이터 파싱하기](https://jonsyou.tistory.com/71)
* [기상자료개방포털 Open-API 활용하기 - 파이썬초보자도 할 수 있다!](https://blog.naver.com/kma_131/222850549606)
* [[Pyhon - Crawling] API 활용하여 과거 기상 관측 데이터 불러오기](https://leedakyeong.tistory.com/entry/Python-Crawling-API-%ED%99%9C%EC%9A%A9%ED%95%98%EC%97%AC-%EA%B3%BC%EA%B1%B0-%EA%B8%B0%EC%83%81-%EA%B4%80%EC%B8%A1-%EB%8D%B0%EC%9D%B4%ED%84%B0-%EB%B6%88%EB%9F%AC%EC%98%A4%EA%B8%B0)

--------------------------------------------------------

1. **Papers With Code** [(https://paperswithcode.com/)](https://paperswithcode.com/)
	* 연구자들이 발표한 **논문과 관련 코드 및 데이터를 공유**할 수 있는 플랫폼
		* **분야별 최신 논문**을 볼 수 있음
		* 다양한 구현 코드들에 대한 GitHub 링크 & 사용 라이브러리(TensorFlow, PyTorch) 표시 &논문 구현 코드에 대한 참고 링크 有
		* 제공하고 있는 데이터set별 SOTA(State-of-the-art) 성능을 보여주는 모델들을 확인할 수 있는 리더보드 有
	* 추천 대상자👩‍💻 : 딥러닝 논문 구현 능력을 향상 시키고 싶은 사람
		* 딥러닝 논문 구현 능력을 향상 시키고 싶다면, 1)논문 읽기와 2)다양한 논문 구현 코드 분석이 필수적입니다. 이때 다른 사람들이 논문을 어떻게 구현했는지 참조하고 싶을 때, Papers With Code 사이트를 이용하면 많은 도움을 받을 수 있습니다.

2. **[유튜브] 딥러닝논문읽기모임** [(https://youtube.com/@user-ow3gm9zd1b)](https://youtube.com/@user-ow3gm9zd1b)
	* AI를 연구하고 있는 대학원생, 교수, 연구원 등이 돌아가며 선정 논문을 설명하는 내용을 담은 **논문 리뷰 영상**
		* **논문에서 중요한 부분이 무엇인지 핵심을 잘 짚어주며, 발표자료 또한 일부 제공**해줌
	* 추천 대상자👩‍💻 : 논문 읽는 것에 어려움이 있어, 관련 논문에 대한 핵심내용이 궁금한 사람
		* 딥러닝은 발전 속도가 대단히 빠르기 때문에, 계속하여 수많은 논문들이 쏟아져 나옵니다. 처음 접하는 분야의 논문을 이해하기 위해선, 그에 대한 선행 연구와 동기를 파악해야 해당 논문의 기술을 자세히 파악할 수 있습니다. 그렇기 때문에 관심 있는 분야의 기본적인 컨셉과 중요한 부분을 캐치하기 위해, 해당 유튜브 채널을 참고하면 무척 도움이 됩니다.

3. **고려대학교 산업경영공학부 DMQA 연구실** [(http://dmqm.korea.ac.kr/)](http://dmqm.korea.ac.kr/)
	* 데이터마이닝 및 품질애널리틱스 (Data Mining & Quality Analytics: DMQA) 연구실 홈페이지
	* 연구실 내부 구성원들의 **연구/프로젝트/세미나 내용**
		* 'Seminar' 카테고리 : 다양한 주제를 바탕으로 진행된 세미나 내용 열람 가능하며, 발표 자료 및 영상이 공유되어 참고하기 유익함
		* 'Projects' 카테고리 : 연구실에서 진행한 프로젝트 컨셉/목표/결과 요약이 열람 가능하여, 각 프로젝트에 대한 아이디어를 얻기 유익함 
	* 추천 대상자👩‍💻 : 대학원 연구생들이 세미나 및 프로젝트 내용을 참고하고 싶은 사람
		* 대학원 연구생들이 다양한 주제로 오픈 세미나를 진행하고 있기 때문에, 관심 있는 주제가 있다면 참고하기에 정말 좋은 곳입니다. 양질의 내용을 정리하여, 심지어 영상과 pdf 자료로도 제공하고 있기 때문에 거의 떠먹여(?) 준다고 할 수 있습니다.


4. **고려대학교 산업경영공학부 DSBA연구실** [(http://dsba.korea.ac.kr/)](http://dsba.korea.ac.kr/)
	* 데이터과학 및 비즈니스 어낼리틱스 (Data Science & Business Analytics: DSBA) 연구실 홈페이지
	* 연구실 내부 구성원들의 **연구/프로젝트/세미나 내용**
		* 'Seminar' 카테고리 : 논문 리뷰로 진행된 세미나 내용 열람 가능하며, 발표 자료 및 영상이 공유되어 참고하기 유익함
		* 'PROJECTS' 카테고리 : 연구실에서 진행한 프로젝트 컨셉/목표/결과 요약이 열람 가능하여, 각 프로젝트에 대한 아이디어를 얻기 유익함 
	* 추천 대상자👩‍💻 : 대학원 연구생들이 세미나 및 프로젝트 내용을 참고하고 싶은 사람
		* DMQA 연구실과 더불어 DSBA 연구실 또한 대학원 연구생들이 다양한 주제로 오픈 세미나를 진행하고 있으며, 이에 대한 유용한 자료들(영상, pdf)을 제공하고 있습니다. 관심 있는 주제가 있다면, DMQA와 DSBA 연구실 모두 찾아보시는 것을 추천드립니다.

<br>

## 📌 기술 블로그

많은 분들이 데이터 분석을 공부하는 이유는, 내가 현재 업무에서 겪고 있는 어려움과 개선하고 싶은 부분을 데이터를 통해 해결하고 싶기 때문입니다. 그렇지만 실제 데이터를 통해 분석을 진행하게 되면 온라인 강의나 책에서 나오는 내용처럼 결과가 아름답지 않습니다.😭 이건 저도 현재 업무에서 고민하고 있는 부분이며, 다른 많은 회사에서도 마찬가지일 것이라 생각합니다. <br>
그렇기 때문에 일부 회사들은 회사가 해결하는 문제 및 기술력 공유를 위해 기술 블로그를 운영하고 있으며, **회사 차원에서 어떤 문제를 어떤 방식으로 해결하고 있는지**와 **해당 문제를 해결하는 과정**을 소개하고 있습니다.  개인 공부 블로그 대비 검증된 포스팅이기 때문에, 글을 보는 사람들은 간접적으로 회사 업무를 경험하며 회사의 기술력을 알 수 있는 장점이 있습니다. 회사 내 기술 조직 구성원의 성장을 목적으로 진행되는 것이다보니 양질의 글이 많이 있기 때문에, 저도 시간이 될 때마다 흥미있는 주제의 글들을 보고 있는 편입니다.

1. **NCSOFT DANBI** [(https://danbi-ncsoft.github.io/)](https://danbi-ncsoft.github.io/)
2. **LINE Engineering** [(https://engineering.linecorp.com/ko/blog)](https://engineering.linecorp.com/ko/blog)
3. **카카오엔터프라이즈 Tech&** [(https://tech.kakaoenterprise.com/)](https://tech.kakaoenterprise.com/)
4. **쿠팡 Engineering** [(https://medium.com/coupang-engineering)](https://medium.com/coupang-engineering)
5. **SOCAR Tech Blog** [(https://tech.socarcorp.kr/)](https://tech.socarcorp.kr/)

<br>

### Reference
*  [[딥러닝] PR12 딥러닝(AI) 논문읽기 모임 발표 영상 (YouTube)](https://blog.naver.com/yo2dh/221282722400)
* [쏘카에서 기술 블로그를 운영하는 방법](https://tech.socarcorp.kr/data/2023/02/15/how-to-organize-tech-blog.html)
* [[Python] 공공데이터 오픈 API를 활용한 기상청 ASOS 데이터 파싱하기](https://jonsyou.tistory.com/71)

----------------------------

### 🔜 Think
통계학 전공을 했었지만, 머신러닝과 딥러닝은 대학교 3, 4학년과 대학원 때부터 접하여 다소 생소하지만 흥미롭게 다가왔습니다. 수많은 양질의 논문들을 모두 보면서 척척 이해하면 좋겠지만, 능력이 부족하여 다른 분들이 정리해주신 좋은 자료들을 보며 공부할 수 있습니다! 다시 한번 이 자리를 빌어서 제가 참고했던 모든 양질의 글을 쓰신 분들께 감사의 인사를 드립니다👍

<br>

저는 개인적으로 제가 하는 업무가 좋은 의사결정을 하도록 돕는 것이라고 생각합니다. 데이터를 기반으로 성공 확률이 높은 의사결정을 지속적으로 하기 위해서는, 데이터 정의 / 데이터 전처리 / 분석 모델링 등에 대한 다양한 파트의 지식이 필요하죠! <br>
이때 분석 모델링을 진행할 때 이해도가 높을수록 좋지만 해당 도메인에 대한 전공자가 아닌 이상 한계가 있기 때문에, 분석을 진행함에 있어 아이디어적인 스킬이 매우 중요하다고 생각합니다. 이런 아이디어 스킬은 한순간에 생기는 것이 아니라 계속 누적되어 나타나는 것이구요.😎 <br>
저도 엉덩이가 무거운 편은 아니라 꾸준히 논문을 다 뜯어보면서 공부는 못하지만, 업무에서 아이디어를 얻기 위해 출퇴근 또는 시간 있을 때 간단하게 읽을 수 있는 부분을 보고자 노력하고 있습니다. <br>
만약 데이터분석을 공부하고 싶어서 해당 포스팅을 방문하셨다면, 힘찬 응원의 박수를 보내드립니다!👏 저 또한 많이 부족하기에, 저희 함께 조금씩 어제보다 더 나은 오늘을 보낼 수 있길 바랍니다.
