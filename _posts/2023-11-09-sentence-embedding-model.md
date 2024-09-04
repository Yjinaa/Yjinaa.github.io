---
title: Sentence Embedding - roberta base
date: 2023-11-09 16:27:00 +0300
description: roberta base sentence embedding
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [model] # add tag
use_math: true
categories:
  - Study Notes
sidebar:
    nav: "blog"
---
https://github.com/jhgan00/ko-sentence-transformers

klue의 bert-base, roberta-base 사용

** roberta-base

- BERT 모델을 더 많은 데이터로 오래, 그리고 더 큰 batch로 학습
- NSP 학습 삭제
- 더 긴 문장들에 대해 학습
- Mask를 dynamic하게 바꿔줌 (epoch마다 중복되지 않도록)

Self supervised model같은 경우 성능이 매우 뛰어나지만 어떤 학습이 성능을 향상시켰는지 분석하는 것은 쉽지 않음

분석 cost도 높음 ⇒ 모델이 크고 parameter가 많기 때문에

hypter parameter 튜닝, 학습 시켜보고 하다가 BERT가 매우 undertraining되었다는 것을 깨달음

따라서 학습 방법론을 발전시킨 RoBERTa 개발

즉, BERT 모델을 더 잘 학습시킬 수 있는 recipe들로 학습시킨 것

특히 Mask를 dynamic하게 바꿔주는 방식이 GPT로 대표되는 auto-regressive 모델과 BERT로 대표되는 Auto encoder 모델의 장점만을 합한 generalized AR pretraining model인 XLNet을 능가한다고 함

### 참고

- BERT모델을 활용했으며, 논문 방식도 그대로 활용, hidden vector size도 512로 설정, maximum length도 512로 동일하게 사용
- peak learning rate과 number of warmup steps, 즉 스케줄링면에서는 다르게 실행
- 실험적인 연구에 따라 epsilon의 term과 $\beta$값 등을 조정
- 짧은 문장은 주입하지 않았고, 문장 길이를 조정하지 않았으며 모든 길이로 적합하게 들어갈 수 있는 문장들만 학습 진행
- Data는 최대한 많이 사용하려했으며 Evaluation은 GLUE, SQuAD, RACE등 다양하게 진행

### Training

- 기존 BERT에서 사용하는 기법은 데이터를 10배 복사한 후 그것들에 대해 maksing을 해주는 것, 하지만 이는 결국 1 way가 정해지고 그것을 4번 반복하므로 동일한 mask에 대해 4번씩 학습하는 것에 불과
- 즉, 데이터들을 복사하더라도 동일하게 마스킹된 세트들이 4번씩 반복된 것(40개가 전부 다 다른 set이 아님)
- 그래서 데이터가 모델에 들어갈 때마다 masking을 새롭게 해주는 dynamic masking 개발

- NSP 제거, 이는 NLI나 QA task들에 대해서는 성능이 떨어지기는 했음
- NSP의 효용성에 대한 의문이 많아 여러 가지 set 활용
-