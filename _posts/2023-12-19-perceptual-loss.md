---
title: Perceptual loss 
date: 2023-12-19 18:21:00 +0300
description: Perceptual loss 조사
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [model, loss] # add tag
use_math: true
categories:
  - Study Notes
sidebar:
    nav: "blog"
---

# Perceptual loss in Image

- perceptual loss란 feature reconstruction loss, style reconstruction loss를 정의해 이미지의 style과 context를 보존하고자 하는 loss

## **Photo-Realistic Single Image Super Resolution Using a Generative Adversarial Network [[Link]](https://arxiv.org/pdf/1609.04802.pdf)**

- original image와 비교했을 때 결과물의 textual detail이 떨어짐
- 연구진들은 이 원인이 loss function에 있다고 봄
    - 기존 pixel-wise MSE를 최소화하는 방법으로 가면 high texture detail을 제대로 잡아내지 못하는 한계가 존재
    - 이전 연구와는 다르게 VGG network의 high-level feature map을 이용한 perceptual loss를 제시해 이러한 문제를 해결

**Loss function**

- Perceptual loss = Content loss + adversarial loss
- adversarial loss는 알고 있는 것과 비슷, content loss는 [Perceptual Losses for Real-Time Style Transfer and Super-Resolution](https://arxiv.org/abs/1603.08155)에서 처음 제시된 perceptual loss와 거의 동일
- 각 이미지를 pre-trained CNN모델에 통과시켜 얻어낸 feature map을 비교하는 것을 perceptual loss라고 함
- 동일한 이미지이나 한 pixel씩 오른쪽으로 밀려있는 두 이미지가 있다고 가정하면 loss가 0이어야겠지만 pixel wise loss는 0이 될 수 없음, illposed problem때문에 이러한 단점이 더 부각됨
- illposed problem: 저해상도 이미지를 고해상도로 복원할 시, 복원 가능한 고해상도의 이미지가 여러 개 존재하는 것, GAN이 여러 가능한 고해상도 이미지를 구해줘도 MSE based per pixel loss를 사용하면 possible solution들을 평균내는 결과를 취하게 되므로, GAN이 생성한 다양한 high texture detail들이 smoothing 되는 결과를 초래
- 이것을 해결하기 위해 GAN이 생성한 HR이미지와 Original HR이미지를 Pretrianed VGG 19에 통과시켜 얻은 Feature map 사이의 유클리안 거리를 구해 content loss를 구함

## Perceptual losses for Real-Time Style Transfer and Super-Resolution [[Link]](https://arxiv.org/pdf/1603.08155.pdf)

- 개별 픽셀 값을 기준으로 두 영상을 비교했을 때, perceptually 똑같아도 per-pixel loss function에 기초하여 서로 매우 다를 것
- VGG의 높은 representation을 기반으로 두 이미지 비교

# For Audio

## 1. **PERCEPTUAL LOSS FUNCTION FOR NEURAL MODELLING OF AUDIO SYSTEMS - ICASSP 2020 [[Link]](https://arxiv.org/pdf/1911.08922.pdf)**

<aside>
💡 **[요약]**
사람의 청각 인지 특성을 반영한 **A-weighting filter를 적용**해 추가적인 계산 없이 사람 귀에 더 비슷하게 들리도록 타겟 디바이스의 소리를 생성 가능했다!

</aside>

### Abstract

이전 연구에서는 원본 신호와 신경망 출력 신호 전부에 pre-emphasis filter를 적용했으며, ESR loss를 사용해 모델이 얼마나 원복을 잘하는지 측정함

- 이번 연구에서는 더 perceptually relevant한 pre-emphasis filter를 고려
    - high frequencies에서 lowpass filtering을 포함 
    → 고주파 대역에서 낮은 주파수 성분을 감소(고주파대역의 세부적인 성분을 감소)시킴으로써 인간 청각 시스템의 특성을 모방하고, 인간 귀로 들을 때 자연스러운 소리를 생성

### A-weighting filter

- relative loudness of different frequencies를 고려하도록 설정됨

$$
H(z) = 1+0.85_z^{-1}
$$

- 이는 고주파 구역의 emphasis를 줄여줌 (고주파 구역은 상대적으로 에너지가 적게 존재하는 경향, 오디오 처리 과정에서 고주파 영역을 강조하는 것을 줄이고 전체적인 오디오 신호의 균형을 맞추고 청취자의 청각적 경험을 개선)
- pre-emphasis filter는 손실을 계산하기 전에 네트워크의 출력과 목표 신호에 적용됨, 이는 입력 오디오와 목표 오디오 모두에 필터를 적용한 후, 이 필터링된 신호들을 기반으로 손실을 계산 → 모델이 손실 계산 시 특정 주파수 대역의 오류에 더 큰 가중치를 부여하게 하여, 최종적으로 모델이 지각적으로 더 우수한 오디오를 생성하도록 도움

**실험 필터 종류**

1. First-order highpass filter: 전통적으로 음성처리에서 사용되어 낮은 주파수를 억제
2. Folded differentiator filter: 높은 주파수와 낮은 주파수를 모두 감쇠시키는 것이 목적
3. A-weighting filter: 인간의 청각 인식을 모방해 특정 주파수 대역을 강조
중간 주파수 대역(대략 500Hz에서 4kHz 사이)에서 가장 민감한 인간의 청각을 고려, 중간 주파수 대역을 강조하고 매우 낮거나 높은 주파수 대역의 소리를 감쇠시킴(인간의 청각인식곡선에 기반)
- 모든 필터들은 훈련중에만 사용되며, 실행시 추가적인 계산 비용 없이 모델 성능을 개선

## 2. **Perceptual audio loss function for deep learning [[Link]](https://arxiv.org/pdf/1708.05987.pdf)**

<aside>
💡 **[요약]**
MSE와 같은 메트릭은 MOS와 같은 subjective 메트릭과 correlation이 적음
PESQ와 같은 Perceptual한 메트릭으로 평가하고 싶은데, PESQ는 특성상 미분 불가능하며 샘플 단위 평가가 불가해서 활용하기 어려움
→ WaveNet의 large receptive field를 이용해서 PESQ 알고리즘을 모방해보자!
→ PESQ 결과를 주고 WaveNet이 이를 예측할 수 있도록 학습시킴
**Conclusion :** 0.25 길이의 샘플을 통해 PESQ값과 WaveNet의 예측값을 비교했을 때, 81%의 상관관계를 보임

</aside>

- PESQ 알고리즘 모방
    - 비선형이고 메모리를 가지고 있어 오디오 샘플 사이의 장기적인 의존성 고려함 → MSE처럼 샘플단위 안되고 differentiable한 함수로 표현이 힘듦, 오디오 신호의 전체적인 품질을 평가
- 따라서 Wavenet을 활용, 확장된 합성곱을 사용해 매우 큰 receptive field를 가지고있어 오디오 샘플 사이의 장기적인 의존성을 잡아낼 수 있음
- Wavenet을 훈련해 clean 오디오와 degraded 오디오를 입력으로 받아 이를 통해 PESQ 점수를 예측하도록 함 ⇒ PESQ 결과를 사용해 PESQ 점수를 예측하는 방식으로 훈련됨
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/4ea136f3-5df0-4c67-984c-786662dca6ae/Untitled.png)
    
