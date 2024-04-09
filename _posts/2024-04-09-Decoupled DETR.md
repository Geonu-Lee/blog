---
title: "Decoupled DETR: Spatially Disentangling Localization and Classification for Improved End-to-End Object Detection"
date: 2024-04-09 12:00:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection,
    DETR
   ]
use_math: true
--- 

# Decoupled DETR: Spatially Disentangling Localization and Classification for Improved End-to-End Object Detection
[ICCV 2023](https://openaccess.thecvf.com/content/ICCV2023/papers/Zhang_Decoupled_DETR_Spatially_Disentangling_Localization_and_Classification_for_Improved_End-to-End_ICCV_2023_paper.pdf), 2024-04-09 기준 3회 인용

## Task
- Object Detection
- DETR

## Contribution
- 기존의 DETR 들은 classification 과 localization 을 위한 cross-attention module을 하나로만 사용 → 두개의 task가 집중해야하는 곳이 다를 것이다
- classification 과 localization 을 다루는 decoder 의 cross-attention module을 decoupling
- task-aware query generation module 제안
- classification score와 IoU 가 misalignment 한 부분을 해결하기 위한 학습 방법 제안

## Proposed Method
<img src="/images/decoupled_detr/fig1.png" width="450px" alt="alt">

단순하게 localization 을 위한 decoder와 classification 을 위한 decoder 두개로 설계했을 때 결과 <br>
두 task가 서로 attention 하는 포인트가 다르다라는 부분을 주장

<img src="/images/decoupled_detr/fig2.png" width="450px" alt="alt">

Figure 2 와 같이 두 task 가 decoupling 될 수 있도록 설계하는 것이 저자들의 목적 <br>
단순히 decoder layer를 복제해서 두개를 사용하는 것이 아니다 <br>
self-attention 은 하나로 공유하고 cross attention 부분만 따로 만들어진 구조 

<img src="/images/decoupled_detr/fig3.png" width="650px" alt="alt">

classification 을 위한 query와 localization을 위한 query를 생성하서 사용

<img src="/images/decoupled_detr/eq3.png" width="450px" alt="alt">

query 생성을 위해서 Encoder 로 부터 얻어진 feature에 mini-detector를 붙여 anchor box를 얻어냄
- feature의 각 cell 에서 bounding box, classification score 얻어냄
- Top N개의 box selected

이 후에 각각의 classification, localization branch 통과

<img src="/images/decoupled_detr/eq5.png" width="450px" alt="alt">

$R_{SP} \in \mathbb{R}^{N \times M \times 2}$

Bilinear interpolation 후 average 를 통해서 query로 사용
- self attention 을 때는 두 task 에 대한 query가 concat 되어서 사용
- 각각의 cross-attention을 위해서 split 된 후 사용

Task Alignment Learning <br>
저자들은 classification score가 높지만 실제로 IoU가 낮은 case들이 많음을 문제점으로 지적

<img src="/images/decoupled_detr/eq7.png" width="450px" alt="alt">

<img src="/images/decoupled_detr/eq6.png" width="350px" alt="alt">

classification loss 학습시 IoU를 기반으로 학습에 반영되도록 설계 <br>
$s$ - classification score <br>
$u$ - IoU value <br>
$\alpha$ - 0.25, $\beta$ - 0.75, focusing parameter - 2 <br>
target 값을 binary로 하는 것이 아닌 score값과 IoU 값에 의해 설정되도록 설계됨

## Experimental Results

<img src="/images/decoupled_detr/tab1.png" width="650px" alt="alt">

12epoch 학습에 대한 결과

<img src="/images/decoupled_detr/tab1_1.png" width="650px" alt="alt">

50epoch 학습에 대한 결과

<img src="/images/decoupled_detr/tab2.png" width="450px" alt="alt">

DFL - Task 별로 cross-attention 나눈 것 <br>
TAQ - query generation 을 따로 한것 <br>
TAL - IoU 기반으로 task alignment learning loss 추가 한것

<img src="/images/decoupled_detr/tab3.png" width="450px" alt="alt">

단순히 decoder 두개 둔 것 vs. cross-attention 만 나눈 것