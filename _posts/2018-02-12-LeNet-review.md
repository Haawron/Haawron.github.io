---
title: [DeepLearning] 01. LeNet
category: DeepLearning
excerpt: |
  [DeepLearning] 01. LeNet
feature_text: |
  ## 01. LeNet
feature_image: "https://unsplash.it/1200/400?image=1048"
---

# LeNet - Yann LeCun
Yann LeCun은 CNN을 처음 개발했으며, 전 세계 딥러닝 4대 천왕 중 한 명이다.<br>
LeNet을 시작으로 지금까지 딥러닝에 역사적인 기여를 했다.<br>
현재는 페이스북에서 AI 기반 로봇을 연구 중이라고 한다.(2018.1)<br>

LeNet은 LeNet - 1부터 LeNet - 5까지 5종류가 있지만 가장 유명한 1과 5에 대해 알아보자.

## LeNet - 1 (1990)
[이미지]<br>
이미지를 구글에서 가져왔는데 마지막 Layer는 Convolution이 아니라 Fully Connected이다.<br>
[이미지]<br>
기존에는 Fully Connected Network(FCN)만을 사용하고 있었다.
하지만 FCN은 개체가 이동하거나 회전하는 등 아주 살짝만 변형이 되어도 완전히 다른 이미지로 인식되어 분명한 한계가 있었다.
2D Convolution을 활용하여 국소적인 특징을 추출해내는 것으로 이를 해결할 수 있다.
그렇게 해서 2D convolution과 pooling(논문에서는 squashing function)을 적절히 조합해 최초의 CNN인 LeNet-1이 탄생했다.<br>

### 특징
- 위쪽으로 갈수록 더 global한 feature를 학습한다. (어떻게 feature가 더 작아지는데 global한 내용을 학습하는지는 뒤의 ZFNet에서 다룸)
- FCN보다 parameter수가 매우 적다. bias를 제외하고 3220개 밖에 되지 않는다.
- max-pooling으로 강한 자극을 취했다. 논문에서는 자극을 요약(summarize)한다고 표현했다.
- 전체 이미지에 같은 kernel을 써서 shared weight를 적용했다.
- 이 때까지의 다른 모델보다 훨씬 효과가 좋았다.
<li style='color:red'>사실 훨씬 더 크게 만들 수 있었는데 컴퓨팅 파워의 한계로 parameter수에 제한이 컸다.</li>

워낙 오래돼서 실용성은 적지만 최초라는 점에서 학문적인 의미는 크다고 할 수 있다.<br>
그리고 이 연구에 사용된 데이터는 손글씨로 쓴 우편번호들인데, 지금은 이것들이 정제되어 MNIST라는 데이터셋으로 딥러닝의 Hello world 같은 개념으로 활용되고 있다. (최초사용은 아니다)<br>


## LeNet - 5 (1998)
[이미지1]<br>
[이미지2]<br>
구조
- input이미지를 위/아래/양옆 2px씩 빈 공간을 추가하여 크기를 32x32로 늘렸다.
Convolution이 이미지 끄트머리에서도 유효하게 되면서 좀 더 detail한 부분을 표현할 수 있다.
이 개념은 후에 padding으로 발전한다.
- (\*)C2(첫 번째 그림에서 두 번째 Convolution) 할 때의 각 output feature map은 미리 정한 3개의 input feature map으로만 진행했다. 순서쌍은 다음 표와 같다.<br>

[표]<br>

### 특징
- parameter는 6만 개로 거의 20배가 되었지만 성능은 훨씬 더 좋다. (FCL 하나가 추가될 때마다 기하급수적으로 늘어난다.)
- 단계별 feature map에서 shift invariance를 확인할 수 있다.

{% include figure.html image="http://yann.lecun.com/exdb/lenet/gifs/asamples.gif" caption="LeNet-5" position="center" %}