- $P$ = differentianle loss function(trained to learn PESQ mapping)         $\lambda$ = [0,1]
- 오디오 샘플 길이 0.25초, 화자 아이덴티티를 condition으로 줌 → 화자별 더 정확한 PESQ 점수 학습 가능
- 즉 PESQ와 같은 미분 불가능한 복잡한 평가 척도를 모방할 수 있는 새로운 미분 가능한 손실함수를 개발함
- 0.25 길이의 샘플을 통해 PESQ를 측정했을 때, 81%의 상관관계를 보임

## 3. **PERCEPTUAL LOSS BASED SPEECH DENOISING WITH AN ENSEMBLE OF AUDIO PATTERN RECOGNITION AND SELF-SUPERVISED MODELS - ICASSP2021 [[Link]](https://arxiv.org/pdf/2010.11860.pdf)**

<aside>
💡 **[요약]**
Speech denoising은 perceptual quality 향상에 어려움을 겪고 있음 ⇒ 각기 다른 음성 특징에 대한 pre-trained 모델의 앙상블로 perceptual loss를 생성해보자!
→ 6개 사용 = speaker classification, acoustic event model, speaker embedding, emotion classification, 2 self-supervised speech encoders
→ loss는 각 모델의 loss + STFT feature-domain L1 loss 총 7개 사용
→ 그러나 7개를 모두 사용했을 때는 모델의 성능이 저하됨을 발견
→ hand-tuning하여 acoustic event model + L1 loss로 해당 성능을 복원할 수 있었음
→ 최신 MTL 방법들이 greedy hand-tuning 기반 weight 선택을 능가하지 못한다!
→ PASE+ 모델과 acoustic event model이 가장 효과적이었음

</aside>

- 음성 디노이징은 인지적인 퀄리티를 향상시키는 데 어려움을 겪고 있음
    - Perceptual Ensemble Regularization Loss 사용
- 6 large-scale pre-trained model를 이용해 분석
    - speaker classification, acoustic model, speaker embedding, emotion classification, 2 self-supervised speech encoders(PASE+, wav2vec2.0)
    - 즉, 다양한 음성 특성에 대한 왜곡을 최소화함으로써, 이러한 개별 손실들을 결합함으로써 전체적인 perceptual loss가 형성
