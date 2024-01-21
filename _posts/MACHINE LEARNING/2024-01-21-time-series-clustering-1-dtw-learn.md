---
layout: post
title:  "Time Series Clustering(1) - Dynamic Time Clustering(DTW)"
subtitle:   "Time Series Clustering with Dynamic Time Clustering"
categories: data
tags: ml
comments: true
use_math: true
---

* 이번 글에서는 시계열 데이터 클러스터링에 대해 정리해 보았습니다.
* 이론 위주로 다루었고, 논문 리뷰와 데이터 실습은는 다음 포스팅에서 진행하겠습니다.(원하는 데이터 못구함😭)
* 많은 연산을 필요로 하는 DTW의 단점을 해결할 수 있는 알고리즘(tadpole,k-shape clustering)에 대해서도 리뷰하였습니다.

---

# ⏳ Time-series Invariances
시계열 데이터는 기존의 다변량 데이터과 어떤 차이점이 있을까요? 바로 **시간 개념이 포함**된다는 것입니다. <br>
따라서 시계열 데이터를 분석을 함에 있어, 시계열적인 특성(scale and translate invariance, shift invariance 등)을 고려해야 합니다. 
*  **scaling and translation invariances**
	*  scale invariance : 시계열 데이터의 스케일을 아무리 바꾸어도 출력값은 변하지 않는다는 것
	* translation invariance : 시계열 데이터를 이동시켜도 출력값은 변하지 않는다는 것
	* scaling and translation invariances : transformation을 했을 때, 물체의 distance measure가 변하지 않는다는 것
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/9a90c96c-997a-4242-baaa-734a0ff8151d)
* **shift invariance**
	* 시점에 대한 왜곡에 따라 발생하는 misalingment에 대해 강건함
	* 즉, 두 시계열 데이터가 있을 때의 시간축에 따른 시간차 패턴을 고려해야하는 것
	* misalignment : 축 정렬 오차
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/69260203-7956-436d-a686-d079554e4705)
* **uniform scaling invariance**
	* 두 시계열 데이터의 lenth가 서로 다를 때, length가 같아야 한다는 것
 ![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/07b6227e-9181-447c-b27c-bc11548b4ece)
* **complexity invariance**
	* 두 시계열 데이터의 complexity가 다를 때, noise 정도가 다를 때에 대해 강건함
	* 즉, 두 시계열 데이터의 complexity가 다를 때를 고려해야하는 것
 ![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/3fc3a871-2775-4ba5-b48d-fef4a4cfc877)

<br>

시계열 데이터의 특징을 반영한 방법 중 하나는 **similarity(유사도) 기반의 알고리즘이 주로 사용되는 Clustering 방법**이 있습니다. 
* 시계열에서의 clustering : 비슷한 시계열 데이터끼리 묶는 것
* clustering 목표
	* 같은 cluster에 속하는 데이터는 최대한 비슷하게, 다른 cluster에 속하는 데이터는 최대한 다르게 cluster 나누기 <br>

Clustering에서 **거리**라는 단어가 등장하는데요. 이때 대표적으로 많이 쓰이는 **distance measure**로는 **유클리디언 거리(ED; Euclidean Distance)**가 있습니다.
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/b50d981a-79e5-450a-b082-94391a989645) <br>

하지만 유클리디언 거리는 시계열 데이터가 맞지 않습니다. 그 이유로는 시계열 특징 중 하나인 **시간 축에 따른 shift invariance (misalignment)**를 고려해야하기 때문입니다. 아래의 예시를 통해 설명하도록 하겠습니다. <br>
빨간색과 파란색 시계열은 데이터 간 lag나 tranformation이 있지만,  어느정도 비슷한 패턴을 가지고 있는 것으로 보입니다. 유클리디안 거리(ED)로 두 시계열 간 거리를 재게 된다면, 왼쪽 그림처럼 시계열 특성을 반영하지 못하고 서로 다른 데이터라는 결과값이 나올 것입니다.
* ED : 시계열 데이터 특성을 반영하지 못함
* DTW : 시계열 데이터 특성을 반영함
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/5b13632d-b6c1-4ebb-b4ac-b56a5a637641) <br>

그렇기 때문에 오른쪽 그림처럼 시계열의 패턴을 고려하면서, 관측치 간의 거리를 정의하고 그 거리를 계산할 수 있는 방법이 따로 필요하게 됩니다.
* 각 시계열 데이터간의 유사도를 잘 정의하는 것이 중요

이때 이것을 해결하기 위해 등장하게 되는 **DTW(Dymamic time warping)**는 **misalignment와 time series length를 고려한 유사도 산출**이 가능한  시계열 간 유사도를 측정하는 방법입니다.

<br>

# Time-series Distance Measures

