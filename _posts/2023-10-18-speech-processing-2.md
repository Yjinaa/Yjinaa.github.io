---
title: Speech Processing - 2
date: 2023-10-18 10:09:00 +0900
description: speech processing 요모저모 2 
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [speech processing] # add tag
use_math: true
categories:
  - Study Notes
sidebar:
    nav: "blog"
---

### parallelism
- parallel conversion = 같은 말을 하는 학습 데이터로 모델을 학습
- non-parallel conversion = 같은 말을 하지 않아도

### How much to how much
- one-to-one = 한 사람의 데이터를 받아 다른 사람의 목소리
- many-to-many = 특정 여러 사람의 목소리 ⇒ 여러사람의 목소리
- any-to-any = 누구든지 말을 해도 됨 ⇒ 누구든지의 목소리 (track one, 세상에 없던 그 누구의 목소리)
- any-to-many(target) = 누구든지 말을 하면 특정 타겟의 목소리(many, track 2)

- channel: waveform이 몇개이냐 (시그널의 개수)
- length: 파일 길이 (시그널의 길이)
- sampling rate: 1초에 44100번 샘플링했어 = 이 소리의 waveform은 44100x10초 개의 숫자로 표현됨 ⇒ 44.1kHz ⇒ 자연계의 신호의 샘플을 그 시간에서 가져옴 ⇒ rate(속도) ⇒ 1초에 44100번을 자연계 데이터를 가져와서 디지털로 기록을 했다 , 10초짜리라면 44100x10개의 숫자가 저장됨 샘플링rate가 44.1kHz

- 목청 = mono, 귀 = stereo
- floating point
- bit depth: 나는 24bit로 한번 샘플링해 ⇒ 이 소리의 waveform을 표현하는 숫자는 24bit로 양자화됨 ⇒ 24bit
- 8bit라면 256개를 나타낼 수 있음 근데 보통 16 or 24 비트 사용
- bit rate: 채널수 * Sr * bit depth → 48000 * 2 * 24 = 2304000 = 2304kbps
    - 초당 몇 비트를 프로세스 해야하느냐, 초당 비트수
    - 1초라고 생각하면 1초에 44100개의 데이터가 오간다
    - 해당 데이터 하나하나가 24비트다
    - 근데 채널이 stereo다 ⇒ 441000 * 24 * 2 = 초당 흘러가는 처리해야하는 비트
- 핵심 네트워크에 들어가기 전에 최대한 정보를 압축 ⇒ 네트워크 통과 ⇒ 압축을 다시 확장

- 그래프에서 세로축에 대해 얼마나 잘게 쪼갤 것인가 ⇒ bit depth
- sampling rate 높아짐 ⇒ 해당 시간단위에서 더욱더 구체적으로 데이터를 나타낼 수 있다
- bit depth가 높을수록 더 정교하게 할 수 있다
- 가로축 촘촘 = sampling rate, 세로축 촘촘 = bit depth
- 무손실 파일 ⇒ bit depth와 sampling rate가 어마어마하게 높고, 압축이 일어나지않았다 뿐이지 손실은 어마어마
- 표본화 양자화 부호화

- input - wav 파일 output - fs (sampling frequency)
- shape = (268237, 2) ⇒ 시그널 하나 길이가 26만정도 되는게 두개다, 즉 26만을 8000으로 나눠야 초가 됨

### 음성 특징 추출

- 음 높이별로 각각 강도를 살펴보고싶다
- DTFT를 가장 많이 사용 : 웨이브를 frequency 대역에 대해 분석하는 함수구나 까지만
- 모든 시그널은 각 프리퀀시 안에서의 사인파의 조합으로 나타낼 수 있음
- 어떠한 시그널이 있다는건 각 주파수의 사인파들의 합으로 분석해낼 수 있다 ⇒ 푸리에변환
- 시그널을 프리퀀시의 강도로 변환하는 것이 바로 푸리에 변환, 역은 inverse 푸리에 변환
- 슬라이딩윈도우: 특정 길이(window)를 정해놓고 일정 hop length만큼 뛰어넘어 구성하는 방법
- 윈도우 length 크기만큼 잘라냄 ⇒ hop length만큼 건너뛰어가며 분석
- 잘라낸 후에는 windoing func를 더해줌, 윈도우에 대해 양 끝점을 0으로 맞춰주는 함수
    - 양 끝점이 0이 아니면 푸리에 결과가 노이즈가 많이 낌
