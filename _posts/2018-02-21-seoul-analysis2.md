---
title: 서울시 학령인구 시각화 2부
category: MISC
excerpt: |
  서울시 학령인구 시각화 2부
feature_text: |
  <h1 style="color:white;font-weight:bold;font-style:normal;font-size:66px">
  서울시 학령인구 시각화 2부
  </h1>
feature_image: "https://i.imgur.com/kG4cv7r.jpg"
---


<br>
<h3> <center><a href="https://haawron.github.io/misc/2018/02/21/seoul-analysis/">
서울시 학령인구 시각화 1부</a>
</center> </h3><br>


과외를 구하기 위해서 (ㅋㅋㅋㅋ) 서울시 학령인구(고등학생) 데이터를 시각화하는 중이다.
1부에서는 예제로 연습만 하다가 끝났다.  
2부에서는 이제 내가 원하는 데이터로 히트맵을 만들고 이미지에 글씨도 삽입할 것이다.

일단 [서울 열린데이터 광장]에서 데이터를 받아왔다. 하지만 데이터 상태가 별로 안 좋다.

{% include figure.html image="https://haawron.github.io/assets/Seoul/구명이_한글.png" position="center" caption="구명이 한글" %}

> 구 이름이 한글로 되어 있다.

참고로 밑의 그림이 베이스인데, 각 path들의 id는 불행하게도 영어로 되어있다. 각 데이터와 path를 매핑시켜
주려면 구의 영어이름과 한글이름을 매핑시켜줘야 한다.

{% include figure.html image="https://haawron.github.io/assets/Seoul/Seoul_districts.svg" position="center" caption="밑그림" %}

{% include figure.html image="https://haawron.github.io/assets/Seoul/svg파일_id.png" position="center" caption="여기는 영어" %}

그럼 이제 할 일은 당연히 노가다

```python
kor_to_eng = {
    '강남구': 'Gangnam-gu',
    '강동구': 'Gangdong-gu',
    '강북구': 'Gangbuk-gu',
    '강서구': 'Gangseo-gu',
    '관악구': 'Gwanak-gu',
    '광진구': 'Gwangjin-gu',
    '구로구': 'Guro-gu',
    '금천구': 'Geumcheon-gu',
    '노원구': 'Nowon-gu',
    '도봉구': 'Dobong-gu',
    '동대문구': 'Dongdaemun-gu',
    '동작구': 'Dongjak-gu',
    '마포구': 'Mapo-gu',
    '서대문구': 'Seodaemun-gu',
    '서초구': 'Seocho-gu',
    '성동구': 'Seongdong-gu',
    '성북구': 'Seongbuk-gu',
    '송파구': 'Songpa-gu',
    '양천구': 'Yangcheon-gu',
    '영등포구': 'Yeongdeungpo-gu_1_',
    '용산구': 'Yongsan-gu',
    '은평구': 'Eunpyeong-gu',
    '종로구': 'Jongno-gu',
    '중구': 'Jung-gu',
    '중랑구': 'Jungnang-gu'
}
# 막상 하니까 금방 한다.
```

나머지는 다 똑같고

{% include figure.html image="https://haawron.github.io/assets/Seoul/out_high_students1.svg" position="center" caption="글씨가 없으니 밋밋하다." %}

여기까지 만들 수 있다.

- - - - - -

이제 각 구명을 손으로 넣어줘야 한다.
다행히 Adobe Illustrator에서 svg 파일을 직접 수정할 수 있다.

{% include figure.html image="https://haawron.github.io/assets/Seoul/AI로_수정.png" position="center" caption="AI로 svg파일 수정" %}

일일이 수작업으로 구 이름을 달아줬다.
뭔가 코딩으로 해결할 방법이 있을 것 같지만 더 상상하기 싫다.  
집에서 나눔바른고딕으로 만들어 왔는데 여기 컴퓨터에서는 폰트가 없어서 폰트가 꺠져부렸다.
그럴 줄 알고 글씨도 path로 만들어 저장해놨다.

{% include figure.html image="https://haawron.github.io/assets/Seoul/out_high_students3.svg" position="center" caption="드디어 완성!!!" %}

~~텍스트를 텍스트로 저장하면 구글에서 검색도 되고 드래그도 가능하지만 그냥 path로 저장하면 당연히 안 된다.~~  
둘 다 안 된다. 글씨 깨지냐 안 깨지냐만 보면 된다.

- - - - - -

이제 이걸로 뭘 알아낼 수 있는지 보자. 일단 내가 살고 있는 중구는 거의 흰색에 가깝다.
어제 구청에서 한 빅데이터 강의에서 생활인구[^1] 데이터를 보여줬는데 주간 생활인구 밀집도는 중구가 서울시 중 1위지만
야간 밀집도는 꼴찌였다. 그렇다. 중구에는 주거지가 적다. 과외가 안 구해지는 이유가 있었다. 주변을 살펴보면
성북구에 애들이 좀 많은 것 같다. 노려봐도 될 것 같다.  
그리고 강남쪽에 양천구, 강남구, 서초구에도 애들이 좀 있는데, 양천구는 너무 멀고
강남까지는 버스로 30~40분이니까 해볼만 할 것 같다.  

이젠 경쟁자가 문젠데, 3월에 개강하면 대학생들이 쭈욱 빠진다. 공대생들 시험이 시작되는 3월 말 쯤이 골든타임일
것으로 예상된다. 학교 시험이 없고 군대 걱정 없는 것을 적극적으로 어핋해야겠다.

이번 연구로 알아낸게 있다.
1. 그림을 파이썬(bs4)으로 그릴 수 있다.
2. 이거 하는 동안 수학 1도 안 썼다.

다음 연구는 수학도 써보고 노가다 그림을 파이썬으로 쉽게 그려보고 싶다.  
신경망 그림이 직접 그리는데 엄청 오래걸릴 것이다. 하지만 코딩으로 가능할 것 같다.

- - - - - -

아 그리고

{% include figure.html image="https://haawron.github.io/assets/Seoul/Seoul_districts_한글추가.svg" %}
{% include figure.html image="https://haawron.github.io/assets/Seoul/Seoul_districts_한글추가2.svg" %}

필요하면 써도 된다. 대신 내 블로그 홍보 조금만...  

- - - - - -

[서울 열린데이터 광장]:http://data.seoul.go.kr/
[^1]:특정 시간에 특정 위치에 있는 인구, 서울시에서는 아직 공개하지 않았다. 2018년 3월 3일에 정식 공개한다고 한다.
