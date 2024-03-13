---
title: "Active Learning for Single-Stage Object Detection in UAV Images"
date: 2024-03-13 19:00:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection,
    Active Learning
   ]
use_math: true
--- 


# Active Learning for Single-Stage Object Detection in UAV Images
[WACV 2024](https://openaccess.thecvf.com/content/WACV2024/html/Yamani_Active_Learning_for_Single-Stage_Object_Detection_in_UAV_Images_WACV_2024_paper.html), 2024-03-13 기준 1회 인용

## Task
- Object Detection
- Active Learning

## Contributions
- Active learning framework for single-staage object detection (YOLOv7)
- Diverse Uncertainty Aggregation (DUA)
- model performance 에 따른 weighting 으로 sample selection

## Proposed Method
<img src="/images/active_single/fig1.png" width="450px" alt="alt">

Label set 학습 -> Unlabel Set Inference -> DUA & WRP 기반 Select images -> Detector 학습

### Diversity in Uncertainty Aggregation (DUA)

<img src="/images/active_single/eq1.png" width="450px" alt="alt">
<img src="/images/active_single/eq2.png" width="450px" alt="alt">
<img src="/images/active_single/eq3.png" width="450px" alt="alt">

objectness score 에 IoU 를 곱해서 $confidence_{box}$ 로 사용 <br>
각 classification score 에 $confidence_{box}$ 를 연산 <br>
(2) 수식으로 uncertainty 연산

각 이미지 마다 inference 로 나온 box들에 대해 class 별로 uncenrtainty 값을 average <br>
class 들에 대해 average 로 구해진 값들을 summation

<img src="/images/active_single/fig2.png" width="550px" alt="alt">

uncertainty 를 단순히 더해서 가장 높은 이미지를 선택 -> 한 class에 굉장히 많은 instances 를 포함하는 이미지를 선택<br>
uncertainty average 해서 가장 높은 이미지를 선택 -> 적은 class에 적은 instance를 포함하는 이미지 선택<br>
DUA -> 다양한 uncertainty 를 갖는 instances 를 포함하는 이미지를 선택 <br>
DUA + WRP -> weighting 으로 car가 중복으로 많이 있지 않은 것을 선택하도록

### Weighting by Performance

<img src="/images/active_single/fig3.png" width="450px" alt="alt">

validation set & training set 에서 random 으로 subset 을 생성 <br>
해당 subset 으로 class 별 AP 를 연산하고 이를 기반으로 weight vector 를 생성

<img src="/images/active_single/eq4.png" width="450px" alt="alt">

WTC (Weighting the classes by their presence) - training data 에 class 수에 따라서 <br>
WRP (Weighing by Random subset Performance) - validation set 의 random subset 에서 <br>
WTP (Weight based on the training performance) - training set 의 random subset 에서

## Experimental Results

<img src="/images/active_single/fig4.png" width="450px" alt="alt">

labeled set 수 & labeled objects 수 에 따른 실험 결과 <br>
제안하는 방법으로 image select 해서 학습했을 때 성능이 제일 좋다

<img src="/images/active_single/tab2.png" width="450px" alt="alt">

validation set 에 대한 성능 비교

<img src="/images/active_single/tab3.png" width="450px" alt="alt">

weighting 방법에 따른 실험 결과 <br>
WTC - training data 에 class 수에 따라서 <br>
WRP - validation set 의 random subset 에서 <br>
WTP - training set 의 random subset 에서

<img src="/images/active_single/fig6.png" width="450px" alt="alt">

각 방법에 따른 weights visualization

<img src="/images/active_single/tab4.png" width="450px" alt="alt">

test-dev set 에 대한 실험 결과

<img src="/images/active_single/fig7.png" width="650px" alt="alt">

실제 prediction results visualization