- 적용 결과 (windowed segments) 에 대해 푸리에를 적용했다 ⇒ 주파수별 세기 결과가 나오게 됨
- 어떤 음성을 주파수 대역에 대해 분석을 했다
- 주파수 분석 해상도
- 입력길이 window length 출력길이 푸리에 transform length
- 슬라이딩 윈도우 하나하나에 대해 푸리에 연산 수행
- 윈도우 안에있는 주파수별 세기를 보고싶은 것이 푸리에 변환
- x축 주파수 y축 주파수 세기

- 분석한거 하나 한 칸을 프레임
- 스펙트럼: 주파수별 세기, 연속으로 이어 붙여서 만든게 스펙트로그램
- gram은 그림을 나타낼때 많이 쓰이니까…… 다이어그램처럼

- 목소리에는 공명이 있음, 만들어낸 주파수가 성도를 거치며 공명 음으로 바뀌고 나머지를 지나쳐오며
- 목소리를 스펙트로그램으로 나타내면 공명구조가 나타남

### pitch contour

- f0: 스펙트로그램에서 가장 밑단
- 찍은 겉선: envelop or contour
- pitch contour: f0의 겉선을 말하는거
- 공명되는 구조에 따라 선을 그리는게 pitch contur
- 주기적 성분: 하모닉 성분, 주기적이지 않은 성분: Aperiodic
- 많이 쓰이진 않음

### WORLD 라이브러리
import pyworld as pw

### mel scale
- 인간이 실제 듣는 주파수와 인지하는 주파수는 약간 다르다
- 주파수를 인간이 인지하는 단계로 나타낸 것 = ‘Mel Scale’
- 육안으로 볼 때는 큰 차이가 없어 보이지만 음성 합성시에는 Mel Scale 적용 여부가 결과물의 자연스러움에 영향을 준다고 함
- 인간이 인지하는 민감도를 반영한 주파수 비율
- 주파수를 일정하게 올리면서 들려주고 사람이 판단하는 간격으로 배치한 것
- 스펙트로그램은 대부분 일정한 주파수 간격으로 푸리에변환 되어있음
- 근데 이걸 mel scale 해서 낮은 주파수대역을 조금 늘려놓고(낮은 주파수에 인간은 더 민감) 높은 주파수 대역을 좀 늘려놓은 것이 mel spectrogram

- mel spectrum에 로그 씌우고 역변환 ⇒ MFCC ⇒ 스펙트럼 엔벨롭이 더 두드러지게 나타남(모음에 더 민감)
- 즉 MFCC는 모음을 인식하는 음성인식 분야에서 많이 쓰인다

### vocoder
- 사람의 음석을 분석한 뒤 합성
- 피처를 다시 웨이브로 만들어내는 것
- MelGAN
    - mel 스펙트로그램은 약 256배 이상 실제 신호보다 resolution이 낮기 때문에 일단 upsampling하고 시작
    - 한 칸에 보통 정보 80개
    - 그래서 처음에 8배 upsampling 두번, 그다음 2배 업샘플링 2번 우선 해줌
    - 거기에 대해 discrimination을 함
    - 잘 할 수 있었던 이유
        1. 다양한 관점에서 봤다
            1. discrimination을 하다보면 할 때 1과 0으로 판단, 진짜다 아니다, 그 순간 판단하는 1과 0 격자 안에 해당하는 파트 픽셀들은 1로 나오게만 하면 되기 때문에 실제 사람이 생각하는 자연스러운 value와는 약간 차이가 있게 됨 ⇒ raw waveform을 그냥 갖단 ㅓㅎ기도하고 avg pool해서 갖다넣기도 하고 두번해서 갖다넣기도 하고 
            2. 멜 스펙트로그램을 받아서 웨이브로 바꿔주는 친구
            3. 레이어 중간에 나오는 hidden state를 feature map