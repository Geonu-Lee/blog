---
title: "Sparse R-CNN: End-to-End Object Detection with Learnable Proposals"
date: 2024-04-03 10:00:00 +09:00
categories: [Paper Reading]
tags:
   [
    Object Detection
   ]
use_math: true
---

# DETRs with Collaborative Hybrid Assignments Training
[CVPR 2021](https://openaccess.thecvf.com/content/CVPR2021/html/Sun_Sparse_R-CNN_End-to-End_Object_Detection_With_Learnable_Proposals_CVPR_2021_paper.html)
, 2024-04-03 기준 973회 인용

## Task
- Object Detection

## Contributions
- propose purely sparse method for object detection in images
- 이전 방법들은 완전한 sparse 방법이 아니다
  - Faster R-CNN : anchor box 의 size 와 ratio 같은 요소들로 인해 density 정도가 차이나고 그에 따라 결과 차이가 크다 --> 즉, sensitive to heuristic assign rules
  - DETR : N개의 object query 와 이미지의 global feature 가 interact 해야하므로 pure sparse가 아니다

<img src="/images/sparse_rcnn/fig1.png" width="650px" alt="alt">

<img src="/images/sparse_rcnn/fig2.png" width="450px" alt="alt">

DETR 은 학습시간도 오래걸린다 <br>
기존의 Dense 한 방법들과 비슷한 학습 시간에 더 좋은 성능을 보여준다

## Proposemd Method

<img src="/images/sparse_rcnn/fig3.png" width="450px" alt="alt">

Sparse box - N개의 learnable proposal box ( N X 4 ) <br>
Sparse features - N개의 learnable proposal features ( N X C )  C = 256 <br>


**Dynamic instance interactive head** <br>
learnable proposal features 를 활용해서 sparse box로 구해진 ROI feature 와의 interaction 

> ROI feature will interact with corresponding proposal features to filter out ineffective bins and outputs the final object feature

학습 과정 (2~7 과정을 k 번 반복) <br>
Backbone feature extractor 로부터 feature 추출 <br>
N 개의 proposal box를 통해서 RoI feature 얻기 <br>
Proposal feature 를 self-attention → object 간 관계 파악 <br>
Proposal feature를 통해서 2개의 1x1 convolution weight 생성 <br>
RoI feature를 2개의 convolution 에 통과 <br>
classification layer, bounding boxes layer 통과 <br>
예측된 bboxes, proposal features 를 다음 stage 입력으로 사용

<img src="/images/sparse_rcnn/fig4.png" width="450px" alt="alt">


k-번째 dynamic instance interaction pseudo-code (3~5 번 과정) <br>
proposal features를 활용해서 2개의 1x1 convolution weight를 만든다 <br>
만들어진 convolution weights를 통해서 roi features에 적용

<img src="/images/sparse_rcnn/eq1.png" width="450px" alt="alt">

Inference <br>
N 개를 그대로 사용 (100개) <br>
학습과정에서 no object ground truth 랑 매칭 되면서 이미지에 없는 애들은 no object 로 prediction 될 것


## Experimental Results

<img src="/images/sparse_rcnn/tab1.png" width="650px" alt="alt">

Sparse R-CNN-R50 > Faster R-CNN-R50, Faster R-CNN-R101 <br>
*은 learnable proposal box를 300개 사용, random crop augmentation 추가

<img src="/images/sparse_rcnn/tab2.png" width="650px" alt="alt">

COCO 2017 test-dev set 결과 <br>
Test-time augmentation (TTA) 적용시에도 성능 향상