## 📐 DTW(Dynamic Time Warping)
DTW는 단어 뜻 그대로 **‘dynamic하게 시계열을 비틀어서’** 두 데이터 간의 유사성을 판단할 수 있게 해주는 알고리즘입니다. 
* misalignment & time series length 고려
* 두 시계열 데이터 사이의 alignment를 조정하여 '어그러진' 시계열 축을 맞춰주며, 전체적인 패턴의 유사성을 측정 <br>

DTW의 핵심은 **두 개의 시계열의 유사도를 토대로 Distance matrix를 만든 이후, 유사도의 총합이 최소가 되는 길을 찾는 것**입니다.
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/2641ef7e-e709-4c04-8538-0ebe1d318e0c)

즉, **Distance를 계산하여 Cost matrix를 산출**하여 , 임의의 window 내에서 **거리의 총합이 최소화되도록 Path를 구하여 유사도를 측정**합니다. 이 계산법을 통해 두 시계열 간 misalignment 해결할 수 있습니다.
* Cost matrix : 일종의 유사도 행렬 
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/adb2d057-8d33-47c2-b5ff-b51b217ce670)

<br>

갑자기 너무 어려워졌죠? 아래의 두 시계열 데이터 예시를 통해 설명하도록 하겠습니다. (seq=10)
* Time series 1 = [1,6,2,3,0,9,4,3,6,3]
* Time seires 2 = [1,3,4,9,8,2,1,5,7,3]
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/b20c59d6-b496-4853-ace7-1ba6b2870c60)

뭔가 비슷하면서도 다른 두 데이터인데, 일단 두 개의 시계열 데이터 길이가 같기 때문에 유클리디언 거리를 계산하자면 13.27입니다. <br>
DTW와 비교하도록 하겠습니다. 시계열 안의 sequence 포인트를 비교할 때 근처 값들을 비교하여 제일 짧은 값(선)을 그어준다고 생각하면 됩니다.
* ED : seq=2일 때의 두 데이터가 연결
* DTW : TS1 seq=3번 데이터가 TS2 seq=2번 데이터와 연결
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/f33ff28c-ed3d-4ff0-9518-d9d5d3d981d8)

그렇다면 TS1과 TS2의 10개의 데이터 간의 유사도를 산출하면 다음과 같습니다.
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/98728b43-3e78-40cd-ae45-3afa46e5c90b) <br>

처음부터 끝나는 step까지 총합(유사도)이 최소가 되는 path를 설정하면 됩니다. 아래의 핑크색 부분이 최종 경로이며, 모든 칸은 누적거리이기 때문에 마지막 칸의 값에 제곱근을 취한 값이 DTW 값이 됩니다.
* **DTW값** : root(15) = 3.87
* **ED값** : 13.27 → DTW는 ED의 약 1/3 정도 값
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/bf4213b6-5871-45a6-a1e4-cacc9660dfd2) <br>

이때 최소가 되는 path, 즉 **선의 거리가 비용(cost)**이 되는데요. 선이 길어질수록(=멀리 가야 할수록) 비용이 높아지게 됩니다. <br>
time step와 관측치의 개수에 따라 계산 시간이 기하 급수적으로 증가하게 되는거죠.
* O(n^2) time & space

<br>

이렇게 DTW를 사용하여, 시계열 데이터 간의 유사도를 산출하는 것은 매우 유용하게 사용됩니다. <br>
하지만 DTW는 그 자체의 연산량이 상당하기 때문에 **computational cost가 매우 높습니다**.  특히 관측치의 증가에 따라 계산 시간이 지수승으로 매우 크게 증가하게 됩니다. <br>
이러한 Time Complexity를 해결하기 위해, DTW를 보다 효율적으로 계산하고 time series 간 유사도를 잘 정의하여 clustering을 수행하고자 하는 다양한 기법들이 제안되었습니다.

<br>

## 📏 DTW 확장 방법 - Density Peak
연산량을 개선하기 위한 cDTW(Sakeo-Chiba Band), LB_Koegh(Lower bound of Keogh), Density Peak 등의 방법이 있으며, 이번 글에서는 **Density Peak**에 대해서만 간단하게 소개하도록 하겠습니다.
* cDTW : 연산을 효율적으로 하기 위하여 band를 줌
	* 누계가 최소가 되는 path 위주로만 탐색
* LB_Koeght : lower bound 개념을 도입하여 계산량을 줄임
	* DTW를 계산하지 않고, 유사한 효과를 얻기 위해 Boundary 개념을 도입
	* lower/upper bound를 넘어가는 부분에 대해서만 ED로 계산하여 비슷한 효과를 냄

<br>

**Density Peak**은 **밀도 기반의 군집화 방법**이며, 군집화를 하기 위해 데이터에서 2가지의 요약값을 추출합니다.
1. Local density
2. Minumum Distance from Points of Higher Density <br>

#### 1️⃣ Local Density
**각 데이터 값에 대해 cutoff distance(d_c)보다 작은 거리에 있는 관측치 수**입니다. <br>
아래의 그림에서 c는 각 데이터값의 index이고, cutoff distance에 의해 관측치 수를 구할 수 있습니다.
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/0fb68dc7-eeb8-4ce7-8426-2e1768fc264c)


