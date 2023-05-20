---
layout: post
title: ROC 커브와 AUC
date: 2023-05-20 18:07:00 +0900
description: 데이터 분석 입문을 위한 통계
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [study] # add tag
use_math: true
---

## 들어가면서 

​	최근들어 캐글 콘테스트에 참여중인데, 오랜만에 ROC 커브 관련 개념이 필요해 정리해봤습니다. 이번 글에서는 ROC 커브와 AUC에 대해 알아보겠습니다. 

​                 

---

​      

## ROC 커브(Receiver Operating Characteristic curve)

&nbsp; &nbsp;&nbsp;ROC 커브는 다양한 임계값을 기준으로 이진 분류 모델의 성능을 시각화하는 그래프입니다. 이진 분류 모델의 예측 결과를 기반으로, 민감도와 1-특이도간의 관계를 시각화합니다. x축에 1- 특이도를, y축에 민감도를 나타냅니다.  

  

민감도(Sensitivity)란 재현율이라고도 불리며, 양성 케이스를 실제 양성으로 맞게 분류하는 비율(True Positive Rate)입니다.
$$
\ TPR\;(True\,Positive\,Rate) = \frac{TP}{TP+FN}\
$$
이 때 FN은 False Negative의 약자로, 모델이 실제 양성 케이스를 음성으로 잘못 분류하는 경우를 의미합니다.

  

특이도(Specificity)란 실제 음성 케이스를 음성으로 맞게 분류하는 비율(True Negative Rate)입니다. 1-특이도는 음성 케이스를 양성으로 잘못 분류하는 비율, 즉 FPR(False Positive Rate)와 같습니다.  
$$
\ FPR\;(False Positive Rate) = \frac{FP}{FP+TN}\
$$
이 때 TN은 True Negative의 약자로, 모델이 음성 케이스를 음성으로 맞게 분류하는 경우를 의미합니다.  

따라서 ROC 커브는 여러 임계점을 기준으로 TPR과 FPR의 관계를 나타낸다고 볼 수 있고, TPR이 클수록, FPR이 작을수록 모델 성능이 좋다고 볼 수 있으므로 좋은 분류 모델일수록 ROC 커브는 좌측 상단에 가까워집니다. 이는 높은 민감도와 높은 특이도를 동시에 가진 모델을 의미합니다. 

  

## AUC (Area Under the Curve)

&nbsp;&nbsp;&nbsp;AUC는 ROC 커브의 곡선 아래 면적을 의미하며, 모델의 전체적인 성능을 나타내는 지표입니다. AUC는 0부터 1 사이의 값을 가지며, 1에 가까울수록 모델의 성능이 높다고 판단됩니다. AUC 값이 0.5에 가까울경우, 모델의 예측 성능은 무작위 수준과 비슷하다고 판단됩니다. 이는 다양한 임계값에서 모델의 TPR과 FPR 변화를 종합하여 성능을 평가하기 때문에, 임계값에 민감하지 않고 일반화된 성능 평가를 제공합니다. 또한, 불균형한 데이터셋에서도 신뢰할 수 있는 성능평가를 가능하게 합니다. 



​                                 



  

