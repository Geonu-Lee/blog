---
title: "Less is More: Focus Attention for Efficient DETR"
date: 2024-01-29 17:00:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection,
    DETR
   ]
use_math: true
--- 


# Less is More: Focus Attention for Efficient DETR
[ICCV 2023](https://openaccess.thecvf.com/content/ICCV2023/html/Zheng_Less_is_More_Focus_Attention_for_Efficient_DETR_ICCV_2023_paper.html), 2024-01-29 기준 3회 인용

## Task
- Object Detection
- DETR

## Contribution
- Focus attention on more informative tokens
- 이를 위해서 token scoring mechanism 을 제안 - Foreground Token Selector (FTS)
- Encoder 에서 dual attention 구조를 설계
- 이를 통해서 효과적으로 background queries 를 abandon

<img src="/images/focus_detr/fig1.png" width="600px" alt="alt">

tokens 을 sparse 하게 가져가는 Sparse DETR
- 불필요한 background 가 포함되어 있다
- 필요한 foreground 가 제거되었다
- 이는 Deocder의 Cross attention map 에 의지하기 때문이다

> this monotonous sparse encoder, the number of retained foreground tokens remains numerous

<img src="/images/focus_detr/fig2.png" width="450px" alt="alt">

제안하는 방법을 DINO 에 적용했을 때 GFLOPs 와 Latency 가 감소한다

## Proposed Method

<img src="/images/focus_detr/fig3.png" width="700px" alt="alt">

### Scoring mechanism

Multi-scale features를 활용 </br>
여러 scale 의 tokens 들을 겹쳐서 구성

>overlap of the adjacent interval by 50% </br>
>ensuring that the multiscale feature map predicts object heterogeneity

<img src="/images/focus_detr/eq1.png" width="500px" alt="alt">

GT box 를 활용해서 label assignment </br>
GT box 안에 있거나, 해당 token이 구성된 scale range 안에 드는 경우 (인접한 scale 끼리 구성했기 때문에?) </br>
Figure 3 과 같이 각 level 에 대해서 supervision

<img src="/images/focus_detr/fig5.png" width="500px" alt="alt">

Top-down 방식으로 진행 </br>
High-level -> Low-level

>Considering that high-level feature maps contain richer semantic than low-level features

<img src="/images/focus_detr/eq2.png" width="500px" alt="alt">

$\alpha$ - learnable modulation coefficients

<img src="/images/focus_detr/fig4.png" width="500px" alt="alt">

Sparse DETR 에 비해서 제안하는 방법의 foreground tokens 가 더 좋다고 주장

<img src="/images/focus_detr/eq3.png" width="500px" alt="alt">

MLP 를 통해서 score 를 구하고 곱해준다 -> 그 다음에 Top-k 개 선택

### Calculation Process of Dual Attention

<img src="/images/focus_detr/al1.png" width="500px" alt="alt">

Dual Attention 은 단순하게 일반 Self-attention 하고 Deformalbe Attention 하는 방식

> the limitation of deformable attention in distant information mixing

## Experimental Results
<img src="/images/focus_detr/tab1.png" width="700px" alt="alt">

제안하는 방법을 적용했을 때 DINO 의 성능을 최대한 유지시키면서 GFLOPs를 줄이고 FPS 를 증가시킨다

<img src="/images/focus_detr/tab2.png" width="500px" alt="alt">

Backbone ResNet-101 에서 다른 방법들과 비교했을 때 성능이 제일 좋다