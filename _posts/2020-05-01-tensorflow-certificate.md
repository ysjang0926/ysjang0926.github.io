---
layout: post
title:  "Tensorflow Certificate 취득 후기"
subtitle:   "Tensorflow Certificate"
categories: data
tags: dl
comments: true
---

**Tensorflow Certificate**란 구글에서 공인하는 일종의 텐서플로우 자격증입니다. 본 인증 시험은 2020년 3월 12일에 런칭되었으며 온라인으로 진행됩니다. <br>

회사에서 직무 관련 자격증 취득 시 경비 지원을 해주기 때문에, 최근 TensorFlow 공부를 조금씩 시작한 겸 얼른 취득해서 끝내자는 생각이 들어 준비해보았습니다. 회사 카드로 결제하여 시험을 보는 것이라서 불합격 시 불합격 사유를 제출하고(...😂) 다시 재시험을 보아야했기에, 지난 일주일 동안 나름 열심히 공부하고 준비했던 것 같습니다.

![tensorflow bedge](https://user-images.githubusercontent.com/54492747/80908887-9ab1b080-8d5e-11ea-9be3-f513221ec3b2.png)

-----

# 1. Tensorflow Certificate 소개
시험 소개 핸드북인 [TF_Certificate_Candidate_Handbook](https://www.tensorflow.org/site-assets/downloads/marketing/cert/TF_Certificate_Candidate_Handbook.pdf)을 참고하면 아래와 같습니다.

## 1.1 사전 준비사항
- **응시료** : $100
	- 해외 카드 결제가 가능해야 하므로, 사전에 가능한 카드를 준비해야 합니다.
- **시험 시간** : 5시간
	- 5시간이 경과하기 전에 submit 버튼을 누르지 않으면, 검사가 자동으로 수행됩니다.
	- 이때 시험을 치르는 동안 이미 제출하고 테스트한 문제에 대해서만 등급이 매겨지게 됩니다.
- **시험 방법** : 온라인
- **시험 환경** : 인터넷 가능 / 개인 노트북 준비 / PyCharm IDE
	- 참고 : [Setting_Up_TF_Developer_Certificate_Exam](https://www.tensorflow.org/site-assets/downloads/marketing/cert/Setting_Up_TF_Developer_Certificate_Exam.pdf)
	- 본 시험은 PyCharm IDE 사항을 지원하는 모든 컴퓨터에서 언제든지 시험을 볼 수 있습니다.
	- PyCharm 환경에서 TensorFlow를 사용하여 모델을 구현해야하는 온라인 테스트이기 때문에, 시험을 치기 위해 PyCharm IDE를 사용하여 TensorFlow Exam 플러그인을 설치해야 합니다.
		- PyCharm에서 `File>Settings...>Plugins>Google Developers Certification`를 통해 설치
- **준비물** : 여권 또는 영어로 된 운전면허증, 노트북 카메라
	- 사진, 서명 및 전체 이름이 포함된 만료되지 않은 기본 ID가 필요하기 때문에, 저는 여권 스캔본을 준비하였습니다.
	- 시험 시작할 때, ID와 본인 셀카가 필요하기 때문에 사전에 스캔본과 카메라(웹캠)를 준비하시면 됩니다.
- **유효기간** : 36개월
- **재시험 규정**
	- 첫번째 불합격시 14일 이후 응시 가능
	- 두번째 불합격시 2개월 후 응시 가능
	- 세번째 불합격시 1년 후 응시 가능

## 1.2 시험 문제
TensorFlow 2.x를 사용하여 5시간 내에 총 **5개의 모델**을 구축하여 문제를 해결해야 합니다. 선형 회귀와 같은 기초적인 모델부터 시작하여 이미지 분류, 텍스트 분류, 시계열 분류과 같이 다양한 문제들로 이루어집니다.
- Category 1: **Basic  model**
- Category 2: **Model from learning dataset**
- Category 3: **Image classification**
	- Convolutional Neural Network with real-world image dataset
- Category 4: **Natural language processing (NLP)**
	- NLP Text Classification with real-world text dataset 
- Category 5: **Time series, sequences and predictions**
	- Sequence Model with real-world numeric dataset

저는 Image Classification까지는 해봤지만 NLP와 Time Series는 Tensorflow를 통해 한 경험이 없어서 걱정을 많이 했습니다. 하지만 구글에서 마련한 강의 커리큘럼을 통해 문제를 어느정도 파악할 수 있고, 생각보다 시험이 어렵지 않기 때문에 괜찮았습니다. 만약 시험을 보실 분들 중에 자연어나 시계열 분야에 익숙하지 않으시더라도 크게 걱정하지 않으셔도 될 것 같습니다!

## 1.3 합격 기준
시험 완료까지 5시간이 주어지며, ~~반드시 90%의 점수를 획득해야 합격할 수 있습니다.~~ <br>
(기존의 핸드북에는 `Candidates are given five hours to complete the exam and must achieve a score of 90%.`와 같이 되어있었는데, 최근 update된 핸드북에는 점수에 관한 내용이 없네요.😢 해당 부분은 다시 update되는대로 수정하도록 하겠습니다.) <br>

제가 시험을 보았을 때는, 한 문제당 submit을 하게 되면 5점 만점으로 점수를 부여받게 되어 있었습니다. 저는 5문제 모두 5/5를 받아서 통과하였지만, 구글링을 통해 후기를 본 결과 어떤 분은 한문제 정도는 4/5를 받아도 무사히 통과를 하신 것을 확인할 수 있었습니다. 한 문제당 점수가 명확하게 적혀있지 않으니, 시험 보실 분들은 되도록 모든 문제에서 5/5를 받으시는 것을 추천드립니다.

<br>

# 2. 시험 준비
시험 준비 내용을 소개하기에 앞서, 먼저 저는 Python보다는 R이 더 익숙하고 TensorFlow를 사용한지는 1년도 되지 않아 강의 위주로 참고를 많이 했습니다. 만약 자격증 취득을 하려고 하시는 분들 중 어느정도 TensorFlow를 사용할 줄 아시고 익숙한 분들이라면, 아래의 내용은 참고 정도로만 하시면 될 것 같습니다.🤗 <br>  
저는 해당 시험을 준비하기 위해 1) 공식 페이지에서 추천하는 구글이 만든 Coursera 강좌인 [Tensorflow In Practice](https://www.coursera.org/specializations/tensorflow-in-practice)와 2) [테디노트 블로그](https://teddylee777.github.io/) 로부터 많은 도움을 받았습니다. <br>

## 2.1 Tensorflow In Practice 강의
선형 회귀부터 시계열 분류까지 다양한 문제들로 구성되어있기 때문에, 처음에는 어느 정도로 공부를 하고 준비해야할지 막막했습니다. 하지만 [공식 홈페이지](https://www.tensorflow.org/certificate)에서 시험을 준비할 수 있도록 친절하게 [강의](https://www.coursera.org/specializations/tensorflow-in-practice)를 소개해주고 있습니다.
![internet_ai](https://user-images.githubusercontent.com/54492747/80911199-dd2fb900-8d6f-11ea-8816-87e2a2c00a42.png)

강의는 아래와 같이 TensorFlow 기초, 이미지 분류, 자연어 처리, 시계열 분석에 맞춰서 구성되어 있으며, tensorflow 2.0를 이용한 모델을 구현하는 것을 설명해주는 것으로 강의가 진행됩니다. 자격증 취득을 떠나서 tensorflow 2.0을 배우고 싶은 분들에게 좋을 것 같으며, 저 같은 경우에는 익숙한 내용은 1.5배속 정도로 빠르게 듣고 넘기고 듣고 싶은 내용 위주로 보았습니다.
![강좌](https://user-images.githubusercontent.com/54492747/80911257-37c91500-8d70-11ea-9f88-fa0eaad61545.JPG)

시험은 해당 강의에서 다룬 내용의 범위를 벗어나지 않고, 심지어 일부 문제는 거의 똑같이(?) 나온다고 볼 수 있습니다. 그렇기 때문에 만약 시간적 여유가 있으시다면 강의를 들으시는 것을 추천드리며, 실습 내용을 충분히 복습하셨다면 쉽게 통과하실 것으로 예상됩니다.

## 2.2 테디노트 블로그
해당 [블로그](https://teddylee777.github.io/)에는 TensorFlow와 자격증에 대해 자세히 설명해주고 있어서 많은 도움을 받았습니다.  특히 TensorFlow Datasets의 경우 처음 접하는 것이다보니 데이터를 불러오고 그에 대한 구조를 파악하는 것이 어려웠는데, [TensorFlow Datasets API 활용법](https://teddylee777.github.io/tensorflow/tfds-datasets) 포스팅을 통해 쉽게 이해할 수 있었습니다.
![블로그](https://user-images.githubusercontent.com/54492747/80911931-7a8cec00-8d74-11ea-9222-ca12ec77f6aa.JPG)

또한 직접 자격증을 취득하셨기 때문에 자격증에 대한 이해도도 높고 환경세팅과 출제 문제를 잘 아시기 때문에 그에 대한 꿀팁(?)들이 많이 있습니다. 최근 [텐서플로우 자격 인증시험 단기취득 과정](https://festa.io/events/974) 교육을 오픈하셨으니, 단기적으로 자격증이 필요하신 분들은 참고하셔서 강의를 들으시는 것도 추천드립니다.

<br>

# 3. 자격증 취득
발표는 시험을 다 본 후 최대 2주 이내에 최종 합격 여부를 메일을 통해 알려준다고 하지만, 대부분 일주일 내로 모두 합격 여부 메일을 받는 것 같습니다. 저는 시험을 보자마자 바로 합격 메일을 받았으며, digital credential 취득까지 약 2~3일 정도 걸렸습니다.

## 3.1 합격 여부 메일
어떤 분 후기 글을 보면 합격 여부 메일까지 1~2일이 걸렸다고 하였는데, 저의 경우 시험을 마치자마자 바로 합격 메일을 받았습니다. 아마 최근 자격증 시험을 보는 사람들의 수요가 높아지다보니 빠르게 결과를 내서 통보해주는 것 같습니다.

![합격메일](https://user-images.githubusercontent.com/54492747/80912942-a069bf00-8d7b-11ea-8808-fe40f5ad94a8.JPG)

합격 메일은 위와 같이 오며, [TensorFlow Certificate Network](https://developers.google.com/certification/directory/tensorflow) 등록을 위해 구글 설문지 작성 링크를 보내줍니다. 설문 작성을 하게 되면 2주 안에 TensorFlow Network에 등록되며, 저의 경우 digital credential 취득과 같이 약 2~3일 정도 걸렸습니다.

## 3.2 Digital Credential 메일
시험 합격자에게는 Digital Credential 인증서가 메일로 오게 되는데 약 2~3일 정도 걸렸습니다. 
![자격메일](https://user-images.githubusercontent.com/54492747/80913140-4ec23400-8d7d-11ea-9bb1-175028641791.JPG)

위와 같이 메일이 오며, `View my credential`을 클릭하게 되면 아래와 같이 인증서 pdf과 badge를 받을 수 있습니다. 또한 링크드인을 통해 공식적으로 기입할 수 있도록 정보를 제공해줍니다.
![TensorFlow Developer Certificate_JYS](https://user-images.githubusercontent.com/54492747/80913202-cabc7c00-8d7d-11ea-9dae-399842e4189f.JPG)

<br>

# 4. 시험 후기
* 저는 TensorFlow 초보자여서 무척 긴장하며 시험에 임했는데, 시험 난이도가 크게 높지 않기 때문에 만약 평소에 많이 쓰시는 분들이라면 쉽게 취득하실 수 있을 것이라 생각합니다.
	* 제 개인적인 생각이지만, 관련 경력 및 경험이 있으신 분들은 굳이 이 시험을 볼 필요가 없을 것 같다는 생각이 들었습니다.
* PyCharm과 Python에 익숙해야 합니다.
	* Jupyter Notebook으로만 python을 하다보니, 시험을 보기 위해 처음으로 Pycharm을 사용했는데 가상환경부터 잡는 것부터 난관이었습니다.
	* numpy, tensorflow와 같은 package 설치 등 기본적인 준비는 미리 해두는 것을 추천드립니다.
* 자격증 취득 목표도 좋지만, 단기적으로 TensorFlow가 무엇이고 어떻게 작동되는지 공부해보고 싶으신 분들에게 해당 자격증을 준비하는 것이 무척 도움될 수 있을 것 같다고 생각합니다.
	* 저도 인강과 블로그를 통해 기본적인 내용을 학습할 수 있었고, 자격증 취득 후 더욱 흥미가 생겨 추가적으로 공부를 진행할 예정입니다.

<br>

------
오랜만에 공부할 겸 회사에서 자격증 경비 지원을 해준다고 하여 자격증 공부를 해보았습니다.😁 여러 자격증들이 후보였는데, TensorFlow Certificate가 최근(2020년 3월 12일)에 나왔기도 했고 TensorFlow 관련 자격증은 처음이다보니 궁금함이 앞서 선택하였습니다. 짧은 기간이었지만 재밌게 잘 공부하고, 무사히 잘 취득한 것 같아서 기분이 좋네요! <br>

저는 주위에서 취업을 위해 분석 관련 자격증 취득에 관련해서 고민을 하는 친구들이나 후배들한테 없는 것보다는 낫겠지만 굳이 추천하고 싶진 않다고 얘기를 하는 편입니다. 오히려 꾸준히 공부한 과정을 깃헙 같은 곳에 남겨놓는 것을 더 추천하는 편이며, 이를 통해 기업에 충분히 열정과 성실을 어필할 수 있을 것이라고 얘기합니다. (저도 이것을 잘 관리 못 했던 편이라 더 아쉬워서 말하는 것 같기도 합니다.😂 저도 계속 노력하고자 하는 부분이라서요!) 왜냐하면 자격증 한 줄에 비해 현업에 있는 분들은 더 많은 시행착오와 다양한 경험을 하시기 때문입니다. 기초 지식을 갖추고 있다면 자격증 공부에 시간을 투자하는 것보다 공모전 등에 도전하는 것이 더 좋다고 생각을 합니다. 하지만 그에 반면 처음 분석을 접하고 공부하는 사람이거나 취업이 목적이 아닌 분석 공부를 하는 겸 따려고 준비 중인 사람의 경우에는 공부 진행과 동기부여를 위해 취득하는 것도 나쁘지 않다고 생각합니다. <br>
그래서 만약 TensorFlow에 대해 관심을 가지고 시작하고자 하시는 분들이라면, TensorFlow 기초 부분과 자격증 내용을 공부하고 시험을 보시는 것도 좋을 것 같습니다! 하지만 개인적으로는 이미 Python과 TensorFlow를 잘 알고 있는데, 단지 자격증 취득을 위해 시험을 보시려고 하는거면 조금 비추입니다!($100가 적은 돈은 아니니까요😢) 텐서플로우 개발자 자격증 보다는 초보 개발자 느낌이라고 할까요..? <br>

제 TensorFlow Certificate 취득 후기는 이와 같구요! 만약 시험을 준비하시는 분에게 제 작은 후기가 조금이나마 도움이 되었으면 좋겠습니다🤗 모두 좋은 결과 있으시길 바라며, 긴 글 읽어주셔서 감사합니다.