- Conformer Transformer Networks를 이용해 강력한 베이스라인을 구축
+ 다양한 사전 훈련된 모델과 결합해 perceptual loss 계산
- 한 번에 하나씩 보조 모델을 사용하며, acoustic event model과 self-supervised 모델 PASE+가 효과적인 것으로 밝혀짐
- 베스트 모델은 PERL-AE, only acoustic event model만 사용
- denoising이 full framework를 활용 가능하냐를 탐색하기 위해, 모든 네트워크를 사용해봤지만 7개의 로스 식이 Multi-Task Learning에 어려움을 겪었음
- 즉, sota Multi Task 가중치 학습 방법이 hand tuning을 능가하지 못함, 도메인 불일치와 loss들의 약한 상호보완성 때문일 수 있음
- 다양한 네트워크와 손실 함수들이 서로 상호 보완이 되지 않고, 다른 데이터 도메인간의 불일치로 인해 최적 성능 달성이 어려움

- simple l1 로스와 Conformer Transformer로 강력한 베이스라인 구축
- PERL이라는 framework 제안, 6개의 오픈소스 large-scale pre-trained networks를 사용함
    - perceptual loss로 speech denoising analyze
- speexh tasks에서 perceptual loss training의 self-supervised representations의 유용성을 입증
- l1 enhancement loss와 conformer componenets가 sota를 달성하기 위해 중요함을 입증
- PERL의 7개의 로스를 전부 사용해서, sota의 멀티태스킹 가중치 학습 방법은 hand-tuned 가중치를 능가할 수 없음을 입증

- l1 loss는 denoising에서의 왜곡 문제 해결 불가
- perceptual loss training을 위해 다양한 타입의 사전 훈련된 speech model의 앙상블인 PERL 제안
    - acoustic event classification, speaker embedding, acoustic model, speech emotion recognition + self-supervised speech encoders
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/9d9da34d-94d6-4314-a7db-1f626b38d480/Untitled.png)
    

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/cd79dc2f-6b22-43b0-99f4-4f63edc84358/Untitled.png)

- $n_i$ = loss를 뽑아내기 위해 사용하는 층의 개수
- 마지막 항은 단순한 STFT feature-domain L1 손실을 나타냄, 신호의 주파수 영역 특징을 보존하는 데 중요
- 각 손실은 특정한 음성의 특성에 초점을 맞추며, 각각 이벤트, 음향, 화자, 감정, PAE+ 및 wav2vec에 해당하는 손실임, 각 손실은 해당 특성에 대한 왜곡을 최소화 하는 데 기여
- $n_i$는 perceptual loss를 유도하는 데 사용된 해당 보조 네트워크의 내부 레이어 수
- i번째 네트워크에서 파생된 perceptual loss는 합산되고 $n_i$를 통해 나눠짐
- 보조 네트워크들은 고정되어있으며 출력 공간을 제한하는 데 도움을 줌, 이는 transfer learning, knoledge distillation, multi-task learning과는 다른 접근방식
- flexible 하기 때문에, denoising 이외에도 voice conversion도 쓸 수 있고 domain adaptation도 뛰어남
    - 근데 각기 다른 도메인과 feature로 훈련되었기 때문에 domain mismatch가 상당할 수 있음 → initial layer 사용 권장
    - 테스트셋 라벨이 모든 태스크에 대해 준비되어 있지 않기 때문에 evaluation은 enhancement metric만 보여줌
- 해당 프레임워크 PERL는 개별적으로, 특정 조합으로도 효과적임
- acoustic event와 PASE+ 모델이 가장 효과적인 것으로 나타남
- regular feature-domain enhancement loss의 combining이 상호 보완적임을 발견함
- Ablation study에서, PERL의 모든 구성 요소를 사용할 때, 7가지 로스 프레임워크가 다중 작업 학습에서는 어려움을 겪고 성능 손실을 겪는 것을 발견함
- 이를 위해 여러 MTL 방법들을 실험하여, 최신 MTL 방법들이 greedy hand-tuning 기반 weight 선택을 능가하지 못하지만, 흥미롭게도 이 시스템은 최적의 손실로 (L1 + AcousticEvent)로 미세 조정하여 손실된 성능을 복원할 수 있음을 결론냄
- 향후에는 PERL을 더 rich한 테스트 setup에서 분석해 보편적인 향상을 위해 나아가고
- SESQA와 같은 자동 음성 품질 정량화 네트워크를 통합하고
- 주 네트워크와 보조 네트워크 사이의 도메인 불일치 완화를 위한 bridge/adaptor를 학습할 수 있다

## 5. **PERCEPTUAL ANALYSIS OF SPEAKER EMBEDDINGS FOR VOICE DISCRIMINATION BETWEEN MACHINE AND HUMAN LISTENING - ICASSP 2023 [[Link]](https://ieeexplore.ieee.org/document/10094782/authors#authors)**

<aside>
💡 **[요약]**
→ (1) 새로운 speaker embedding loss 식을 제안하고, (2) 인간의 음성 인식과 모델의 speaker embedding 간 차이를 분석

→ 현재 speaker embedding은 latent 표현이 훈련 과정 중 명시적으로 감독 되지 않기 때문에, 클래스 내 변동성으로 인해 성능이 저하될 가능성 존재
→ 코사인 유사도를 직접적으로 통합해보자! → latent features와 해당 class의 center간의 코사인 거리에 따라 패널티 부여
→ 훈련과정 중 크로스 엔트로피와 코사인 유사도가 통합되어 함께 감독됨 ⇒ 클래스 내 거리 최소화, 클래스 간 거리 최대화

