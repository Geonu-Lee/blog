---
title: "Enhancing Your Trained DETRs with Box Refinement"
date: 2024-04-24 10:30:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection,
    DETR
   ]
use_math: true
--- 

# Enhancing Your Trained DETRs with Box Refinement
[arxiv 2023.07](https://arxiv.org/pdf/2307.11828.pdf), 2024-04-23 기준 0회 인용

## Task
- Object Detection
- DETR

## Contribution
- Box refinement network 구조를 제안
- well-trained models 를 변경하거나 다시 학습하지 않고 plugin 방식의 구조 제안
- 다양한 DETR 방법들에 적용 가능

<img src="/images/refinebox/fig1.png" width="450px" alt="alt">

제안하는 Refinement module 을 적용했을 때 성능이 향상된다

## Motivation

<img src="/images/refinebox/fig2.png" width="450px" alt="alt">

기존에 학습된 DETR 은 고정시키고 가벼운 Refinement Network 만 사용해서 box를 refine 한다

<img src="/images/refinebox/fig3.png" width="450px" alt="alt">

idel performance 분석을 위해서 <br>
box predictions 을 GT box로 대체 -> eliminate localization error <br>
classification predictions 을 GT class 로 대체 → eliminate classification error <br>
localization error 를 제거했을때의 성능 향상이 크다 <br>
즉, localization quality 가 안좋다 → refinement network 를 제안한다

## Proposed Method

<img src="/images/refinebox/fig4.png" width="650px" alt="alt">

**Extracting the Multi-scale Features** <br>
backbone 으로부터 multi-scale feature 를 추출 <br> 
추후 RoIAlign 할시에 사용됨

**Refining the Boxes** <br>
RoIAlign - Residual Blocks - MLP <br>
prediction 된 box 정보를 기반으로 RoIAlign - (7 x 7) <br>
Residual Block - bottleneck Blocks <br>
MLP - predicting the deltas to the ground truth <br>

**Training and Inference**
학습시에는 GT 랑 매칭된 prediction 결과만 refine 하고 학습 <br>
Inference 시에는 classification score 를 기준으로 top K 가 선정하고 refine <br>

## Experimental Results

<img src="/images/refinebox/tab1.png" width="650px" alt="alt">

여러 DETR 방법들에 적용했을 때 성능이 향상된다

<img src="/images/refinebox/fig5.png" width="450px" alt="alt">

기존에는 classification error 를 제거했을 때 성능이 크게 향상되지 않았었다 → poor localization quality 때문에 <br>
제안하는 RefineBox 를 적용했을 때에는 classification error 를 제거했을 때 성능이 크게 향상된다

<img src="/images/refinebox/tab_ablation.png" width="350px" alt="alt">

Refine 하는 box 가 많아질수록 성능은 더 향상된다