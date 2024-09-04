---
title: Speech Processing 요모저모
date: 2023-10-17 13:02:00 +0900
description: Speech Processing Basics
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [speech processing] # add tag
use_math: true
categories:
  - Study Notes
sidebar:
    nav: "blog"
---

# MFCC

- 오디오는 시간(가로축)에 따른 음압(세로축)의 표현, 즉 시간 영역(time domain)의 표현임
- 여기에 FFT를 수행하면 주파수(가로축)에 따른 음압(세로축)의 표현, 즉 주파수 영역(frequency domain)의 표현이 가능해지고, 이것을 Spectrum이라고 함

<details>
  <summary>FFT</summary>
  - Fast Fourier Transform, 신호를 주파수 성분으로 변환하는 알고리즘으로, 기존의 이산 푸리에 변환(DFT)을 더욱 빠르게 수행할 수 있도록 최적화한 알고리즘
</details>

- 스펙트럼을 사용하면 각 주파수의 대역별 세기를 알 수 있으니, 신호에서 어떤 주파수가 강하고 약한지를 알 수 있게 됨
- Spectrum에서 소리의 고유 특징 추출 가능, Cepstral을 통해 분석함
- MFCC는 일반적인 Spectrum이 아닌 Mel Spectrum에 대해 Cepstral 분석을 적용해 추출함

어떻게 추출?
- 악기 소리나 사람의 음성은 일반적으로 배음구조를 가짐 (Harmonics 구조)
<details>
  <summary>배음구조</summary>
  소리는 한 가지의 주파수만으로 구성되지 않음, 기본 주파수와 함께 기본 주파수의 정수배인 배음(harmonics)들로 구성됨
  예를 들어 피아노 건반에서 4 옥타브 ‘라’(440Hz)음을 연주했다면 그 소리는 기본 주파수인 440Hz 뿐만이 아니라 그 정수배인 880Hz, 그리고 그 다음 배음들까지 포함함