이후 low-level acoustic features와 임베딩의 관계를 분석함
→ MFCCs가 가장 많은 정보를 공유, 하지만 스피커 임베딩이 MFCCs를 통해 포착한 정보는 많으나 MFCCs만으로는 임베딩의 변동성을 설명하기 어렵다는 결과
→ 이는 스피커 임베딩이 higher level, compositional representation을 학습한다는 것을 보여줌

→ 사람에게 테스트 결과, 사람의 예측과 모델의 예측이 70% 가량 일치
→ 그러나 noisy한 음성의 경우 사람의 예측은 거의 일관적인 반면 모델의 성능은 12% 저하됨
→ 스피커 쌍 별로 집계된 주관적 유사성 점수가 모델에 의해 예측된 유사성 점수와 유의미한 상관관계를 보임 (r=0.6)
→ 이는 모델이 두 스피커를 혼동하는 경우, 사람들 역시 그 두 스피커를 구별하는 데 어려움을 겪을 수 있다는 것

</aside>

- 인간의 음성인식과 관련해 포착하는 정보에 대해 조사
- 스피커 임베딩 공간에서의 상대적 거리는 인간 청취차가 추론하는 목소리의 유사성과 적당히 일치
    - 기계와 인간의 청취가 목소리를 구별할 때 겹치는 부분이 있음을 확인
- 스피커 임베딩이 강건한 성능을 보여주고 있지만, 임베딩 형성이 지각적으로도 관련성이 있다고 간주될 수 있는지에 대한 확장된 분석은 진행되지 않았음

- speaker discrimination 태스크를 인간 청취자에게 부여해 얻은 speaker voice similarity prediction을 모델의 예측 결과와 비교
- 각기 다른 speaker의 발화를 16kHz로 샘플링하고 64 mel-scaled friquency bands로 변환
- CNN을 통해 화자 인식
    - 5 blocks, 2d conv layer, batch norm, ReLU, MaxPooling + Linear layer to fixed size
    - final projection using linear and softmax layer가 classify 하는 데 이용됨
- unseen speaker에 대해서는, 모델은 두 발화에 대한 latent speaker embedding을 코사인 유사도를 통해 직접적으로 비교함
- 이전의 d-vector 혹은 x-vector 기반 approach는 출력 층에서 cross entropy를 통해 speaker embedding을 얻음
    - 추론에서는 마지막 레이어가 제거되고 speaker embedding이 거리 메트릭을 이용해 비교됨(코사인 유사도같은)
- 그러나, latent 표현이 훈련 과정중에 명시적으로 supervised되지 않기 때문에, 클래스 내 변동성은 남아있으며 이는 성능을 떨어뜨릴 수 있음
- 따라서 분류 과정과 latent embedding을 공동으로 supervise하여 cross entropy와 코사인 유사도를 동시에 최대화하고자 함
    - 즉, 딥러닝 과정에만 의존하지 않고 코사인 유사도를 목적함수에 직접 넣어주겠다는 것
    - latent features와 해당 class center의 코사인 거리에 따라 패널티가 부여됨
    - 따라서 훈련 과정 중 크로스 엔트로피와 임베딩 유사도가 합쳐져 감독됨
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/e93969f8-621b-4c81-ad33-2f25a4ea577e/Untitled.png)
    
    $m$ = length of the mini-batch     $z_i, y_i$ = latent, final layer outputs for the ith sample    $c_{y_i}$ = center vector of the corresponding speaker
    
    $s_i$ = binary target vector extracting the i-th speaker  $N_s$ = number of speakers  $D$   = embedding layer size
    
    - $c_{y_i}$는 sgd를 이용해 각 speaker에 대해 학습됨(lr = 0.01), 주변의 클러스터링된 각각의 표현으로
    - 서로 다른 스피커는 분리하고, 동일 스피커에 대한 거리는 최소화
- speaker 구별에 있어 모델이 어떤 acoustic feature를 사용했는지 확인하기 위해, 다양한 low-level 특징과의 관계에 따라 분석

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/d1d515cc-4c3e-4f02-93ff-c184d4dac7d0/Untitled.png)

- 이러한 특징과 스피커 임베딩간의 관계를 평가해 서로를 가장 잘 예측하는 조합을 식별
- 선형회귀 접근법 사용
- cepstral과 chroma feature가 speaker embedding과 가장 많은 정보를 공유
- 스펙트럼 특징은 임베딩에 의해 잘 예측될 수는 있었으나 임베딩 분산을 설명하진 못했음
- 스피커 임베딩이 MFCCs를 통해 포착한 정보의 양은 많으나 MFCCs만으로 speaker embedding의 변동성을 설명하기는 어렵다는 것

