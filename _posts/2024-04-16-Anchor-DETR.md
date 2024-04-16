---
title: "Anchor DETR: Query Design for Transformer-Based Object Detection"
date: 2024-04-16 10:00:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection,
    DETR
   ]
use_math: true
--- 


# Anchor DETR: Query Design for Transformer-Based Object Detection
[AAAI 2022](https://ojs.aaai.org/index.php/AAAI/article/view/20158/19917), 2024-04-16 기준 275회 인용

## Task
- Object Detection
- DETR

## Contribution
- 기존의 DETR 의 object query는 어느 region을 집중하는지 명확하게 파악하기 어렵다. (each object query will not focus on a specific region)
- anchor points 를 기반으로 object query 생성하는 방법 제안
- memory cost를 줄이기 위한 attention variant 를 제안 - Row-Column Decouple Attention (RCDA)

## Proposed Method

<img src="/images/anchor-detr/fig1.png" width="450px" alt="alt">

prediction 된 box의 center 값들을 visualization <br>
기존의 DETR은 각각의 object query에 대한 값들일 넓게 퍼져 있다. → object query들이 specific region을 집중하지 못하고 있고 이는 학습을 어렵게 한다

<img src="/images/anchor-detr/fig2.png" width="450px" alt="alt">

Figure 2 와 같이 anchor points를 생성하고 anchor point를 기반으로 object query 가 생성될 수 있도록 설계

#### Anchor Points to Object Query

<img src="/images/anchor-detr/eq4.png" width="400px" alt="alt">

anchor points $N_A$ 개를 생성 - (300, 2) <br>
grid anchor points 일 때는 17 x 17 = 289 개로 비슷하게 세팅

<img src="/images/anchor-detr/eq5.png" width="400px" alt="alt">

$g$ 는 positional encoding function <br>
positional encoding 후 두개의 Linear Layer 로 구성된 MLP 통과 <br>
anchor points 를 기반으로 만들어진 positional embedding을 통해서 object query가 anchor point 기반으로 학습되도록 한다

<img src="/images/anchor-detr/fig3.png" width="650px" alt="alt">

Encoder 의 positional encoding도 같은 방법으로 진행

#### Multiple Predictions for Each Anchor Point

하나의 anchor point 에서 multiple object prediction이 가능하도록 하기 위한 방법

<img src="/images/anchor-detr/eq6.png" width="400px" alt="alt">

pattern 수 만큼의 embedding 생성 - $3 \times C$

$Q_f^{init} \in \mathbb{R}^{N_p \times N_A \times C}$ <br>
$Q_p \in \mathbb{R}^{N_p \times N_A \times C}$

anchor point 개수 만큼 반복 <br>
$3 \times 300 \times C$

$Q = Q_f^{init} + Q_p$

anchor point 기반으로 만들어진 positional embedding

#### Row-Column Decoupled Attention

$K_f \in \mathbb{R}^{H \times W \times C}$ <br>
$K_{f,x} \in \mathbb{R}^{W \times C}$ <br>
$K_{f,y} \in \mathbb{R}^{W \times C}$

H, W 방향으로 각각 average pooling

<img src="/images/anchor-detr/eq8.png" width="400px" alt="alt">

memory 효율을 위해 H 와 W 를 분리해서 연산

<img src="/images/anchor-detr/eq9.png" width="400px" alt="alt">

기존의 2D로 진행했던 과정을 1D로 진행하는 수식

$A \in \mathbb{R}^{N_q \times H \times W \times M}$

일반적인 DETR attention map 크기

$A_x \in \mathbb{R}^{N_q \times W \times M}$ <br>
$A_y \in \mathbb{R}^{N_q \times H \times M}$

$N \times H \times W \times M$ 크기의 attention map 보다 크기가 작다

<img src="/images/anchor-detr/eq10.png" width="400px" alt="alt">

RCDA 방식으로 attention 을 진행했을 때 가장 크기가 큰 Nq x H x C 와 비율을 계산

> 2x memory for the C4 or DC5 feature and saving 4x memory for the C3 feature


## Experimental Results

<img src="/images/anchor-detr/tab2.png" width="650px" alt="alt">

다른 Detectors 와의 비교 분석 실험

<img src="/images/anchor-detr/tab3.png" width="650px" alt="alt">

각 componet 에 대한 ablation study

<img src="/images/anchor-detr/tab5.png" width="450px" alt="alt">

anchor points 의 유형에 따른 실험