</details>
- 배음 구조는 악기나 성대의 구조에 따라 달라지며 배음 구조의 차이가 음색의 차이를 생성
- Spectrum에서 배음 구조를 유추해낼 수 있다면 소리의 고유한 특징을 찾아낼 수 있음, Cepstral을 통해 가능
![하모니](https://github.com/user-attachments/assets/54989d36-ee44-4b17-978a-4e0b673aee25)
- 위에서 화살표로 가리키는 곳이 피크, 각 피크들은 지배적인 주파수 영역을 가리킴, 이 피크들을 포먼트(formants)라고 함
<details>
  <summary>formant</summary>
  소리가 공명되는 특정 주파수 대역
  사람의 음성은 성대에서 형성되어 성도를 거치며 변형되는데, 소리는 성도를 지나면서 포먼트를 만나 증폭되거나 감쇠
  즉, 포먼트는 배음과 만나 소리를 풍성하게 혹은 선명하게 만드는 필터역할을 함
</details>

- 포먼트는 소리의 특징을 유추하는 중요한 단서임
- 해야할 일은 포먼트들을 연결한 곡선과 스펙트럼을 분리해내는 것임
- 해당 곡선을 Spectral Envelope라고 하고, MFCC는 둘을 분리하는 과정에서 추출함
- 사람의 청각기능은 고주파수 대역보다 저주파수 대역에서 더 민감함
- 사람의 이런 특성을 반영해 물리적인 주파수와 실제 사람이 인식하는 주파수 관계를 표현한 것이 Mel Scale임
- 이 Mel Scale에 기반한 Filter Bank를 Spectrum에 적용하여 도출해낸 것이 Mel Spectrum임
- Mel Scale은 Filter Bank를 나눌 때 어떤 간격으로 나눠야하는지 알려주는 역할을 함
- 이렇게 사람 청각기관의 특성을 반영해 최종적으로 얻어낸 Mel Spectrum에서 Cepstral 분석을 수행해 MFCC 추출 가능함

# Min, Max 값 뽑는 이유
- 277초동안 수집된 음악은 12,225,071개의 피크로 구성
- 이 모든 피크를 렌더링하는 것은 효율적인 시각화가 아님
- 따라서 적당한 길이의 샘플을 만들고 해당 샘플안의 최대값과 최소값만 수집할 것
- 나이퀴스트 이론에 따르면 어차피 하나의 사이클을 표현할 때 최대값 한개와 최소값 한 개만 알면 문제 없음 해상도는 좀 떨어질 수 있지만.
    1. 샘플레이트만큼 이터레이션을 돈다
    2. 특정 길이의 2차 샘플구간을 정하고 구간 내 최대와 최소값을 찾는다
    3. resultPeaks 배열의 짝수 인덱스에 최대값을, 홀수 인덱스에 최소값을 삽입

# Clipping
- 장비가 처리할 수 있는 신호의 세기를 넘어가는 현상
- 말 그대로 신호가 잘려나가는 것

# Yingram
- Yin algorithm의 발전된 형태
- 원래 Yin algorithm은 자기상관함수에서 미분하여 주파수가 낮아지는 부분을 찾음
- 최소값을 가지는 frame을 찾아 f0를 정하는 형태
- 임계값 처리: 미분된 상관계수 함수에서 임계값 이하의 값은 무시
- 최소값을 가지는 위치가 음성 신호의 기본 주파수에 해당
- 그런데 Yin gram은 pitch를 찾기 위한 별도의 작업을 하지 않고 자기상관계수 자체를 특징으로 사용
- controllability가 떨어진다 ⇒ time-lag axis to midi-scale로 Yingram 특징으로 변환

# Reflecting pad
- 경계효과를 줄이기 위해 활용되는 패딩, 주로 이미지나 신호처리에 사용
- 주어진 배열을 대칭으로 뒤집어 패딩으로 추가하는 방식
- 배열의 가장자리에 나타나는 경계 효과를 줄이고, 더 부드러운 연속성을 가지도록 함
- [1,2,3,4,5]가 있다면 1과 5의 주변에는 데이터가 충분치 않아 필터링이나 연산이 부드럽게 이어지지 않음
- 따라서 주어진 배열을 대칭으로 뒤집은 값을 추가함
- [3,2,1,2,3,4,5,4,3] 과 같이 패딩된 배열이 생성됨
- np.pad(arr, (2,2), mode=’reflect’)와 같은 방법으로 생성할 수 있음

# parallel VC
- 같은 말(linguistic content)을 다른 여러 사람들이 발음한 데이터셋에 대해 설계됨
- 이 경우, source speaker의 acoustic features를 target speakers의 features로 맵핑하는 acoustic models는 직접적으로 학습될 수 있다. Linguistiv features가 동일하다고 생각하면 직관적으로 이해된다.

# non-parallel VC
- 사람들마다 하는 말이 대부분 다른 데이터셋에 대해 설계된다. 당연히 parallel VC의 경우보다 더욱 challenging하다. Non-parallel VC는 크게 두 가지 카테고리로 나눌 수 있다. 첫 번째 카테고리는 non-parallel 조건을 parallel 조건으로 변환하고 parallel 방식처럼 mapping 함수를 학습하는 것이다. 예로, TTS를 통해 parallel 데이터를 생성하는 방식이 있다. 두 번째 카테고리는 linguistic representations와 speaker related representations를 분해한다. (=factorize) 그러면 conversion stage에서 source speaker의 linguistic content는 보존되는 반면 source speaker의 speaker representation은 target speaker의 representation으로 변형된다.

# speaker embedding
- 발화자의 특징과 같은 숫자들을 표현
- 타코트론의 중간중간 들어가서 이 발화자는 이런식으로 말할거니까 이렇게 하고 저 발화자는 이렇게 말할거니까 다른 계산을 해라 이렇게 스스로 학습할 수 있도록 만듦

# ESR loss

- Error-to-Signal Ratio Loss
- 오디오 처리 및 음향 신호 처리 분야에서 사용되는 손실함수
- 신호처리나 신호 복원 작업에서 모델의 성능을 측정하고 최적화하는 데 사용
- 목표: 오디오 신호의 원래 버전과 모델의 출력간의 오차를 측정하고 이를 원본 신호의 SNR 또는 SNR의 역수로 정규화(Signal-to-Noise Ratio)
- 모델이 원본 신호를 복원하고 잡음을 줄이는 데 얼마나 성공적인지를 측정, ESRLoss가 낮을수록 모델의 복원 능력이 더 우수하다고 판단 가능
- 원본 오디오 신호와 모델의 출력 사이 MSE를 계산, MSE를 원본 신호의 에너지로 나누어 SNR 계산, SNR을 역수로 취해 ESRLoss 얻음

# pre-emphasis filter
- 음성 및 오디오 처리에서 사용되는 신호 처리 필터 중 하나로, 고주파 성분을 강조하고 낮은 주파수 성분을 감소시키는 데 사용됨
- 오디오 신호 전처리하고 노이즈 감소시키거나 음성 특징을 강조
- 고주파 성분 강조 + 노이즈 감소 + 음성 특징 강조
- 전처리 단계에서 입력 신호에 pre-emphasis 필터를 적용하여 필터링된 신호를 얻고, 이를 후속 음성 처리 작업에 사용

### acoustic event model
- 오디오 신호에서 특정 음향 이벤트(경적, 문 닫히는 소리 등)을 인식하고 분류하기 위해 사용되는 인공지능 모델, 오디오 데이터에서 다양한 음향 이벤트의 특징을 학습하고 이를 바탕으로 새로운 오디오 샘플에서 해당 이벤트를 감지하거나 분류 가능