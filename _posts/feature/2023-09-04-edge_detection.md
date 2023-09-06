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

안녕하세요 MJ입니다.

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
<div class="phrase">작성중... </div>