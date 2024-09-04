---
title: Speaker Embedding + Attention 조사
date: 2023-12-19 18:21:00 +0300
description: spk emb + attention
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [speaker embedding] # add tag
use_math: true
categories:
  - Paper Reading
sidebar:
    nav: "blog"
---
## End-to-End Trainable Self-Attentive Shallow Network for Text-Independent
Speaker Verification - 2020 [[Link]](https://arxiv.org/ftp/arxiv/papers/2008/2008.06146.pdf)

GE2E 모델은 speaker verification 분야에서 광범위하게 쓰임

그러나, LSTM은 두 가지 한계가 존재 
1) vanishing gradient - 굉장히 긴 시퀀스에 대해서는 성능 저하 유발
2) GE2E 인코더는 모든 프레임을 equal 하게 취급하고, 각 프레임이 전달하는 각기 다른 양과 형태의 정보를 모으기가 불가능함, 어떤 프레임에는 더 valuable한 정보들이 있을 수 있고 또 이외 프레임에는 별로 중요하지 않은 정보들이 있을 수 있는데, 이를 고려하지 못함. 따라서 컴퓨팅 자원이 별로 중요하지 않은 정보를 계산하는 데에도 사용됨

따라서, 이러한 문제들을 해결하기 위해 end-to-end trainable self-attentive shallow network를 제안

이는 TDNN과 self-attention 매커니즘의 결합에 기반된 임베딩 모듈 사용

TDNN은 넓은 일시적인 컨텍스트에서 discriminative 패턴을 구별하는 샘플링 방법으로서 활용, 이는 computational load를 줄여줌

또한 병렬적인 샘플링 작업이 RNN보다 훈련이 빠르게 진행되도록 함

그리고 self-attention을 기반으로 한 풀링 레이어는 모델이 어떠한 부분을 중요하게 여겨야하는지 집중할 수 있도록 하고, 임베딩 효율을 높임

GE2E와 비교했을 때, EER, DCF, AUC에서 각각 63%, 67%, 85%의 성능 향상을 보여줌

인풋 길이가 길어질때는, DCF 스코어가 GE2E 대비 17배 높았다

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/c5122a34-e0ea-4e49-a2f7-079b27e47585/2b2f8c68-4b4f-4953-b40b-f2351eea6c42/Untitled.png)

## Double Multi-Head Attention for Speaker Verification [[Link]](https://arxiv.org/pdf/2007.13199v1.pdf)

대부분의 speaker verification sota 모델은 speaker embedding extraction을 사용

이 아키텍처는 보통 feature extractor와 pooling layer를 포함, 이는 다양한 length의 utterances를 fixed-length speaker vectors로 인코딩함

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/c5122a34-e0ea-4e49-a2f7-079b27e47585/23764e42-5502-4cc6-85f1-852c833e09e9/Untitled.png)

## Serialized Multi-Layer Multi-Head Attention for Neural Speaker Embedding - 2021 [[Link]](https://arxiv.org/pdf/2107.06493v1.pdf)

이 논문에서는 직렬화된 멀티헤드어텐션을 통해 text-independent speaker verificaiton에 사용되는 스피커 임베딩을 제안한다.

이전의 연구들에서는, frame level의 feature들이 모여 utterance level의 representation을 생성한다. Transformer network에 영감을 받아, 이 논문에서 제안하는 방법은 stacked 셀프 어텐션 매커니즘의 계층적인 구조를 확용한다. 이는 좀 더 화자들과 연관된 정제된 feature들을 얻어낼 수 있다.

Werialized attention mechanism은 고정된 차원의 representation을 생성하기 위한 셀프 어텐션 모듈의 stack을 포함한다. 멀티헤드 어텐션을 병렬로 쓰는 대신에, 직렬화된 멀티 레이어 멀티 헤드 어텐션은 집중된 정보를 층간에 순차적으로 전달하고 집약한다. 

serialized attention mechanism은 N개의 stacked 레이어로 이루어져있고, 한 레이어 당 두 개의 모듈이 stack 되어있음 ⇒ self-attention + feed forward module

그리고 residual connection을 각 모듈에 적용함

layer 정규화가 self attention input과 feed forward 모듈의 전에 각각 적용됨

각 서브 레이어의 출력 결과물은 

$$
x +LayerNorm(Sublayer(x))
$$

멀티헤드 어텐션을 병렬로 활용하는 대신에, 한 레이어로부터 정보를 모으고 전달하는 것을 제안

이는 모델이 더 깊은 레이어에서 temporal한 컨텍스트를 모으는 것을 도와줌

스킵 커넥션은 셀프 어텐션 모듈 전체에 걸쳐 쓰임

n 번재 셀프 어텐션 모듈에서는 가중 평균과 가중 표준편차가 얻어지며, 이는 affine transformation을 통해 utterance level 벡터로 전환됨 ⇒ layer n의 serialized head로 볼 수 있음

최종 utterance-level embedding은 모든 헤드로부터의 utterance-level 벡터의 summation으로 이루어짐

이후 ReLu, Bath Norm을 거쳐 classifier layer의 인풋으로 들어감

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/c5122a34-e0ea-4e49-a2f7-079b27e47585/cbb75607-d6ab-4e54-9ccc-ea60c1d223e0/Untitled.png)