- 사람에게 두 음성을 주고 같은 사람인지 다른 사람인지 평가하게 함
    - (클린한 스피치 조건) 평균 70.4%의 정확도로 스피커 검증 작업을 수행했고, 이 중 69.2%가 모델에 의해 예측된 결과와 일치
    - (noisy한 스피치 조건) 평균 73.6%로 여전히 robust했으나 모델의 성능은 12% 감소함
- 피어슨 상관분석 결과, clean 조건에서는 객관적 점수와 주관적 점수 간 더 강한 상관관계가 관찰됨(r=0.65)
    - 그러나 noisy한 조건에서는 r=0.51
- 스피커 쌍 별로 집계된 주관적 유사성 점수가 모델에 의해 예측된 유사성 점수와 유의미한 상관관계를 보임 (r=0.6)
    - 이는 모델이 두 스피커를 혼동하는 경우, 사람들 역시 그 두 스피커를 구별하는 데 어려움을 겪을 수 있다는 것
- 스피커 유사성은 학습된 스피커 임베딩의 코사인 거리에 의해 objective하게 결정됨
    - 제안된 공동 loss는 baseline(x-vector approach)보다 좋은 성능과 노이즈 강건성을 보여줌
- 각 acoustic feature 하나씩만으로는 표현될 수 없음, 이는 스피커 임베딩이 higher level, compositional representation을 학습한다는 것을 보여줌
- 모델 예측과 사람의 인지적 예측은 70%의 일치율을 보임, SNR 5dB에서 인간 청취자들은 영향을 받지 않았으나 모델은 12%까지 성능이 떨어짐

## 6. **Speech Denoising with Deep Feature Losses [[Link]](https://arxiv.org/pdf/1806.10522.pdf)**

<aside>
💡 **[요약]**
denoising에 배경 소음을 제거하기가 힘듦 ⇒ 일반적인 L1 loss 사용 시 perceptually 중요한 low energy speech 정보를 부적절하게 처리한다는 것을 발견
⇒ auxiliary network의 각 activation의 차이에 대해 패널티를 부과하는 **deep feature loss** 사용
⇒ cv에서는 ImageNet을 auxiliary network로 활용했는데, 음성에는 그러한 standard한 모델 부재
⇒ 음성 classification 모델을 직접 훈련시켜 auxiliary network로 활용함

</aside>

- Under-determined case of single-channel speech denoising 어려움
    - 주어진 오디오 신호에서 음성과 노이즈를 분리하는 데 필요한 정보가 부족한 상황의 single channel speech denoising
    - 단일 채널 오디오 신호에서 복잡하고 불확실한 배경 소음을 제거하는 것이 어려운 작업임
- 두 wav 사이의 손실 계산을 위해, pretrained audio classification metwork를 이용하여 internal activation patterns를 비교함 → 각기 다른 스케일에서의 수많은 features를 비교
    
    <aside>
    👉 content loss를 본따온듯.. cnn에서 pretrained된 vgg 이용해서 초기 레이어에서 이미지의 low level 특성을 뽑아내어 이미지의 전반적인 구조와 내용을 유지 → 원본 이미지와 생성된 이미지 사이의 feature map의 차이를 기반으로 계산
    
    </aside>
    
- L1 loss를 사용했을때, 낮은 SNR에서 출력 품질 현저히 저하, 즉 지각적으로 중요한 low energy speech 정보를 부적절하게 처리
    - ⇒ pretrained 네트워크의 internal activation의 차이에 대해 패널티를 부과하는 deep feature loss 사용
    - layered network의 특성상, loss 네트워크에서 다른 depth의 feature activation은 시그널의 다른 타임 스케일에 해당
    - 이러한 activation에서의 차이에 패널티를 부과함으로써, 다양한 오디오 스케일에 대해 여러 feature들을 비교하게 됨 → 즉, 다양한 면을 종합적으로 분석할 수 있다는 것!
- 그러나 ImageNet과 같은 standard한 모델이 음성쪽엔 아직 없기 때문에, 직접 훈련시킴
    - 15 conv layers with 3*1 kernels, batch norm, LReLU units, zero padding
    - 두 가지 태스크 (scene type 맞추기, event 맞추기)
    - xavier 초기값, adam, lr $10^{-4}$, 2500 epochs (4,680 iterations for 1 epoch)
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/2558e51f-b83d-439a-abbe-bdec3cfe3090/Untitled.png)
    
    $\Beta$ = clean signal   $\theta$ = denoising network parameter   $\phi^m$ = mth feature layer of feature loss network $g(x)$ = output of the denoising network
    
    $\lambda_m$ = $\lambda=1$로 설정 후 10 에포크 훈련 후의 역수     feature loss는 weighted L1 loss로 정의됨
    
- perceptual experiments
    - Amazon Mechanical Turk에서 A/B Test

## 7. FEATURE ENHANCEMENT WITH DEEP FEATURE LOSSES FOR SPEAKER VERIFICATION - ICASSP 2020 [[Link]](https://arxiv.org/pdf/1910.11905.pdf)

