---
title: 생성모델의 작동원리 이해하기
date: 2024-01-19 23:55:00 +0900
description: 생성모델의 작동원리
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [AI] # add tag
use_math: true
categories:
  - Study Notes
sidebar:
    nav: "blog"
---

## **들어가면서** 

생성모델 공부를 시작하면서 생성모델의 근본적인 원리를 파악하고 정립하기 위해 개인적으로 작성했던 노트를 정리한 글입니다.
생성모델을 공부하다보면 사전확률, p, q, z 등의 개념들을 흔히 접하게 되는데, 이 개념들에 대해 차근차근 알아가볼까요?

---

#### 생성모델의 학습방식
생성모델 학습의 목적은 랜덤한 숫자를 타겟 분포와 유사한 분포로 매핑해주는 '함수'를 학습하는 것이다.

여기서 랜덤한 숫자가 바로 z, 샘플링한 값이다. 샘플링은 난수를 생성해 특정 분포를 따르게 만드는 복잡한 과정이기 때문에, 일반적으로 정규분포나 균등분포와 같은 단순한 분포를 사용한다.
이렇게 학습을 시작하기 전에 잠재변수의 분포를 정규분포를 따른다고 가정하는데, 이 가정을 사전확률이라 하며 모델이 데이터 없이 z에 대해 가지고 있는 초기 지식을 나타낸다.

예를 들어 고양이 사진을 생성하는 모델을 만들고 싶다고 하자. 고양이가 나오는 분포가 존재는 할 것이지만 매우 복잡할 것이기에 이 식을 명확하게 알아낼 수는 없다. 따라서 이미 알고 있는 분포인 정규분포에서 시작해 z를 샘플링하고, 이 z를 입력으로 하여 고양이 이미지 분포로 매핑하는 함수 f(x)를 학습하는 것이다. 모르는 p분포(여기서는 고양이)를 알기 위해 q분포(모델이 생성하는 분포)를 p분포로 근사시킨다는 말이 나오는게 이 부분이다. 모델이 생성하는 값의 분포를 실제 고양이 분포에 가깝게 만드는 것이 목표이기 때문이다.

학습 과정에서 모델이 얼마나 데이터 분포를 잘 근사하는지 평가하기 위해 우도의 개념이 쓰인다. 이전에 포스팅했던 우도와 확률에 관한 글에서 확률은 분포가 주어졌을 때 데이터의 가능성을 계산하고 우도는 데이터를 기반으로 분포(모수)의 가능성을 계산한다고 언급했었다. 생성모델에서 우도는 모델이 데이터 분포를 얼마나 잘 근사하는지 평가하는 기준으로 활용된다. 관측 데이터 x가 특정 잠재변수 z로부터 생성될 가능성을 계산한다. 생성모델은 학습 과정에서 이 우도를 최대화 하는 방향으로 파라미터를 업데이트하게 된다.

결론적으로, 랜덤한 입력  z 를 타겟 분포로 매핑하는 함수를 학습하는 과정에서, 모델은 베이즈 정리를 통해 관측 데이터를 기반으로 잠재변수와 데이터 간의 관계를 학습하고, 우도를 기준으로 데이터 분포를 최대한 잘 설명하도록 최적화된다.

이 과정을 통해 생성모델은 랜덤한 입력을 받아 실제 데이터와 유사한 결과를 생성할 수 있다.

---

## 마치며
생성모델은 단순히 데이터를 생성하는 도구를 넘어, 확률과 통계의 원리를 기반으로 데이터를 이해하고 재현하려는 접근 방식입니다. 학습 과정에서 사전확률, 우도, 베이즈 정리 등 핵심 개념이 활용되며, 이를 통해 모델은 관측된 데이터를 학습하고, 더 나아가 새로운 데이터를 생성할 수 있는 능력을 갖추게 됩니다.

이번 글에서는 생성모델의 학습 원리와 그 과정에서 등장하는 주요 개념들을 살펴보았다.
이 글이 생성모델의 핵심 개념을 이해하는 데 도움이 되었기를 바라며, 앞으로 생성모델을 공부하거나 연구할 때 유용한 참고 자료가 되었으면 좋겠습니다.😊