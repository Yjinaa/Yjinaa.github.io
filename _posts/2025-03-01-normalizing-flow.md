---
title: 내가 이해한 Normalizing Flow
date: 2025-03-01 14:59:00 +0900
description: 우도와 확률의 개념
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
toc: true
toc_sticky: true
tags: [AI] # add tag
use_math: true
categories:
  - Study Notes
sidebar:
    nav: "blog"
---


Normalizing Flow는 생성모델 중 하나이다. 즉, 간단한 분포(주로 정규 분포)에서 샘플링한 $z$를 복잡한 분포로 변환시켜 생성한다. <br/>

학습시에는 $x$에서 $z$로, Input을 간단한 분포로 매핑하는 변환을 학습한다. 반대로 추론시에는 $z$에서 $x$로, 간단한 분포에서 복잡한 분포로 변환되어야 한다. 학습한 flow를 역으로 거쳐가는 것이다. 즉 normalizing flow는 **가역함수**의 활용을 필요로 한다. 
<br/>
<br/>
고양이 이미지 생성 모델을 예로 들어보자. 모델이 학습해야 할 것은 고양이 이미지들이 가지는 패턴과 분포를 학습하여, 새로운 샘플이 같은 분포를 따르도록 하는 것이다. 즉, 고양이 이미지들이 존재하는 확률 분포를 학습하는 것이다. 이는 고양이 이미지 $x$의 확률 밀도값, 즉 $x$가 나올 가능성을 최대화 하는 것과 같다.
<br/>

변환 과정에서 $y = f(x)$는 중간과정의 데이터이며, 우리가 필요한 것은 변환 후 확률밀도 $p(y)$이다. NF는 단순한 분포에서 샘플링한 $z$를 $f(x)$를 거쳐 변환하는 방식이므로,  변환 이전 확률밀도 $p(z)$를 알고 있다. 따라서 $f(x)$로 변환된 이후의 $p(y)$도 계산할 수 있다.
<br/>

$z$에 $f(x)$를 적용해가며 복잡한 분포로 변환하는데, 이 때 변환 시 확률밀도값이 어떻게 바뀌는지 계산하기 위해 필요한 것이 야코비안 행렬의 행렬값이다. (= 확률 밀도 변화량)
변환후의 확률 밀도를 구하려면, 원래 확률 밀도 $p(z)$에 변환의 야코비안 행렬식(=det)을 곱해 $p(y)$를 알 수 있다.
<br/>

그런데 이 야코비안 행렬의 행렬값을 구하기 위해서는 $f(x)$가 역변환이 가능해야한다. 그래서 normalizing flow의 성립 조건이 $f(x)$가 가역적이어야 한다는 것이다.
<br/>

즉, nf의 구조적 특성과 더불어 확률 밀도 계산의 두 가지 측면에서 nf의 성립 조건이 가역함수여야 한다는 결론이 나온다.
<br/>

흔히 nf에서 affine coupling layer를 쌓는다고 하는데, 이게 $f(x)$를 계속해서 적용해가는 과정이다. 즉, $n$개의 레이어를 쌓는다고 하면 $n$개의 $f(x)$를 순차적으로 적용하는 것이 된다.
<br/>
<br/>
<br/>

# reverse = True와 False

코드를 작성하다보면, training에서는 reverse = False 옵션으로, infer에서는 reverse=True 옵션을 사용하게 된다. 이 이유는 다음과 같다.
<br/>

##  forward pass (reverse=False)

forward pass는 복잡한 분포 $x_i$로부터 $z$를 만들어내는 과정이다. 즉 주어진 데이터 $x_i$에서 단순한 분포 $z$를 만들어내도록 $f^{-1}(x)$를 곱해준다.
<br/>

$$
logp(x_i) = logp(z) - log\Bigg\vert det\bigg(\frac{\partial f_i^{-1}(x_i)}{\partial x_i}\bigg)\Bigg\vert
$$

<br/>

만일 레이어가 $n$개라면,
<br/>

$$
logp(x_i) = logp(z_n) + \sum_{i=1}^nlog\Bigg\vert det\bigg(\frac{\partial f_i^{-1}}{\partial z_{i-1}}\bigg)\Bigg\vert
$$