<aside>
💡 **[요약]**
SV 할 때, 노이즈가 포함되니 성능이 떨어짐 ⇒ speech enhancement를 이용해보자! [6. **Speech Denoising with Deep Feature Losses [[Link]](https://arxiv.org/pdf/1806.10522.pdf)**]에서 쓰였던 Deep Feature Loss를 써보자.
→ 이전 연구와 다르게 auxiliary network의 태스크를 x-vector network task로 지정해 같도록 하자.
→ log mel-filterbank 사용해 feature domain enhance → MFCC 기반 auxiliary network와 호환성을 높이자.
→ 훨씬 더 큰 데이터셋을 써보자.
→ DFL과 FL을 혼합하여 사용해보고 비교 (DFL 단독, FL 단독, DFL+FL)

**Conclusion:** DFL 단독 사용이 가장 성능이 좋았으며, DFL + FL은 baseline보다도 성능이 좋지 않았다!

</aside>

- SV에 speech enhancement가 key 라고 여김, DFL을 이용해 enhancement해서 SV의 성능을 발전시켜보자! (x-vector based SV systems)
- [**Speech Denoising with Deep Feature Losses]** 를 참고, 그러나 다른 점은
    1. auxiliary task를 x-vector network task로 지정해 같게 함 i.e. speaker classification
    2. feature-domain을 enhance → log mel-filterbank, MFCC 기반 auxiliary network와 호환성을 높임
    3. 훨씬 더 큰 규모의 데이터셋을 사용해 proof-of-concept. 
    4. 추론 중에만 enhancement를 수행하므로 x-vector network 재훈련할 필요가 없음
- Loss
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/8c077d86-32bc-4f03-8dc3-2a630d985947/Untitled.png)
    
    - $F_n, F_c: F*T$ 행렬, n은 noisy, c는 clean한 샘플을 넣어 얻은 feature 행렬을 뜻함, F는 주파수 bin의 수, T는 프레임 개수
    - $e(\cdot)$: enhancement network, $a(\cdot)$: auxiliary network
    - $a_i(\cdot)$: i 번째 layer의 output, L = layer의 개수
    - $L_{DFL}, L_{FL}$의 계수는 1로 고정 → coefficient re-weighting scheme를 시도했지만 도움 되지 않았음
    - 3 가지의 loss function을 제안, 결론적으로는 DFL 단독 사용이 가장 좋았고, FL+DFL은 오히려 baseline보다 좋지 않은 성능을 보임

- **speaker embedding**
    - residual network
    auxiliary network로 ResNet-34-LDE를 활용, Angular Softmax loss function 활용 (LDE size=64, network has 5.9 parameters)
    - x-vector network
    2개의 네트워크를 실험 ⇒ ETDNN과 FTDNN
    ETDNN은 TDNN보다 더 좋은 성능을 보여줌, convolution layers 사이에서 interleaving dense layers함으로써
    FTDNN은 convolution layers간의 weight matrix를 two low rank matrices로 product 시킴
    ETDNN과 FTDNN의 파라미터는 각각 10M와 17M
- training
    - batchsize 128, 50 epochs, Adam, lr 0.0075 with warmup, 800frames
    - trained with mean-normalized log Mel-filterbank features
    - 이러한 normalization mismatch를 해결하기 위해, enhancement와 auxiliary사이에 online mean normalization을 시행
    - ETDNN과 FTDNN은 Mean-Variance Normalized 40-D MFCC features를 이용한 Kaldi scripts로 훈련됨
    ⇒ FTDNN의 성능이 더 좋음

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/de8cded5-223c-4f3d-a7de-9531293b95a6/Untitled.png)

## 8. PL-EESR: PERCEPTUAL LOSS BASED END-TO-END ROBUST SPEAKER REPRESENTATION EXTRACTION - IEEE ASRU 2021 [[Link]](https://arxiv.org/pdf/2110.00940.pdf)

<aside>
💡 **[요약]**
speech enhancement의 목적은 noise를 줄임으로써 인지적 퀄리티를 향상시키는 것
그러나 지나친 supression은 음성의 왜곡을 초래하고 speaker embedding extraction의 성능까지 저하시킴
⇒ robust speaker representation extraction을 위한 PL-EESR 제안

