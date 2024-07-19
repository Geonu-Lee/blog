---
title: "SimpleNet: A Simple Network for Image Anomaly Detection and Localization"
date: 2024-07-19 17:00:00 +09:00
categories: [Paper Reading]
tags:
   [
    Anomaly Detection
   ]
use_math: true
---   
# SimpleNet: A Simple Network for Image Anomaly Detection and Localization

[CVPR 2023](https://openaccess.thecvf.com/content/CVPR2023/html/Liu_SimpleNet_A_Simple_Network_for_Image_Anomaly_Detection_and_Localization_CVPR_2023_paper.html), 2024-07-19 기준 122회 인용

## Task
- Anomaly Detection

## Contributions
- shallow feature adaptor 를 통해서 local feature 를 target domain으로 transfer
- simple anomaly feature generator 를 통해서 anomaly feature 를 생성
- binary anomaly discriminator 를 통해서 anomaly feature 와 normal featrue 를 구분
- 기존 방법들 대비 빠른 inference speed

<img src="/images/simplenet/fig2.png" width="450px" alt="alt">

기존의 방법보다 성능와 inference speed 가 향상됨

## Proposed Method

<img src="/images/simplenet/fig3.png" width="650px" alt="alt">

### Fetaure Extractor & Adaptor
ResNet-like backbone 으로 feature 추출 <br>
$L$ level 의 hierachies features 를 사용 <br>
본 논문에서는 WideResnet50 을 사용 <br>
2, 3 번째 layer feature 를 사용 -> 4번째 layer 는 ImageNet classification 을 위한 정보가 많이 집약

$q_{h,w}^{i} = G_{\theta}(o_{h,w}^{i})$

MLP 를 통해서 target domain 으로 featrue adaptation

### Anomalous Feature Generator & Discriminator

$q_{h,w}^{i^-} = q_{h,w}^{i} + \epsilon$

Adpatation 된 feature 에 Gaussian distribution 의 random noise 를 더해 anomaly feature 를 생성

2-layer MLP 를 통해서 생성된 anomaly feature 와 normal feature 를 구별

### Loss function and Training

<img src="/images/simplenet/loss.png" width="450px" alt="alt">

truncated $l1$ loss 로 학습

<img src="/images/simplenet/algorithm.png" width="450px" alt="alt">

Pytorch-like Algorithm <br>
굉장히 simple 하다

## Experimental Results

<img src="/images/simplenet/tab1.png" width="650px" alt="alt">

MVTec AD 데이터셋에 대해서 Average 성능이 가장 좋다

<img src="/images/simplenet/fig8.png" width="650px" alt="alt">

anomaly image에 대한 Qualitative 결과 <br>
개인적인 견해로는 Gaussian distribution 으로 noise 를 추가해서 anomaly feature 를 만들기 때문에 anomaly map 이 roughly 하게 나오는 것 같다