<br/>
식은 이렇게 될 것이고, 이 $z$는 역변환을 통해 단순화된 확률분포라고 가정하기 때문에(만일 그렇지 않다면 모델 성능이 어느 순간 정체될 것이다... 이 때는 $n$의 개수를 늘리든가 해야한다. $x$의 분포가 꽤나 복잡해서 $z$를 단순화하기에는 레이어 수가 충분치 않다는 의미) 수학적으로 명확히 정의되어 있는 아래의 식을 통해 $p(z)$를 계산할 수 있고, 그렇다면 우리는 $logp(x_i)$를 계산할 수 있다.
<br/>
<br/>

<div style="text-align: center;">
$$
p(z) = \frac{1}{(2\pi)^{d/2}}exp\bigg(-\frac{1}{2}z^{\top}z\bigg)
$$
</div>

<br/>

$logp(x_i)$는 $x_i$의 확률밀도 값, 즉 학습 데이터 $x_i$가 모델이 표현하는 확률 분포에서 등장할 가능성을 나타낸다. 모델은 이를 최대화하도록 학습되며, 학습 과정에서 손실함수로 음의 로그 우도(Negative Log-Likelihood, NLL)을 최소화하는 방식으로 최적화된다.
<br/>

단, 이 때 $z$가 충분히 단순화되지 않았다면 $p(z)$는 매우 작은 값이 나올 것이고(정규분포에서 $z$가 나올 가능성이므로) 이는 $p(x)$도 매우 작은 값이 나올 가능성이 크다. 그래서 모델 성능이 계속해서 나아지지 않을 가능성이 크다. (=> 이럴 때 $n$을 늘려줘야 한다는 말)
<br/>

다시 돌아가서, $p(x_i)$를 최대화하는 방향으로 모델이 학습된다는 말은, 손실함수는 음의 로그 우도이므로 $-logp(x_i)$를 최소화한다는 말과 같다. 이렇게 가중치가 업데이트되며 모델링 식이 완성된다.

<br/>

## infer(reverse=True)

여기서는 $z$를 복잡한 분포 $x$로 변환한다. 이미 training 과정에서 모델링이 끝났으므로 모델이 할 일은 그저 정규분포에서 샘플링한 $z$를 식에 통과만 시켜주면 된다. 아까는 $x$에 $f{-1}$를 가했지만, 지금은 $z$에 $f$를 가해 $x$를 생성하는 것이다.
<br/>

# Affine Coupling layer

앞서 normalizing flow의 성립 조건이 두가지라고 언급했다.
<br/>

- $x$ → $z$(training), $z$ → $x$ (sampling)과 같은 과정에서 invertible이 쉬워야 함(역변환해서 generate해야 하기 때문)
- Jacobian Determinant 계산이 쉬워야 함 (Jacobian 계산의 computational cost가 높기 때문)

<br/>

=> 이러한 두 가지 조건을 충족하기 위해 affine transformation이 도입되었다.
<br/>

이 방식은 아래 식을 통해서도 알 수 있듯이 반반 나눠서(copling layer), 선형 변환을 한 쪽만 수행(affine transformation)하는 것이다.

<br/>

<div style="text-align: center;">
$$
y_{1:d} = x_{1:d}
$$
</div>
<br/>
<div style="text-align: center;">
$$
y_{d+1:D} = x_{d+1:D} \cdot exp(s(x_{1:d})) + t(x_{1:d})
$$
</div>

<br/>

입력을 두 부분으로 나누고, 한 부분을 그대로 유지한 채 나머지 부분을 변환하는 방식을 사용한다. 이 때, 변환함수 $s(x)$와 $t(x)$는 작은 neural network이며, $s(x)$는 스케일링(scale), $t(x)$는 이동(translation)역할을 한다. 이러한 구조를 사용하면 가역성을 유지하면서도 야코비안 행렬식을 쉽게 계산할 수 있다.
<br/>
<br/>

Normalizing Flow는 확률 밀도 추정과 샘플링을 동시에 수행할 수 있는 생성 모델로, 가역적인 변환을 활용해 복잡한 분포를 효과적으로 모델링할 수 있다. 이러한 특성 덕분에 다양한 연구 및 실무 분야에서 사용될 수 있으며, 효율적인 변환 구조를 설계하는 것이 핵심이다. Normalizing Flow는 확률 밀도 추정과 샘플링을 동시에 수행할 수 있는 강력한 생성 모델이다. 핵심 개념을 이해하면, 다양한 응용 분야에서 활용할 수 있는 가능성이 열린다.
