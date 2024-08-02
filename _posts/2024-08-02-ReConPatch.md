---
title: "ReConPatch: Contrastive Patch Representation Learning for Industrial Anomaly Detection"
date: 2024-08-02 17:30:00 +09:00
categories: [Paper Reading]
tags:
   [
    Anomaly Detection
   ]
use_math: true
---   
# ReConPatch: Contrastive Patch Representation Learning for Industrial Anomaly Detection

[CVPR 2024](https://openaccess.thecvf.com/content/WACV2024/html/Hyun_ReConPatch_Contrastive_Patch_Representation_Learning_for_Industrial_Anomaly_Detection_WACV_2024_paper.html), 2024-08-02 기준 27회 인용

## Task
- Anomaly Detection


## Contributions
- Construct discriminative features for anomaly detection by training a linear modulation
- Contrastive patch representation learning 방법 제안 (ReConPatch)
- MVTec AD dataset, BTAD dataset 에서 state-of-the-art (SOTA) 성능을 찍었다고 함 (그 당시에, ensemble method)

## Proposed Method

> representation biased to the natural image data, which has a gap with the target data

기존 pre-trained model 은 anomaly detection 을 위한 target data 와의 gap 이 있다는 문제 지적

> The main concept of our proposed approach is to train the target-oriented features

본 논문에서는 target-oriented features 를 학습하는 것이 목적이라고 함

### Overall structure

<img src="/images/reconpatch/fig1.png" width="750px" alt="alt">

$\mathcal{P}(x,h,w) \in \mathbb{R}^{C'}$

Patch-level features 를 뽑는 과정은 PatchCore 와 동일하게 진행 <br>
두 개의 networks 를 사용
- patch-level feature representation learning 을 위한 네트워크
- pairwise and contextual similarities 를 계산하여 contrastive loss 를 계산하기 위한 네트워크

PatchCore 와 동일하게 Corset sampling 해서 Memory Bank 에 저장하고 Inference 에 사용

### Patch-level feature representation learning

> aggregate highly similar features while repelling those with low similarity

similarity 가 높은 feature 들은 더 근접하도록, 반대로 낮은 feature 들은 더 멀어지도록

#### Pairwise similarity

<img src="/images/reconpatch/eq1.png" width="450px" alt="alt">

$
\bar{z}_i = \bar{g}(\bar{f}(p_i)), \bar{z}_j = \bar{g}(\bar{f}(p_j))
$

projected representation 에 대해서 patch-level 로 similarity 연산

<img src="/images/reconpatch/fig2.png" width="550px" alt="alt">

같은 Pairwise similarity 일지라도 (a) 인 경우에는 서로 멀어저야하고, (b)의 경우는 멀어저야함 <br>
Pairwise similarity 만으로는 충분하지 않기에 contextual similarity 사용

#### Contextual similarity

<img src="/images/reconpatch/eq2.png" width="450px" alt="alt">

k-th nearest neighbor 를 기반으로 하는 contextual similarity 계산

<img src="/images/reconpatch/eq3_4.png" width="450px" alt="alt">

expanding the query to the neighbors of neighbors

> redefined by averaging the similarities over the set of k-nearest reciprocal neighbors.

<img src="/images/reconpatch/eq5.png" width="450px" alt="alt">

contextual similarity 는 asymmetric 하기 때문에 bi-directional similarity 연산

<img src="/images/reconpatch/eq6.png" width="450px" alt="alt">

Pairwise similarity  와 Contextual similarity 를 linear combination

<img src="/images/reconpatch/eq7.png" width="450px" alt="alt">

계산된 similarity 로 가까운 feature 들은 더 가깝게, 먼 feature 들은 더 멀게 학습

<img src="/images/reconpatch/eq8.png" width="450px" alt="alt">

 similarity 를 계산하는 네트워크는 느리게 EMA 로 업데이트

>Fast training of the similarity calculation network reduces the consistency of the relationships between the patch-level features, leading to unstable training.

### Anomaly detection with ReConPatch

<img src="/images/reconpatch/eq9_10.png" width="450px" alt="alt">

PatchCore 와 동일한 방법으로 anomaly scores 연산 <br>
Memory bank 에 저장된 값과 가장 가까운 coreset 을 사용


## Experimental Results

### Ablation study

<img src="/images/reconpatch/tab1.png" width="450px" alt="alt">

coreset subsampling 비율에 따른 ablation study

<img src="/images/reconpatch/tab2.png" width="450px" alt="alt">

Dimension 크기에 따른 ablation study

<img src="/images/reconpatch/tab4.png" width="450px" alt="alt">

hierarchy levels 와 patch size 에 따른 ablation study

<img src="/images/reconpatch/tab4.png" width="450px" alt="alt">

data augmentation 에 따른 실험

> To simulate real-world scenarios, we randomly applied rotation, translation, color jitter (brightness and contrast), and Gaussian
blur.

PatchCore 는 성능이 많이 하락되지만 제안하는 ReConPatch 는 많이 하락 되지 않는다고 주장

### Anomaly detection on MVTec AD

<img src="/images/reconpatch/tab5.png" width="750px" alt="alt">

PNI - Gauss Lab, Seoul National University (ICCV 2023) <br>
전체적으로 성능이 더 우수한지는 잘 모르겠다..

<img src="/images/reconpatch/tab6.png" width="450px" alt="alt">

여러 backbone 에 대한 결과들을 ensemble 했을 때의 비교 분석 <br>
480 x 480 사이즈에 대해 Ensemble 방법에서는 Detection 성능 (image-level AUROC) 이 SOTA 라고 주장 <br>
320 x 320 사이즈에서는 SOTA


### Anomaly detection on BTAD

<img src="/images/reconpatch/tab7.png" width="750px" alt="alt">

BTAD dataset 에 대해서도 성능이 크게 향상된지는 모르겠다


### Qualitative analysis

<img src="/images/reconpatch/fig3.png" width="450px" alt="alt">

ReConPatch 학습 방법이 의도대로 잘되고 있다라는 것을 보여주는 Figure <br>
2D representation 을 위해서 UMAP 을 사용하고 visualization <br>
ReConPatch 이 학습이 진행될 수록 의도한대로 feature 의 분포가 학습된다


<img src="/images/reconpatch/fig4.png" width="450px" alt="alt">

PatchCore 와 비교했을 때 normal 과 abnormal 의 discriminability 가 높다

<img src="/images/reconpatch/fig5.png" width="750px" alt="alt">

GT 와 anomaly segmentation 결과 <br>
다른 method 와 비교는 없다