[7. FEATURE ENHANCEMENT WITH DEEP FEATURE LOSSES FOR SPEAKER VERIFICATION - ICASSP 2020 [[Link]](https://arxiv.org/pdf/1910.11905.pdf)]와 유사한 접근 방법 사용(activation을 비교)했으나 해당 논문의 단점 존재함
→ 따라서 이 단점들을 해결한 joint training network 제안

총 3단계로 학습이 이루어짐 ⇒ pre-training 1, pre-training 2, fine-tuning
→ pre-training 1: speaker representation module이 cross entropy를 사용해 훈련되고 fix 됨
→ pre-training 2: cross entropy와 speaker representation module(=auxiliary network)을 이용한 perceptual loss로 enhancement module이 훈련됨
→ fine-tuning: 두 모듈이 함께 훈련됨, initial rate=0.0001만 달라지고 나머지 조건은 pre-training 2와 같음

</aside>

### Abstract

- speech enhancement의 목적은 background noise를 줄임으로써 인지적 퀄리티를 향상시키는 것
- 그러나 지나친 supression은 음성의 왜곡을 일으키고 speaker embedding extraction의 성능을 떨어뜨림
- 이러한 문제 해결을 위해, end-to-end learning framework를 제안, PL-EESR (robust speaker representation extraction)
- 이 프레임워크는 speaker identification 태스크의 피드백과 raw 음성 시그널과 그 noisy version 간 high-level perceptual deviation에 기반하여 최적화됨
- noisy환경과 clean 환경 각각에서 speaker verification 진행

### Introduction

- [7. FEATURE ENHANCEMENT WITH DEEP FEATURE LOSSES FOR SPEAKER VERIFICATION - ICASSP 2020 [[Link]](https://arxiv.org/pdf/1910.11905.pdf)]와 유사한 approach(auxiliary network에 통과시켜 activation을 비교하는 방법)를 사용, 그러나 다른 점은
    - 위 연구에서의 auxiliary network는 speaker representation network랑은 다름
    - 토탈 파라미터가 26.1M인데, 이는 standard x-vector network보다 큼 ⇒ 두 단계 training을 거쳐야하고, enhancement모델이 verification 모델과 독립적임
    - perceptual loss가 clean speech인지 아닌지에 크게 의존함 (manually하게 선택해야 함)
    - 따라서 이 연구에서는 end-to-end joint training network를 제안함, 총 파라미터 수는 9.8M
        - perceptual loss와 cross entropy loss로 최적화되는 강건한 end-to-end speaker representation network 제안
        - task-specific enhancement module과 spaekr embedding extraction module로 구성됨
        - 먼저 임베딩 모델이 speaker identification task로 훈련되고 fix됨
        - 다음으로 enhancement 모듈이 cross entropy와 perceptual loss로 훈련됨, 이 때 auxiliary network로 speaker embedding extraction 모듈을 사용
        - 잠재적인 mismatch를 감소시키기 위해 두 모듈은 동시에 fine tuning
        
        ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/13edd200-5408-4352-b6b7-b063b3db91c8/Untitled.png)
        

### Perceptual loss

- Problem
    - 전통적으로, speech enhancement 네트워크는 clean spectrogram과 ground-truth 간 유클리드 거리로 훈련됨
    - 그러나 이는 speaker 관련 정보 손실 혹은 왜곡으로 이어질 수 있음
    - enhanced spectrogram으로 embedding task에 직접적으로 적용시킨다면, 성능이 저하될 것이며 특히 high SNR 컨디션에서는 더욱 심해짐
    - 연구진은 이가 유클리드 거리가 예측값과 ground truth간 perceptual difference를 포착하지 못하기 때문이라고 봤음
- perceptual loss
    - deep network는 히든 레이어에 따라 각기 다른 activations를 보여주고, 이는 input feature의 특징을 학습하도록 함
    - 이러한 가정에 기반해서, activation간의 거리인 perceptual loss가 제안됨
    - perceptual loss의 원리에 따르면, auxiliary network는 어떤 정보를 keep할지 정함 ⇒ 목표는 speaker relative information을 강화하는 것
    - 따라서 speaker verification network가 먼저 훈련될 수 있고, 그 후 auxiliary network로써 fix됨 ⇒ enhancement module을 위해 perceptual loss를 추출할 수 있도록

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/749115d9-efac-4d87-867c-36e733096b7d/Untitled.png)

- $\theta$ = enhance module   $\phi$ = auxiliary speaker embedding extraction module    $\phi_i$ = ith layer of embedding extraction module

### Method

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/6f627438-018f-4c9a-926a-5ae5196fa1af/Untitled.png)

- (d)를 보면, 30차원의 log-MEl spectrogram이 speech signal로부터 추출되고 이는 enhancement 모듈과 representation module에서 다 쓰임
- corpus chaanel의 영향을 제거하기 위해, channel normalization 수행
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/ecd1b987-17c8-43a1-8a19-09e65ac46036/Untitled.png)
    
    $\mu_n, \sigma_n$ = noisy channel의 mean, deviation           $k$ = index of Mel-filter        $X(k)$ = feature corresponding to $k_{th}$ MEl-filter
    
- enhanced speech에 inverse normalization 수행
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/df59e04c-a3bf-41e2-8895-de18fc094aac/Untitled.png)
    
    $\sigma_c, \mu_c$ = clean version의 mean, deviation
    
- instance normalization 수행
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/4ffcfa94-f34f-4ad6-99fc-47939b72666e/Untitled.png)
    
    $\sigma_i, \mu_i$ = 각 $\hat{s}_i$의 mean, deviation
    

**enhancement module**

- temporal context 정보를 효과적으로 활용하기 위해, 3개의 BLSTM(128)과 하나의 fc layer가 enhance module에 존재
- sigmoid가 fc layer에 적용됨 (0에서 1까지의 mask를 생성하기 위함)

**verification module**