#### 2️⃣ Minimum Distance from Points of Higher Density
**데이터의 Local density보다 높은 관측치까지의 거리 중 최소거리**입니다. <br>
아래의 그림에서 index=10와 index=21인 데이터값의 Local density를 구하도록 하겠습니다.
* index=10
	* index=10 데이터값의 Local density = 4
	* 4보다 높은 관측치끼리의 거리들을 구했을 때, index=5인 데이터와의 거리가 가장 최소거리 = 0.8
* index=21
	* index=21 데이터값의 Local density = 2
	* 2보다 높은 관측치끼리의 거리들을 구했을 때, index=1인 데이터와의 거리가 가장 최소거리 = 0.18
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/cac4aca7-d3b1-4b10-9338-2c5076444515) <br>

위에서 구한 두가지 요약값을 곱하여, 곱한 값에 대해 상위 K개를 군집의 중심으로 선택을 하게 됩니다.
* `think` local density가 높으면서 더 높은 density와의는 거리가 멀다
* 즉, 군집의 중심일 가능성 ↑
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/ccfe7216-16fe-4956-8870-ee97dfb7131e) <br>

Density Peak은 두 가지 요약값을 활용하여 Cluster를 이루기 때문에, 굉장히 간단하면서도 파워풀한 알고리즘입니다. <br>
하지만 Density Peak에서도 많은 하이퍼파라미터가 존재하기 때문에, 연산량이 발생할 수 밖에 없습니다. (완벽한 방법 X)
* Cutoff distance & Number of Center

<br>

이때 Times series 데이터가 적용 가능 하도록 Density Peak의 개념을 확장한 알고리즘이 **TADPole(Time-series Anytime DP)**입니다.

|   | **Density Peak**     | **TADPole**          |
|:---------:|:--------------------:|:--------------------:|
| Date type | Multivariate dataset | Time-series dataset  |
| Input     | Distance Matrix(ED)  | Distance Matrix(DTW) |

<br>

# 📈 TADPole Clustering Algorithm  
Data type이 **Time-series dataset**이고, Input이 **DTW**일 때, 두가지를 고려해야 합니다.
* 어떻게 DTW를 효율적으로 계산할 것인지
* 어떻게 Local density와 Minumum Distance from Points of Higher Density를 구할 것인지 <br>
 
### ⬛ 효율적인 DTW 계산 방법
효율적으로 DTW를 계산하는 방법 중 하나는, DTW 연산을 줄이기 위해 Boundary 개념을 도입하는 것입니다. Boundary를 넘어가는 부분에 대해서는 ED로 계산하는 것이죠.
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/594389fa-3992-416f-a275-7cb76eba22b2) <br>

Q와 C 시계열 데이터가 있다고 했을 때, U와 L은 Upper & Lower Bound가 됩니다.
* Lb_matrix = Boundary를 넘어가는 부분만 ED로 계산한 거리
* UB_matrix = 유클리디안 거리
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/b814e92c-0f4d-42b1-9f51-78fa9a99a431)

<br>

이때 기존 DTW의 연산을 피하기 위해, 새롭게 DTW 거리를 정의합니다. <br>
*  매시점의 DTW 거리는 **Lb_matrix ≤ DWT ≤UB_matirx**로 산출
* 새로운 방법으로 DTW 거리로 산출하여, 최종 DTW값을 산출
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/46727e66-7d0d-462b-b9a2-5d05f3261d86)

<br>

### ⬛ LD, MD 계산
#### (1) Local density
Local density는 각 데이터 값에 대해 cutoff distance(d_c)보다 작은 거리에 있는 관측치 수입니다. <br>
매시점의 DTW 거리가 Lb_matrix ≤ DTW ≤UB_matirx인 케이스 일 때, d_c보다 작은 거리에 있는 값을 캐치하면 됩니다.
![image](https://github.com/ysjang0926/ysjang0926.github.io/assets/54492747/2ada9fc2-20ba-42ac-bb13-f32e397bae6c)


#### (2) Minumum Distance from Points of Higher Density
> 해당 부분은 이해가 조금 되지 않아, 마무리 되는대로 내용을 추가하도록 하겠습니다.

<br>
-------
### Reference
* [Paparrizos, John, and Luis Gravano. "k-shape: Efficient and accurate clustering of time series." _Proceedings of the 2015 ACM SIGMOD international conference on management of data_. 2015.](https://www.researchgate.net/profile/John-Paparrizos/publication/303801193_k-Shape_Efficient_and_Accurate_Clustering_of_Time_Series/links/645d59654353ba3b3b5c3245/k-Shape-Efficient-and-Accurate-Clustering-of-Time-Series.pdf)
* [Time Series Clustering Algorithms](http://dmqm.korea.ac.kr/activity/seminar/245)
* [DTW로 시계열 클러스터링하기](https://pizzathiefz.github.io/posts/time-series-clustering-with-dtw/)
