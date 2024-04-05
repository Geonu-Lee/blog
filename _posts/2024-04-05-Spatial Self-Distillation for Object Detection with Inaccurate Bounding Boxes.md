---
title: "Spatial Self-Distillation for Object Detection with Inaccurate Bounding Boxes"
date: 2024-04-05 09:00:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection
   ]
use_math: true
---


# Spatial Self-Distillation for Object Detection with Inaccurate Bounding Boxes
[ICCV 2023](https://openaccess.thecvf.com/content/ICCV2023/html/Wu_Spatial_Self-Distillation_for_Object_Detection_with_Inaccurate_Bounding_Boxes_ICCV_2023_paper.html)
, 2024-04-05 기준 4회 인용

## Task
- Object Detection

## Contributions
- 정확하지 않은 bounding boxes 를 학습하는 방법을 제안
- 기존의 MIL 연구들은 spatial information 을 고려하지 않는다
- Spatial Self-Distillation based Object Detector (SSD-Det) 를 제안
  - Spatial Position Self-Distillation (SPSD)
  - Spatial Identity Self-Distillation (SISD)

## Motivation

<img src="/images/SSD-Det/fig1.png" width="450px" alt="alt">

Box annotation 이 잘못되어 있는 경우가 빈번하다

기존의 MIL 방법으로 할 경우 3가지 문제가 존재한다

Object Drift - 실제 bounding box와 일치하지 않음 <br>
Group Prediction - 여러 object 가 합쳐지는 경우 <br>
Part Domination - Object 의 Local part 만 보는 경우

## Proposed Method
<img src="/images/SSD-Det/fig3.png" width="650px" alt="alt">

#### Basic Box Refiner

<img src="/images/SSD-Det/eq2.png" width="400px" alt="alt">

<img src="/images/SSD-Det/eq3.png" width="400px" alt="alt">

<img src="/images/SSD-Det/eq4.png" width="400px" alt="alt">

<img src="/images/SSD-Det/fig6.png" width="650px" alt="alt">

Neighborhood sampler 를 통해서 inaccurate box annotation 으로 부터 bag of proposals 를 생성

생성된 objects 에 대한 RoI Align 을 하고 classification score 와 instance score를 연산

두 score 를 곱한 것을 최종 score 로 사용

모든 proposals 에 대한 합을 prediction 으로 두고 one-hot category label 과 loss 연산

→ Stage I

Stage I 의 Refined box 를 Input 으로 넣어서 Stage II 처리

Stage I 과 달라진 점은 FL Loss를 사용하기 위해서 classification 부분은 sigmoid 사용

Negative class loss 연산


#### Neighborhood sampler
<img src="/images/SSD-Det/eq6.png" width="400px" alt="alt">

hyper-parameter 에 따라 조절되는 것이 많다

<img src="/images/SSD-Det/fig7.png" width="650px" alt="alt">

#### Spatial Position Self-Distillation (SPSD)
SPSD 를 통해서 Bags 를 만든다

우선 Neighborhood sampler 로 Bags 를 만들고, <br>
이를 GT 로 두고 regressor 를 학습하고 여기로부터 나오는 box를 활용

<img src="/images/SSD-Det/eq7.png" width="400px" alt="alt">

#### Spatial Identity Self-Distillation (SISD)

IoU를 예측해서 selection 하는 데 사용 <br>
stage I 에서 refined 된 box를 merge 한 Box 에 대한 IoU 를 GT 로 두고 학습

<img src="/images/SSD-Det/eq8.png" width="350px" alt="alt">

## Experimental Results

<img src="/images/SSD-Det/tab1.png" width="650px" alt="alt">

제안하는 방법을 적용시켜 nosiy level 데이터를 학습했을 경우 성능이 좋다

<img src="/images/SSD-Det/tab8.png" width="450px" alt="alt">

다른 Detector 에 적용했을 때도 성능이 좋다