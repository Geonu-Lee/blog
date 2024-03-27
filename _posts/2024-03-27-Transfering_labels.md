---
title: "Translating Labels to Solve Annotation Mismatches Across Object Detection Datasets"
date: 2024-03-27 14:00:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection,
    Transfer Label
   ]
use_math: true
---

# Translating Labels to Solve Annotation Mismatches Across Object Detection Datasets
[ICLR 2024](https://openreview.net/forum?id=ChHx5ORqF0), 2024-03-27 기준 0회 인용

## Task
- Object Detection
- Transfer Label


## Contributions
- 데이터셋 마다 다른 annotation protocols 들로 인해서 annotation mismatches 가 있다
- 이를 manually 하게 구별하기에는 많은 비용이 들어가니 transfer label 방법을 제안
- source datasets 의 labels 를 target datasets 의 annotation style 로 transfer 하는 방법을 제안 - Label-Guided Pseudo-Labeling (LGPL)

## Motivations

<img src="/images/LGPL/fig1.png" width="650px" alt="alt">

Source Datasets <br>
MVD - Cyclist 에 대해서 사람만 annotation <br>
nuImages - Cyclist 에 대해서 사람과 자전거를 포함하게 annotation, 자전거도 annotation

Target Dataset <br>
Waymo - 사람과 자전거가 포함되게 annotation, 자전거는 annotation x

Source to Target Label Transfer 를 통해서 <br>
MVD 에서의 annotation 은 자전거까지 포함되도록 변경 <br>
nuImages 에서는 자전거 annotation 을 제거하도록 변경

## Taxonomy of Annotation Mismatches in Object Detection
<img src="/images/LGPL/fig2.png" width="650px" alt="alt">

#### Class semantics
Cityscapes - rider 와 bicycle 을 분리 <br>
Waymo - rider 와 bicycle 을 통합 <br>
nuImages - all visible bicycle

#### Annotation instructions

nuImages - not include hydraulic arms <br>
Waymo - include hydraulic arms

nuImages - Occlusion 이 발생해도 예측해서 annotation <br>
Waymo - Occlusion 발생한 곳은 제외하고 annotation

#### Human-machine misalignment
> Automatically annotated synthetic image datasets, constructed using 3-D simulated scenes, exhibit discrepancies compared to human-annotated datasets.

synthetic datasets 에 대해서 automatically 하게 annotations 했을 때 생기는 문제

#### Cross-modality labels

다른 modality label 정보를 활용해서 annotations 했을 때 생기는 문제

## Proposed Method

<img src="/images/LGPL/fig3.png" width="650px" alt="alt">

최종 목표 : source datasets label -> target datasets label style 

#### Training

source datasets 을 통해서 RPN 이 source-like proposals 을 하도록 학습 <br>
해당 RPN 을 통해서 Target Image 에서 source-like proposals 을 추출

실제 target image label 정보를 기반으로 ROI Extractor 로 얻어지는 것과 source-like porposals 을 기반으로 ROI Extractor 로 얻어지는 것을 Transfer Head 의 입력으로 활용 <br>
-> source-like proposals 이 실제 target label 로 이동할 수 있도록 Transfer Head 가 학습

> Assigning these random class labels to each proposal in training ensures that ftrans learns to correctly determine whether the region should be labeled 

class label 을 random selelct 하여 box feature 에 concatenate 한 후 Transfer Head 로 입력 <br>
-> 어떠한 방식으로 class label 을 random select 하는지 명확한 설명을 찾지 못함

#### Inference (Transfer Label of Source datasets)
학습시 source-like proposals 을 뽑기 위해서 사용했던 RPN 을 실제 source image 의 label 로 대체

<img src="/images/LGPL/transferred.png" width="450px" alt="alt">

score 값이 threshold 를 넘는 경우만 keep, 아닐 경우 drop


<img src="/images/LGPL/eq1.png" width="650px" alt="alt">

## Experimental Results

#### Datasets

<img src="/images/LGPL/datasets.png" width="650px" alt="alt">

서로 같은 class label sets 이라는 가정 <br>
class label sets 이 다른 경우 common class 만 사용

1 - 'cross-modality' mismatch, 10 classes <br>
2 - 'class sematics' , 'human-machine misalignment' mismatches, 7 classes <br>
3 - 'human-machine misalignment' mismatch, 3 classes <br>
4 - 'class semantics’ and ‘annotation instructions’ mismatches, 1 class (cyclist)


<img src="/images/LGPL/tab1.png" width="650px" alt="alt">

source datasets 이 하나일 때 실험 결과

No transfer - source datasets 의 label 을 그대로 사용 <br>
Statistical normalization (SN) - target datasets 의 mean height, width 를 활용해서 source datasets 의 label 을 scaling <br>
Pseudo-labeling (PL) - target datasets 으로만 학습된 Object detector 를 사용해서 source datasets 의 pseudo label 을 만들어서 학습 <br>
Pseudo-labeling & Noise-filtering (PL & NF) - PL 방법과 비슷하나 source datasets 의 pseudo-label 생성할때 RPN 에서 source label 과 IoU 가 0.5 보다 낮은 경우 제거

<img src="/images/LGPL/tab2.png" width="650px" alt="alt">

source datasets 이 여러 개 일때 실험 결과

<img src="/images/LGPL/fig8.png" width="650px" alt="alt">

Qualitative Results