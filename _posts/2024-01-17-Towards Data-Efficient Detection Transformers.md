---
title: "Towards Data-Efficient Detection Transformers"
date: 2024-01-17 00:00:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection,
    DETR
   ]
use_math: true
---   
# Towards Data-Efficient Detection Transformers

## Task
- Object Detection
- DETR

## Contribution
- Detection transformers are generally data-hungry
- RCNN 에서 DETR 로 step-by-step transition 하면서 분석
- Sparse feature sampling 방법 제안
- Label augmentation 방법 제안

![Alt text](/images/data_efficient_detr/fig1.png)

DETR 계열의 Detector 들은 small-size dataset 에서 성능이 크게 떨어진다

## Difference Analysis of RCNNs and DETRs

![Alt text](/images/data_efficient_detr/tab1.png)

Sparse-RCNN 에서 각 모듈들을 빼고 추가하면서 DETR 로 transition </br>
그 과정에서 각각의 모듈이 성능에 미치는 정도를 분석 </br>
결론적으로는 DETR 을 위한 모듈들이 data hungry 이다 </br>
그리고 RoIAlign 같은 것으로 sparse feature sampling 하는 것이 크게 도움을 준다

## Proposed Method

![Alt text](/images/data_efficient_detr/fig2.png)

제안하는 방법 overview

![Alt text](/images/data_efficient_detr/eq3.png)

그 전 decoder layer 에서 prediction 된 box 정보를 활용해서 RoIAlign </br>
2번째 decoder layer에서만 진행 </br>
$\boldsymbol{\text{z}}^L_l \in \mathbb{R}^{N \times K^2 \times D}$

![Alt text](/images/data_efficient_detr/eq4.png)

backbone feature 로부터 high-resolution feature 를 사용 </br>
down-sampled feature 3 levels 사용

3 levels 에 대해서는 backbone 으로부터 온 feature 에서 RoIAlign </br>
low-resolution feature 는 encoder 로 부터 온 feature 에서 RoIAlign </br>
그리고 이 모든 것들을 concat

$\boldsymbol{\text{z}}^{ms}_l \in \mathbb{R}^{N \times LK^2 \times D}$

RoIAlign 으로 sparse feature sampling 이 되기때문에 computation cost 가 크게 늘지는 않는다라고 주장

### Label Augmentation
![Alt text](/images/data_efficient_detr/fig3.png)

> only a small number of detection candidates are provided with a positive supervision signal in each iteration.

단순히 positive label 을 반복해서 object query 들이 positive 으로 더 할당될 수 있도록 한다

> A fixed repeat time of 2 is adopted for our label augmentation and non-maximum suppression (NMS) with a threshold of 0.7 is used for duplicate removal.

## Experimental Results
![Alt text](/images/data_efficient_detr/tab2.png)

small dataset Cityscapes 에서의 실험 결과

![Alt text](/images/data_efficient_detr/fig4.png)

COCO 데이터셋에서 sub-sampled 로 데이터셋을 구성해서 실험한 결과