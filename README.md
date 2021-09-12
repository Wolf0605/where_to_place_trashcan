# 광양시 수거기 설치

## 목차
+ 설치할 동은 어떻게 정할것이고 어디에 설치 할 것인가
+ 각 동 설치 수와 공원 면적을 DF 로 묶어서 보기
+ 쓰레기장 ,건물데이터를 가져와서 동마다 나눠주기
+ FOLIUM 으로 시각화한 공원, 쓰레기장, 건물분포도를 보고, 설치기준을 정하기
+ 직접 스카이뷰로 찍은것을 시각화

### 설치 기준 : 인구수
**생각나는게 인구 수**

1.인구수 데이터를 금호동제외하고  모두 뽑아오기 ( 금호동은 기기수 12개로 미리 정해줌)

2.그리고 인구수에 비례하게 뽑아본다

3.기기수가 총 50이라 금호동제외 38 이상이면 많은곳에서 한곳씩 뺀다
```python
for i in range(dong):
  percent += [(remove_gumho_df.iloc[i][1] * (machine / sum_2017))]
  a += [(round(remove_gumho_df.iloc[i][1] * (machine / sum_2017)))]

  if a[i] > 10:
    a[i] = a[i]-1
a.insert(10, 12)
```
이후 공원 데이터와 붙혀주면~~
![image](https://user-images.githubusercontent.com/82213429/132976917-7d7d9237-aa53-469d-a56e-7eca27387596.png)
대충 이런식으로 된다
### 어디에 설치할까?
**일단 경험을 살려보자**

쓰레기 자동 수거기를 어디서 많이 보았는가?? **공원!!**

선택한 이유는 그 이후에 자세히 찾으면 되는 법 ㅋㅋ..

1. 주택단지와 공원단지를 시각화해서 봤을때 주민이 많은곳 ( 인구수가 많은곳이 ) 공원이 많음
2. 통계 상 코로나 이후 공원을 다니는 사람 수 많이 증가
3. 쓰레기장 근처에 설치해도 큰 효과를 얻을 수 없다고 생각
Why ? 위 사진처럼 보듯이 쓰레기 한개씩 넣어서 갈리는 방식이다. 쓰레기 바구니들고 버리는데 저기 하나하나 넣을
사람은 없다고 생각함.
4. 신문  기사에 광양시 공원 이용객증가로 쓰레기가 많다더라

### 어디 설치할지 정해볼까?
**가진 데이터로 할 수 있는 만큼 시각화를 해본다**

1. 공원좌표
2. 건물 좌표
3. 공동주택 분리수거장 좌표
4. 동경계선 좌표

### 먼저 할 것
1. 전처리하여 공원을 동마다 나눌 것( 데이터 동이 이쁘게 나뉘지않아서 예외처리 해야한다)
2. 건물역시 동에 따라 나눌 것( 동좌표 경계와 엮을 줄 몰라서 수작없으로 사각형으로 나눔)
3. 데이터가 이쁘게 동별로 있어서( 따로 전처리 x)
4. ( **아직 미 구현** )

### 전처리를 했으면 시각화로 봐보기
**공원** 과 **빌딩**, **분리수거장** 을 시각화(Folim 을 통해) 하여 어디에 설치해야 좋을지 봐봄
![image](https://user-images.githubusercontent.com/82213429/132982820-d4553a4b-97e7-4957-b6df-a1f0468204e5.png)
![image](https://user-images.githubusercontent.com/82213429/132982836-114a9f67-17fe-49ac-b78a-8509b260ff60.png)

#### 생각보다 복잡하다

### 특징을 더 잡아서 선택지를 줄여야 겠다고 생각
1. 공원을 중심으로 주변에 건물 수가 많은 공원을 우선으로 뽑자( 기계 설치수보다 공원이 훨 많음 )
2. 이후 스카이뷰로 하나씩 훑어봐서 괜찮은 곳 선정(아파트 교차로, 운동시설, 상가 등)
3. 단 설치 가능한곳에 설치하고 전기를 끌어올 수 있어야함
```python
import math

def surround_park(park, 동빌딩):
  global apt_num
  apt_num = []
  for i in range(len(park)):
    distance = math.sqrt(park['공원면적'][i]) + 300
    apt = 0
    for x in range(len(동빌딩)):  
      if park['위도'][i] - distance/10**5  < 동빌딩[x][0] < park['위도'][i] + distance/10**5  and park['경도'][i] - distance/10**5 < 동빌딩[x][1] < park['경도'][i] + distance/10**5:
        apt += 1
    apt_num.append(apt)
  return apt_num
  ```
위에는 공원과 동 입력시 그 공원 주변 건물 수 뽑아줌

### 따로 처리해준 금호동
이건 **공원이 없**기 때문에 지도로 보면서 큼지막한 공원을 시각화 하고 다른것들과 마찬가지로 처리 해 주었다. 단, 뉴스 기사를 찾아보고 그린랜드 라는 곳은 관광객이 많이오는 곳이므로 2개를 설치 함
### 스카이뷰로 하나하나 보면서 괜찮은 곳 선정
![image](https://user-images.githubusercontent.com/82213429/132983289-3785a918-8c3b-4c87-b27c-08935e4d3290.png)
