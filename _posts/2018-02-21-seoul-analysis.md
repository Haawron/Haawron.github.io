---
title: 분석 서울시 학령인구 시각화
category: MISC
excerpt: |
  [분석] 서울시 학령인구 시각화
feature_text: |
  <h1 style="color:white;font-weight:bold;font-style:normal;font-size:66px">
  서울시 학령인구 시각화
  </h1>
feature_image: "https://i.imgur.com/kG4cv7r.jpg"
---

요즘 과외 구하기가 너무 힘들다. 분당에 살았을 때는 그래도 좀 구했었는데 서울 오고 나서는 여태까지 한 명도
못 구했다. 이게 내가 과외를 구하는건지 구걸하고 다니는건지 의심스러울 정도였다. 너무 안 구해져서
답답하던 와중에 서울시에서 데이터를 공개했다는 걸 알게 됐다. 그것도 아주 옛날부터...  

<br>
<h3> <center><a href="http://data.seoul.go.kr/">
서울 열린데이터 광장</a>
</center> </h3><br>

뒤져보다보니 추계인구이긴 하지만 구별 고등학생 인구가 나와있었다. 하지만 시각화가 되어있지 않아 매우 불편쓰  
그래서 시각화하는 방법을 찾아보기로 했다. 역시 먼저 해본 사람이 있었다. [여기]에 매우 잘 나와있었다.  
근데 색이 좀 맘에 들지 않았다. 이전에 히트맵 이미지에서는 수치에 따라 색조가 변하면 알아보기 힘들다는
글을 본 적이 있다. 확실히 수치에 따라 빨주노초파남보로 색이 변하면 수치의 강약이 한 눈에 안 들어온다.
채도 변화로 표현하면 가장 자연스럽다고 한다. 링크의 예제에서는 구별 고령화 인구 데이터를 사용했다.
색깔만 바꿔서 나도 따라 만들어봤다.

예제에서는 일일이 색을 입력해줬는데 나는 그러기 귀찮아서 gradient를 구해주는 코드를 추가했다.
처음에 RGB를 10진법으로 바꿔서 `np.linspace` 해주면서 삽질 좀 했다.
각 채널을 따로따로 gradient 시켜줘야 한다.

```python
def hex_to_rgb(color_in_hex : int):
    hex_str = hex(color_in_hex)[2:]
    return np.array(tuple(int(hex_str[i:i+2], 16) for i in (0, 2, 4))).astype(np.int32)

def rgb_to_hex(rgb):
    return '0x{:02x}{:02x}{:02x}'.format(*rgb)

def gradient_colors(start, end, steps):
    start, end = hex_to_rgb(start), hex_to_rgb(end)
    delta = (end - start) / (steps - 1)
    rgbs = tuple(np.int32(start + delta * i) for i in range(steps))
    return tuple('#' + rgb_to_hex(rgb)[2:] for rgb in rgbs)
```

구간을 일정하게 쪼개기 위해 normalize(?)도 해줬다.
데이터를 0~1로 만드는 걸 normalize라고 하는게 맞는지 기억이 안 난다.

```python
max_value = max(counts_only)
senior_count = dict((key, value / max_value) for key, value in senior_count.items())
```

그리고 BeautifulSoup가 업데이트 됐는지 `soup.prettify()`의 동작이 조금 달라졌다.
예제는 svg만 프린트 해주는데 나는 html 문서 전체가 프린트된다.
삽질이 더 필요하다.

```python
with open('Seoul_districts.svg', 'r') as svg:
    soup = BeautifulSoup(svg.read(), 'lxml')
    svg.seek(0)  # 커서 맨 앞으로
    header = svg.readlines()[:3]
```

여기서 `soup`객체만 따지 말고 `header`도 같이 따줘야 된다.
나중에 다시 써줘야 되기 때문이다.   
나머진 뭐 다 똑같다.  

대신에 보기 싫은 `if`문을 없앴다.

```python
color_class = levels - 1 if count == 1.0 else int(count // (1 / levels))
# 음 만족스러워
```

그림 왼쪽위에 색깔 별 수치 표시도 있으면 있어보일 것 같다.

