---
title: "DETR Does Not Need Multi-Scale or Locality Design"
date: 2024-04-15 9:30:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection,
    DETR
   ]
use_math: true
--- 


# DETR Does Not Need Multi-Scale or Locality Design
[ICCV 2023](https://openaccess.thecvf.com/content/ICCV2023/papers/Lin_DETR_Does_Not_Need_Multi-Scale_or_Locality_Design_ICCV_2023_paper.pdf), 2024-04-15 기준 6회 인용

## Task
- Object Detection
- DETR

## Contribution
- 최근 많은 DETR 기반 방법들에서 사용하고 있는 multi-scale 방법과 Locality constraint 방법들이 필요없다
- single scale feature 만 사용하는 Improved “Plane“ DETR 방법을 제안
- Masked image modeling (MIM) -based backbone network 사용
- Box-to-pixel relative position bias (BoxRPB) 제안

## Proposed Method
<img src="/images/plane-detr/fig1.png" width="450px" alt="alt">

기존 DETR 에 비해 큰 향상을 보여줌 <br>
multi-scale 과 locality design 을 사용하는 deformable detr 에 비해서도 향상됨

#### Enhanced Plain DETR baseline
기존의 여러 DETR 기반 연구들에서 제안된 기법들을 적용

<img src="/images/plane-detr/tab1.png" width="450px" alt="alt">

MTE - Merging Transformer encoder into the backbone <br>
FL - Focal Loss <br>
IR - Iterative refinement <br>
TS - Two-stage <br>
LFT - Look forward twice → DINO <br>
MQS - Mixed query selection → DINO <br>
HM - Hybrid matching 

#### Box-to-Pixel Relative Position Bias

<img src="/images/plane-detr/eq1.png" width="400px" alt="alt">

기존 DETR 의 cross-attention 연산 과정

<img src="/images/plane-detr/eq2.png" width="400px" alt="alt">

Relative Position Bias (RPB) 를 추가 <br>
일반 RPB 와 다른점은 box 정보를 활용

#### Naive BoxRPB implementation

left-top, right-bottom 과의 relative position 사용

<img src="/images/plane-detr/eq3.png" width="400px" alt="alt">

K x H x W x 4 → K x H x W x 256 → K x H x W x M <br>

M = number of attention head

#### Decomposed BoxRPB implementation

<img src="/images/plane-detr/fig2.png" width="650px" alt="alt">

<img src="/images/plane-detr/eq5.png" width="400px" alt="alt">

연산 효율을 위해서 x, y 를 따로 진행 <br>
K x W x 2 → K x W x 256 → K x W x M <br>
K x H x 2 → K x H x 256 → K x H x M

#### Masked image modeling (MIM) pre-training

Masked image modeling 방법중 SimMIM pre-trained weights를 사용 <br>
Swin-Transformer 사용

#### Bounding box regression with re-parameterization

기존의 DETR 학습방식은 large objects가 loss에 주는 영향이 큼

<img src="/images/plane-detr/eq6.png" width="400px" alt="alt">

$l-1$ decoder layer의 예측된 bounding box 정보를 활용해서 re-parameterization


## Experimental Results

다른 방법들과 비교하기보단 Ablation study 로 각각의 방법의 효과를 입증하는 방향

<img src="/images/plane-detr/tab2.png" width="400px" alt="alt">

BoxRPB, MIM, parameterization 에 대한 ablation study

<img src="/images/plane-detr/tab3.png" width="650px" alt="alt">

box relative position bias 에 대한 ablation study

<img src="/images/plane-detr/tab5.png" width="400px" alt="alt">

<img src="/images/plane-detr/fig3.png" width="650px" alt="alt">

MIM pre-training 에 대한 ablation study

