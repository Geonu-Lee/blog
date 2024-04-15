---
title: "Efficient DETR: Improving End-to-End Object Detector with Dense Prior"
date: 2024-04-15 15:00:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection,
    DETR
   ]
use_math: true
--- 


# Efficient DETR: Improving End-to-End Object Detector with Dense Prior
[arxiv 2021.03](https://arxiv.org/pdf/2104.01318.pdf), 2024-04-15 기준 119회 인용

## Task
- Object Detection
- DETR

## Contribution
- Object query 가 random으로 initialization 되는 것은 성능하락을 줄 것이다
- Detection head 로 부터 얻어지는 score 값을 기반으로 encoder output feature 에서 k개 선택
- 선택된 encoder feature 를 object queries 로 활용
- 이렇게 선별된 object query를 통해서 decoder layer를 하나만 써도 된다! → 기존에는 너무많은 decoder layer를 사용했다

## Motivation

<img src="/images/efficient_detr/fig1.png" width="450px" alt="alt">

기존의 DETR들은 Cascade Decoder layer를 사용 <br>
저자들은 random 으로 initialization 된 object query 가 성능이 나올수 있는 것은 결국 많은 decoder layer를 사용해서라고 분석

<img src="/images/efficient_detr/tab2.png" width="450px" alt="alt">

decoder layer 수가 적을 수록 성능이 급격히 떨어지는 것을 실험을 통해서 보여줌

<img src="/images/efficient_detr/tab1.png" width="450px" alt="alt">

decoder layer 수에 민감한 이유로 각 decoder layer에 적용되는 auxiliary loss (=헝가리안 매칭 로스) 때문이라고 설명
- 본 논문에서는 이 실험 부분을 DETR이라고 설명
- 하지만 DETR 에서는 각 decoder layer에 헝가리안 적용 x, 마지막 decoder layer 결과에 적용
- Table 1 에서 decoding loss를 사용하지 않은 실험에 대해서 명확한 설명이 없지만 추측하기로는 head에서 나온 결과를 1:N 으로 매칭해서 로스 연산?

<img src="/images/efficient_detr/fig2.png" width="450px" alt="alt">

Deformable Detr의 Object query에 대한 reference points 를 시각화

reference points도 학습이 되어야 물체에 몰린다 <br>
Initial stage에서는 이미지 전체를 cover 하는 느낌이다

**저자들의 결론: object queries의 random initialization은 성능에 좋지 않은 영향을 준다.**

## Proposed Method
<img src="/images/efficient_detr/fig3.png" width="450px" alt="alt">

(a) Deformable DETR - Object queries에 대한 linear layer로 reference points 뽑고 deformable attention 할때 사용 <br>
(b) RPN 으로 region proposal 하고 center points를 reference points 로 활용 <br>
(c) (b)에서 얻어지는 referece points를 기반으로 encoder output features를 object queries 로 활용

<img src="/images/efficient_detr/tab4.png" width="450px" alt="alt">

Table 4를 통해서 Object query를 encoder output features으로 사용했을 때 3 AP 증가했다고 설명 (2번째 행과 4번째 행)

<img src="/images/efficient_detr/fig5.png" width="650px" alt="alt">

기본적으로 Deformable DETR을 사용

Dense - RPN을 통해서 region proposal <br>
Proposal된 region 들에 대해 foreground score가 높은 순대로 K개 선택 <br>
선택된 K개의 bounding box의 center를 reference points 로 사용 <br>
reference points에 해당하는 K개의 encoder output features를 object queries로 활용


## Experimental Results

<img src="/images/efficient_detr/tab5.png" width="650px" alt="alt">

다른 방법들과의 비교 실험 결과

<img src="/images/efficient_detr/tab8.png" width="450px" alt="alt">

K에 따른 실험 <br>
K의 수를 Linear 하게 줄이는 방식