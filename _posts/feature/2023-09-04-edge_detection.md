---
title: "Edge Detection"
excerpt: "이미지(영상)에서 edge를 검출하는 방법을 알아본다."
category: computer vision
# excerpt: "Github에 공개되어있는 대표적인 Face Parsing 실험 및 비교"

tags:
  - computer vision
search: true

# 목차
toc: true  
toc_sticky: true 
use_math: true
---
## Introduction

정물화를 그린다는 가정을 해봅시다.

그릴 대상과 도구는 모두 준비되어 있고 구도까지 잡았을 때, 제일 먼저 해야할 작업이 무엇일까요?

<div class="phrase">우리는 <mark>스케치</mark>를 합니다!</div><br>

<div style="text-align: center">
<img src="https://mymodernmet.com/wp/wp-content/uploads/2021/05/person-drawing-a-pumpkin-1.jpg" width="420px">
</div><br>


채색을 하고 질감을 표현하기 전에 대략적인 윤곽 및 대상을 표현할 수 있는 선들을 그립니다. 이것을 *edge*라고 부릅니다. 영상(이미지)에서 *edge*는 중요한 정보입니다. 사물과 배경을 구분짓는 경계이기도 하고, 어떤 장면인지 알 수 있게 하는 뼈대가 되는 정보입니다.

이번 포스팅에서는 에서 *edge* 정보를 추출하는 방법을 알아보겠습니다. 그럼, 

<div class="phrase">¡Vamos!</div>

## Image Gradient
0부터 255사이의 RGB 값으로 표현되어 있는 이미지(영상)에서 어떻게 edge라는 것을 정의할까요?

다시 스케치로 돌아가 봅시다.

선의 색(검은색)과 도화지, 즉 바탕의 색(흰색)을 비교를 해보면, 그 색의 차이가 많이 난다는 것을 알 수 있습니다. *Edge*는 일종의 경계선이기 때문에 어떤 한 지점의 내부와 외부를 경계짓기 위해 색깔차이가 날 수 밖에 없습니다.

*Edge*를 찾으려면 픽셀 값 변화율이 큰 지점, 즉 픽셀값의 미분값이 큰 지점(엄밀히는 *gradient*의 크기가 큰 지점)을 찾는 것이 *edge*를 찾는 것이라고 할 수 있습니다. 영상 $$I$$에 대한 gradient 영상 $$\lVert \nabla I \rVert$$를 찾는 것이 *Edge*를 찾는 첫 걸음입니다.

한 픽셀 위치$$(x, y)$$에서의 gradient $$\nabla I(x,y)$$를 구하는 방법은 여러가지가 있습니다.

1안)

$$
\begin{align}
\nabla I(x,y) &= 
\begin{bmatrix} 
\nabla_x I(x, y) \\ \nabla_y I(x, y)
\end{bmatrix}
\\ &=
\begin{bmatrix} 
 I(x+1, y) - I(x, y)\\ I(x, y+1) - I(x, y)
\end{bmatrix}
\end{align}
$$

2안)

$$
\begin{align}
\nabla I(x,y) &= 
\begin{bmatrix} 
\nabla_x I(x, y) \\ \nabla_y I(x, y)
\end{bmatrix}
\\ &=
\begin{bmatrix} 
 I(x+1, y) - I(x-1, y)\\ I(x, y+1) - I(x, y-1)
\end{bmatrix}
\end{align}
$$

<br><br>

## Canny Edge Detection
$$\quad$$1) Noise Reduction<br>
$$\quad$$2) Gradient Intensity 구하기<br>
$$\quad$$3) Non-maximum Suppression<br>
$$\quad$$4) Double Thresholding<br>
$$\quad$$5) Edge Tracking by Hysteresis<br>

### 1) Noise Reduction
Gaussian filter와 같은 loss pass filter를 활용해 영상(image)에 튀는 값(noise)를 제거해 줍니다.

### 2) Gradient Intensity 구하기
노이즈가 제거된 이미지에서 에지의 강도와 방향을 계산합니다. 에지는 픽셀 값의 변화가 큰 지점을 의미하며, 이 변화율을 **그라디언트(Gradient)**라고 합니다.

방법: Sobel 필터와 같은 미분 연산자 커널을 사용하여 이미지의 수평 방향($$G_x$$)과 수직 방향($$G_y$$)의 그라디언트를 계산합니다.

강도 (Magnitude): 에지의 세기 $$M$$을 계산합니다.

$$M = \sqrt{G_x^2 + G_y^2}$$

방향 (Direction): 에지의 방향 $$\theta$$를 계산합니다.

$$\theta = \arctan(G_y / G_x)$$

### 3) Non-maximum Suppression

2단계에서 계산한 그라디언트 강도를 사용하여 에지의 굵기를 1픽셀로 줄이는 작업입니다. 만약 에지가 두꺼우면 중심선 외의 다른 픽셀들도 에지로 인식될 수 있습니다.

방법: 계산된 에지 방향을 기반으로, 특정 픽셀이 그 방향상의 주변 픽셀보다 그라디언트 강도가 최대가 아닌 경우 해당 픽셀을 억제(0으로 만듦)하여 제거합니다. 이 과정을 통해 얇고 정확한 에지(Thin Edge)만 남게 됩니다.

### 4) Hysterisys Thresholding
마지막으로, 에지를 최종적으로 결정하기 위해 **두 개의 임계값(Threshold)**을 사용합니다. 

이것이 Canny 알고리즘의 가장 중요한 특징 중 하나입니다.

상위 임계값 (High Threshold, $$T_H$$): 이 값보다 강도가 크면 **강한 에지(Strong Edge)**로 확정합니다.

하위 임계값 (Low Threshold, $$T_L$$): 이 값보다 강도가 작으면 **약한 에지(Weak Edge)**라도 에지가 아닌 것으로 간주하여 제거합니다.

처리: 강도가 $$T_L$$와 $$T_H$$ 사이에 있는 **약한 에지(Weak Edge)**는 강한 에지와 연결되어 있을 때만 최종 에지로 포함됩니다.

이를 통해 실제 에지와 노이즈를 효과적으로 구분할 수 있습니다.
<!-- <div class="phrase">작성중... </div> -->