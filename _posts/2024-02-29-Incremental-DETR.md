---
title: "Incremental-DETR: Incremental Few-Shot Object Detection via Self-Supervised Learning"
date: 2024-03-03 23:00:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection,
    DETR
   ]
use_math: true
--- 

# Incremental-DETR: Incremental Few-Shot Object Detection via Self-Supervised Learning
[AAAI 2023](https://ojs.aaai.org/index.php/AAAI/article/view/25129), 2024-02-29 기준 16회 인용


## Task
- Object Detection
- DETR
- Incremental Object Detection


## Contribution
- DETR 기반 Incremental Object Detection 구조를 제안
- Pre-trained -> Fine-tuning, self-supervsied leaarning 구조 제안
- catastrophic forgetting problem 를 막기 위해서 knowlege distillation 구조도 제안


## Proposed Method

### Base Model Training

<img src="/images/incremental-detr/fig1.png" width="650px" alt="alt">

#### Base Model Pre-training

<img src="/images/incremental-detr/eq1.png" width="400px" alt="alt">

<img src="/images/incremental-detr/eq2.png" width="400px" alt="alt">

<img src="/images/incremental-detr/eq3.png" width="400px" alt="alt">

base class 에 대해서 일반적인 DETR 학습 방법으로 학습

#### Base Model Fine-tuning

<img src="/images/incremental-detr/proposal.png" width="400px" alt="alt">

Selective Search 으로 base class GT 와 안겹치는 부분에서 object proposal <br>
class 는 $n+1$ 로 지정

<img src="/images/incremental-detr/eq4.png" width="500px" alt="alt">

Selective Search 에 의해서 proposal 된 box 에 대해서 학습

<img src="/images/incremental-detr/eq5.png" width="400px" alt="alt">

일반 base class 에 대한 GT 에 대한 Loss + Selective Search 로 얻어진 GT 에 대한 Loss


### Incremental Few-shot Fine-Tuning

<img src="/images/incremental-detr/fig2.png" width="650px" alt="alt">

Base class 에 대해서 학습된 weights 를 initialize 로 사용 <br>
Projection Layer 와 Classification Head 만 학습 <br>
Classification Head 는 base class number + novel class number 로 새로 구성? (base class 에 대한 head weight 만 load?)

<img src="/images/incremental-detr/eq6.png" width="500px" alt="alt">

base class 에 대한 정보를 forgetting 하는 문제를 방지하기 위해서 <br>
novel class GT box를 제외한 구역에 대해서 distillation

<img src="/images/incremental-detr/eq7.png" width="450px" alt="alt">

base class logit 에 대해서도 distillation

<img src="/images/incremental-detr/eq8.png" width="450px" alt="alt">

novel class 에 대한 DETR 학습 + Feautre distillation loss + Logit distillation loss

## Experimental Results

<img src="/images/incremental-detr/tab1.png" width="650px" alt="alt">

"1-41" : base class <br>
"41" : novel class <br>
"41-80" : novel class

"1-41", "1-40" : base model 에 모두 사용해서 학습 <br>
"41 (ft)": "1-40" 으로 학습된 base model weight 에 "41" class fine tuning <br>
"41 (scratch)": "41" class 에 대해서 scratch 부터 학습 <br>
"(1-40) + (41)": "1-40" 으로 base model 학습 후 "41" 을 novel class 으로 제안하는 방법 학습

"1-80": 모든 class를 base model 학습 <br>
"41-80 (ft)" : "1-40" 으로 학습된 base model 에 novel class finetuning <br>
"41-80 (scratch)" : novel class를 scratch 부터 학습

"(1-40) + (41-80)" : "1-40" 를 base class, "41-80" 을 novel class로 제안하는 방법을 학습


<img src="/images/incremental-detr/tab2.png" width="650px" alt="alt">

class 마다의 이미지 샘플 수에 따른 Incremental few-shot object detection 실험 결과
