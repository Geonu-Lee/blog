---
title: "Learning Foreground-Background Segmentation from Improved Layered GANs"
date: 2024-04-25 12:00:00 +09:00
categories: [Paper Reading]
tags:
   [
    Generation,
    GAN,
    Segmentation
   ]
use_math: true
--- 

# Learning Foreground-Background Segmentation from Improved Layered GANs
[WACV 2022](https://openaccess.thecvf.com/content/WACV2022/html/Yang_Learning_Foreground-Background_Segmentation_From_Improved_Layered_GANs_WACV_2022_paper.html), 2024-04-25 기준 11회 인용

## Task
- Generation
- GAN
- Segmentation

## Contributions
- automatic 하게 이미지와 segmentation mask 를 생성
- GAN 을 통해서 foreground 와 background 를 생성
- GAN 과 segmentation network 와 alternate training

## Proposed Method

<img src="/images/foreground-background-gan/fig1.png" width="450px" alt="alt">

Layered GAN - 기존에 있던 방식
- foreground layer, background layer 가 존재
- foreground 와 background 를 각각 생성하고 합치는 방식

<img src="/images/foreground-background-gan/fig2.png" width="450px" alt="alt">

기존의 Layered GAN 에서는 문제가 존재
- All as foreground, All as background 문제
- 이를 해결하기 위해서 perturbed composition 하는 PerturbGAN 이 있지만 여전히 문제 존재

**Improving Layered GAN 구조와 Segmentation mask 를 같이 학습하는 구조를 제안**

<img src="/images/foreground-background-gan/eq1.png" width="400px" alt="alt">

<img src="/images/foreground-background-gan/eq2.png" width="400px" alt="alt">

Layered GAN 방식 <br>
background x_b 를 생성, foreground x_f 를 생성, mask 생성 <br>
이를 기반으로 composition

<img src="/images/foreground-background-gan/eq3.png" width="400px" alt="alt">

composition 할 때 perturbation 을 사용

<img src="/images/foreground-background-gan/perturbation.png" width="550px" alt="alt">

scaling, rotation, translation 사용

<img src="/images/foreground-background-gan/fig3.png" width="650px" alt="alt">

background 생성할때와 foreground 생성할때 공용으로 사용하는 latent code 를 public code 라고 부름 <br>
foreground 생성할때만 사용되는 latent code 를 private code 라고 부름

<img src="/images/foreground-background-gan/eq5.png" width="400px" alt="alt">

private code 와 생성된 이미지 x, 생성된 mask 와의 Mutual Information Maximization 위한 Loss 를 추가

<img src="/images/foreground-background-gan/algorithm.png" width="400px" alt="alt">

GAN 과 Segmentation network 를 alternate tranining

<img src="/images/foreground-background-gan/eq8.png" width="350px" alt="alt">

GAN 학습할때 segmentation network 는 regularize the background generation 할때 사용 <br>
생성된 background 의 pixel 들이 background 로 분류될 수 있도록 <br>
이 과정에서 segmentation network 는 고정

<img src="/images/foreground-background-gan/figA.png" width="650px" alt="alt">

GAN 부분의 Network 의 자세한 구조

## Experimental Results

<img src="/images/foreground-background-gan/tab4.png" width="650px" alt="alt">

제안하는 방법의 GAN 이 성능이 좋다

<img src="/images/foreground-background-gan/tab5.png" width="450px" alt="alt">

segmentation 결과도 잘나온다

<img src="/images/foreground-background-gan/fig6.png" width="650px" alt="alt">

Qualitative results