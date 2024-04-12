---
title: "Cascade-DETR: Delving into High-Quality Universal Object Detection"
date: 2024-04-12 9:30:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection,
    DETR
   ]
use_math: true
--- 


# Cascade-DETR: Delving into High-Quality Universal Object Detection
[ICCV 2023](https://openaccess.thecvf.com/content/ICCV2023/papers/Ye_Cascade-DETR_Delving_into_High-Quality_Universal_Object_Detection_ICCV_2023_paper.pdf), 2024-04-12 기준 8회 인용


## Task
- Object Detection
- DETR


## Contribution
- 기존의 방법들은 다양한 도메인들에 약하다
- object-centric attention 을 하는 Cascade Attention 구조를 제안
- Classification confidence score를 사용하는 것보다 expected IoU를 사용하도록 하는 IoU-aware Query Recalibration 제안
- 여러 도메인의 데이터셋을 모은 데이터셋 제안

## Motivation
<img src="/images/cascade-detr/fig1.png" width="650px" alt="alt">

기존의 방법 Faster R-CNN, DN-DETR 경우 실제 세계와 같은 다양한 도메인에서 성능이 많이 하락

<img src="/images/cascade-detr/fig2.png" width="450px" alt="alt">

> Existing DETR-based methods still have substantial room for improvement.

성능이 향상될 공간이 존재한다

## Proposed Method

#### Cascade Attention

<img src="/images/cascade-detr/fig3.png" width="450px" alt="alt">

Cascade 모양으로 attention module를 설계 <br>
이전 decoder layer 에서의 prediction box 값을 활용 <br>
이전 레이어에서 prediction 된 box 내부만 cross-attention 진행 <br>
First 레이어에서는 DAB (Dynamic anchor box) DETR 에서 제안한 learnable anchor box를 사용

<img src="/images/cascade-detr/eq2_3.png" width="450px" alt="alt">

>cross-attention region $S_i$ not only brings **object-centric bias**, but will also be **iteratively refined**

cross-attention region 은 object-centric bias 와 iteratively regined 효과가 있다

<img src="/images/cascade-detr/fig4.png" width="450px" alt="alt">

기존의 방법은 Cityscapes 데이터셋에서 특정 object를 attention 하지 못하는 문제가 있다

#### IoU-aware Query Recalibration

>computing final detection results, **classification confidence** is adopted as a **surrogate** to rank all query proposals

최종 detection results를 위해서 classification confidence 값들이 판단 기준이 된다

>However, the classification score **does not explicitly account for the accuracy of the predicted bounding box**

이를 해결하기 위해 IoU-aware scoring 을 제안

<img src="/images/cascade-detr/eq4.png" width="450px" alt="alt">

query $q$ 와 groundtruth box에 대한 expected IoU 를 구하는 것 <br>
별도의 Linear Layer 로 expected IoU를 연산

<img src="/images/cascade-detr/eq5.png" width="450px" alt="alt">

최종 스코어는 expected IoU score와 classification confidence score를 같이 사용 (마지막 layer에 대해서만)

<img src="/images/cascade-detr/fig5.png" width="450px" alt="alt">

Upper bound  - 실제 GT 와의 IoU 값을 기반으로 sorting <br>
Baseline - classification confidence score 로 sorting <br>
Cascade - expected IoU score 로 sorting


Training <br>
<img src="/images/cascade-detr/eq6.png" width="450px" alt="alt">


#### UDB10 Dataset

제안이라기 보다 기존의 데이터셋들 조합 <br>
10개 데이터셋을 모음

<img src="/images/cascade-detr/tab1.png" width="450px" alt="alt">

Domain Generalization & Adaptation 이 아니라 각각 학습하고 평가

## Experimental Results

<img src="/images/cascade-detr/tab8.png" width="650px" alt="alt">

COCO 데이터셋 결과

<img src="/images/cascade-detr/tab13.png" width="700px" alt="alt">

UDB 10 데이터셋 결과