```python
# 네모 박스 넣기
for i in range(levels):
    size = 70
    xx = yy = 50
    xx = '{}'.format(xx + size * i)
    new_rect = soup.new_tag('rect', x=xx, y=str(yy), width=str(size), height=str(size), stroke="white", fill=colors[i])
    svg_tag.append(new_rect)
```

그리고 이제 svg 문서를 작성해주면 된다.
문서를 좀 멍청하게 써서 깔끔해보이게 하려면 약간의 수정을 좀 더 해주면 된다.

```python
with open('Seoul_Seniors/out_seniors.svg', 'w') as op:
    op.writelines(header)
    op.write(str(svg_tag).replace('></path', '/').replace('></rect', '/'))  # 약간의 수정
```

전체 코드다.  
todo : 이거 접었다 폈다 하는 거 만들어보자, 코드 글씨 너무 큼.

```python
import csv
from bs4 import BeautifulSoup
import numpy as np


def hex_to_rgb(color_in_hex : int):
    hex_str = hex(color_in_hex)[2:]
    return np.array(tuple(int(hex_str[i:i+2], 16) for i in (0, 2, 4))).astype(np.int32)


def rgb_to_hex(rgb):
    return '0x{:02x}{:02x}{:02x}'.format(*rgb)


def gradient_colors(start, end, steps):
    start, end = hex_to_rgb(start), hex_to_rgb(end)
    delta = (end - start) / (steps - 1)
    rgbs = tuple(np.int32(start + delta * i) for i in range(steps))
    return tuple('#' + rgb_to_hex(rgb)[2:] for rgb in rgbs)


senior_count = {}
counts_only = []
past_header = False
levels = 6

with open('Seoul_Seniors/senior_lsf.csv', 'r') as csvfile:
    spamreader = csv.reader(csvfile, delimiter=',')
    for row in spamreader:
        try:
            unique = row[0]  # 구 이름
            count = float(row[1].strip())  # 숫자, 띄어쓰기 벗기고, float으로 만듦
            senior_count[unique] = count  # dict에 넣음
            counts_only.append(count)  # 얻다 쓰는거지? >> 안 씀 ㅋㅋㅋ
        except:  # 헤더에서는 float하다가 에러남
            pass

max_value = max(counts_only)
senior_count = dict((key, value / max_value) for key, value in senior_count.items())  # normalize to 0 ~ 1

with open('Seoul_districts.svg', 'r') as svg:
    soup = BeautifulSoup(svg.read(), 'lxml')
    svg.seek(0)  # 커서 맨 앞으로
    header = svg.readlines()[:3]

paths = soup.findAll('path')  # 태그 path의 attribute들의 dict의 list임 [{id: ..., d: ...}, {id: ...}, ...]
color_start, color_end = 0xFFF5E7, 0xFF9400
colors = gradient_colors(start=0xFFF5E7, end=0xFF9400, steps=levels)

for p in paths:  # 각 path태그마다 (p는 tag 객체임)
    if p['id']:  # 태그의 id가 있으면
        count = senior_count[p['id']]  # 해당 구의 숫자를 받아와서

        color_class = levels - 1 if count == 1.0 else int(count // (1 / levels))
        color = colors[color_class]  # 색을 받아옴
        p['fill'] = color  # 색을 넣음 (path 태그에 fill 태그를 추가함; 변수 paths를 수정하면 변수 paths가 수정됨)

svg_tag = soup.svg

# 네모 박스 넣기
for i in range(levels):
    size = 70
    xx = yy = 50
    xx = '{}'.format(xx + size * i)
    new_rect = soup.new_tag('rect', x=xx, y=str(yy), width=str(size), height=str(size),
                            stroke="white", fill=colors[i])
    svg_tag.append(new_rect)

with open('Seoul_Seniors/out_seniors.svg', 'w') as op:
    op.writelines(header)
    op.write(str(svg_tag).replace('></path', '/').replace('></rect', '/'))
```

그리고 결과

{% include figure.html image="https://haawron.github.io/assets/Seoul/out_seniors.svg" position="center" caption="2018 서울시 구별 고령화 인구 히트맵" %}

아직 안 끝났다 ㅋㅋ 글이 길어지니 2부로 가자.

[서울 열린데이터 광장]:http://data.seoul.go.kr/
[여기]:http://hellogohn.com/post_one48