- x-vector 채택, 첫 5개의 TDNN 레이어가 frame-level information추출
- 2개의 fc layer가 utterance-level의 정보 추출
- 두 단계는 global average pooling layer로 연결됨
- 각 레이어의 activation이 어떤 영향을 미치는지 알지 못하기 때문에, 모든 5개의 TDNN 레이어와 첫 번째 fc layer를 training에서의 perceptual loss 계산에 사용
- 추론 시 speaker embedding은 첫 번째 fully connected layer에서 추출됨

**Training Strategy**

- [7. FEATURE ENHANCEMENT WITH DEEP FEATURE LOSSES FOR SPEAKER VERIFICATION - ICASSP 2020 [[Link]](https://arxiv.org/pdf/1910.11905.pdf)]의 speech enhancement network를 훈련하기 위해 (1)이 사용됨
- 이는 (b)에 묘사되어 있음
- clean utterance가 target으로 사용되고, noisy utterance가 target의 auxiliary network의 동일한 activation을 가지도록 훈련됨
- 그러나, (1) 식의 gradient는 오직 noisy utterance의 enhancement에만 관심있음 ⇒ 이는 clean utterance에 잠재적으로 안좋은 영향을 끼칠 수 있음
- 그래서, 연구진은 (c)와 (d)의 훈련 방식을 제안
- mask가 생성되어 clean utterance에 적용되고, enhancement module은 각 noisy와 clean utterance의 두 gradient를 모두 사용해 최적화됨
- enhancement 모듈의 목표는 noisy utterance와 clean utterance가 일관된 representation을 갖도록 하는 것
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/854f206c-7255-43f3-8752-bf0e7f2dc9a9/Untitled.png)
    
    - $X, S$ = noisy/clean utterance     $j$ = 6
- 좋은 speaker representation network는 같은 화자로부터의 utterance distance는 최소화하고, 다른 화자간 distance는 최대화하는 것
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/f3bb2563-bda6-4961-a68e-f31bd61557e3/Untitled.png)
    
    - 이 cross entropy loss는 흔한 loss 식임, 서로 다른 화자 간 거리를 최대화 하기 위한
    - $\phi(\theta(S))$ = speaker representation module에서 예측된 speaker label    $y$ = ground truth speaker label
    - 직관적으로, (6)에서의 softmax는 intra-class discrimination을 크게 하지만 inter-class distance에는 영향을 끼치지 않음
    - 이러한 문제를 해결하기 위해, entropy loss와 perceptual loss를 동시에 사용함
    
    ![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/cb2384fe-4dd0-4320-bb10-91d51f7e6ad9/Untitled.png)
    
    - perceptual loss는 inter-class distance의 criterion으로 볼 수 있음
    - $\lambda$ = intra-class와 inter-class distance의 균형을 맞춰주는 상수, 여기서는 0.5로 설정
    
    **4.3. Training**
    
    - Pre-training + finetune
    - pre-training
        - (Pre-training 1) speaker representation module - batch size 512, SGD, lr=0.2 and decreases by half when the loss decrease ratio in each epoch is less than 0.01
        - early stopping (lr이 연속으로 2번 줄어들면)
        - vox_clean_aug dataset 사용, 이 단계에서는 speaker representation 모듈에 cross entropy loss만 사용됨
        - 이렇게 훈련된 speaker representation module은 고정되고 auxiliary network로 사용됨
        - (Pre-training 2) enhancement module은 preceptual loss와 cross entropy loss를 동시에 사용해 최적화됨
        - 3 쌍의 clean과 noisy utterance가 사용됨 (vox_noisy/vox_clean, vox_noisy_aug/vox_clean,  vox_clean_aug/vox_noisy_aug)
        - batch size 128 (noisy 64 +clean 64), Adadelta optimizer, initial lr 0.3, decreases and early stopping은 pre-training 1과 동일
    - finetune
        - speech enhancement module과 speaker representation module이 함께 finetune됨 ⇒ potential misamatch를 줄이기 위해
        - initial rate=0.0001 이외에는 pre-training 2와 모든 과정이 똑같음
    

### ++ **GE2E**

- LSTM layer + linear layer
- $e_ji$와 각 중심점 $c_k$ 사이의 유사도를 계산 (코사인 유사도 사용)
- $S_{ji,k}$ = 스피커 j의 i번째 발화 임베딩 $e_{ji}$와 모든 스피커 중심점 $c_k$간의 스케일된 코사인 유사도 행렬

![Untitled](https://prod-files-secure.s3.us-west-2.amazonaws.com/13d9be09-c03c-4519-ade0-7c372fd1cb54/7ed263dc-6126-4898-8c45-242bc9b1c4f9/Untitled.png)

- 소프트맥스 함수는 유사도 행렬에 적용되어 각 임베딩이 해당 스피커의 중심점에 가깝게 위치하도록 함, 각 임베딩 벡터가 자신의 스피커 중심점에 가까워질수록 손실 감소 (k=j일 경우 소프트맥스 출력이 1에 가까워지고 그렇지 않으면 0에 가까워지도록)

