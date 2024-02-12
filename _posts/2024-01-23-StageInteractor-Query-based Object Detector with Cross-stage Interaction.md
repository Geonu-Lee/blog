---
title: "StageInteractor: Query-based Object Detector with Cross-stage Interaction"
date: 2024-01-23 23:00:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection,
    DETR
   ]
use_math: true
--- 

# StageInteractor: Query-based Object Detector with Cross-stage Interaction
[ICCV 2023](https://openaccess.thecvf.com/content/ICCV2023/html/Teng_StageInteractor_Query-based_Object_Detector_with_Cross-stage_Interaction_ICCV_2023_paper.html), 2024-01-23 기준 2회 인용

## Task
- Object Detection
- DETR

## Contribution
- 기존 DETR의 one-to-one matching 방법들은 모델에게 strong fine-grained discrimination 을 요구하게 된다
- Cross-stage interaction 구조를 제안
- Reuse dynamic operators with lightweights adapters (Adamixer)

<img src="/images/stageinteractor/fig1.png" width="400px" alt="alt">

본 논문에서 제안하고 있는 방법을 적용했을 경우 성능이 향상된다

## Motivation
<img src="/images/stageinteractor/fig2.png" width="550px" alt="alt">

> This strict bipartite matching would ask the detector to capture details to distinguish the predictions

Figure 2(a) 에서 Stage1 의 white box는 GT와 할당되어 Positive로 정확하게 예측 <br>
하지만 red box 는 GT와 많이 겹침에도 불구하고 GT와 할당되지 않아 negative로 예측 <br>
Stage 6 에서는 반대가 되는 문제도 보여준다

본 논문에서 제안하고자 하는 것은 이런 문제를 해결하여 두 query에 대한 결과가 모두 정확하게 하는 것

## Proposed Method
<img src="/images/stageinteractor/fig3.png" width="800px" alt="alt">

### Cross-stage Label Assignment
<img src="/images/stageinteractor/fig5.png" width="450px" alt="alt">

stage $i$ 에서 다른 stage $j$ 결과를 활용 <br>
논문에서는 $i-1$ 부터 $L$ stage 까지를 사용

다른 stage $j$ 에서 GT와 매칭된 query index를 활용 <br>
query index에 대한 stage $i$ 에서의 Query의 prediction 결과와 GT와의 IoU 값이 threshold 를 넘을 경우 진행 <br>
해당 GT 를 Candidate Bags에 넣어준다 <br>
그리고 one-hot vectors 로 stage $i$ classification logits 와 BCE Loss <br>
이를 통해서 Figure 2와 같은 문제를 해결하려고 한다


### Cross-stage Dynamic Filter Reuse
<img src="/images/stageinteractor/fig6.png" width="450px" alt="alt">

해당 제안 방법은 AdaMixer에 대한 개선 방법

<img src="/images/stageinteractor/adamixer.png" width="450px" alt="alt">

AdaMixer 정리해둔 내용 캡처

Adpative channel mixing 을 위해서 content query 를 활용하여 weights를 생성<br>

$i$ stage 에서 $d$ 번째 content vector 에 대한 weights <br>
$W^{(i)}_d \in \mathbb{R}^{D_C \times D_C}$

이 과정에서 파라미터가 $D \times {D_C}^2$ 만큼 필요하다

필요한 파라미터를 줄이기 위해서 Filter Bank 에 값들을 저장하면서 재사용하는 방법을 제안

<img src="/images/stageinteractor/eq3.png" width="450px" alt="alt">

Filter Adaptor
$W^{(1)}_j$, $W^{(2)}_j$

$M_j$ - 전 stage 에서 저장된 값

필요한 파라미터 수 - $2{D_C}^2$

## Experimental Results
기본적으로 AdaMixer 방법에 제안하는 방법을 적용

<img src="/images/stageinteractor/tab2.png" width="500px" alt="alt">

제안하는 방법을 AdaMixer에 적용했을 때 성능이 제일 좋다

<img src="/images/stageinteractor/tab3.png" width="800px" alt="alt">

다른 방법들과 비교했을때도 성능이 좋다

<img src="/images/stageinteractor/tab6.png" width="500px" alt="alt">

Cross-Stage Label Assignment (CSLA) 방법이 성능 향상에 더 크게 기여한다