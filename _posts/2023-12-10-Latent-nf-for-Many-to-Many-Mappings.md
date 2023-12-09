---
layout: post
title: 논문 리뷰 - Latent Normalizing Flows for Many-to-Many Cross-Domain Mappings
date: 2023-12-10 00:23:00 +0900
description: 논문 리뷰
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [paper,study] # add tag
use_math: true
---

## 들어가면서 

​	최근 연구 인턴을 하면서 Normalizing flow 모델에 대해 공부중인데, 연구중인 분야 관련 필요한 논문이 있어 리뷰해보았습니다.

​                 

---

​      

# Latent Normalizing Flows For Many-to-Many Cross-Domain Mappings



## Abstract

&nbsp; &nbsp;&nbsp; &nbsp;이미지와 이미지와 텍스트의 joint representations를 학습하는 것은 이미지 캡셔닝과 같은 여러 크로스 도메인 작업의 기반이 됩니다. 이전의 연구들은 대부분 supervised 방식으로 두 도메인을 공통된 latent representation으로 매핑하는데, 연구진은 이는 두 도메인이 서로 다른 생성 과정을 거치기 때문에 다소 제한적이라고 지적합니다.

그래서 해당 연구팀은 새로운 semi-supervised framework를 제안했는데, 이는 도메인간의 공유 정보와 도메인별(domain-specific) 정보를 별도로 모델링 합니다.

해당 모델은 도메인별 정보를 위해 normalizing flow 기반 priors를 통합하는데, 이는 두 도메인간 다양한 many-to-many 매핑을 학습할 수 있도록 도와줍니다.

해당 모델은 이미지 캡셔닝과 text-to-image 합성 등 다양한 작업들에서 효과성을 입증했다고 합니다.

  

## 1. Introduction

joint image-text representation은 이미지 캡셔닝, text-conditioned image synthesis와 같은 크로스 도메인 작업들에서 적용성을 찾을 수 있습니다.

다만 이미지와 텍스트 분포는 각기 다른 생성 과정을 가지는데, 이는 두 분포의 joint 생성 모델링을 어렵게 하는 원인입니다.

최근 이미지와 텍스트의 joint 분포를 학습하는 sota 모델은 fully supervised setup에서 공통된 shared latent space로 두 도메인을 인코딩합니다.

이러한 접근은 supervised information을 공유 잠재 공간에서 모델링할 수는 있지만, 도메인 특화(domain-specific) 정보는 보장하지 못합니다.

그러나, 고려되는 도메인 (ex. 서로 다른 생성 과정을 가지는 이미지와 텍스트는 many-to-many 매핑이 필연적임)에서는 이미지에 해당하는 다양한 캡션들이 있을 수 있고, 또 반대도 마찬가지죠

그래서, many-to-many 매핑이 가능하도록 latent space에서 도메인별 vatriations도 인코딩하는 것이 매우 중요합니다.

크로스 도메인 합성의 sota 모델은 conditional distributions 학습을 위해 conditional variational autoencoders를 사용하거나 generative adversarial networks를 사용합니다.

그러나, 이러한 생성 모델들은 latent space에서 Gaussian priors를 강제합니다.

Gaussian priors는 강력한 regularization 혹은 (1)**posterior collapse**를 일으킬 수 있음 → Gaussian 사전확률은 latent space에서 복잡한 분포를 모델링 할 때 (1)**엄격한 제약**을 부과하기 때문입니다.

> posterior collapse: 가우시안 분포는 중심 근처에 데이터 포인트를 집중 시키는 경향 존재, 중심에서 멀리 떨어진 데이터 포인트는 덜 고려하게 만듦 → 다양성 저하, 즉 모델이 데이터의 복잡성을 무시하고, 모든 데이터 포인트를 잠재 공간의 동일한 영역(평균 주변)으로 매핑하려는 경향이 있음을 나타냄

따라서 이는 정확성과 크로스 도메인 생성 모델의 다양성을 심히 제한합니다.

최근 연구는 normalizing flows가 latent space에서 복잡한 분포를 모델링하는데 이점이 있다는 것을 발견했습니다.

Normalizing flows는 latent space에서 간단한 분포에서 복잡한 data-dependent prior로의 연속적인 변환을 통해  **high degree of multimodality**를 포착할 수 있습니다.

Ziegler & Rush(2019)는 normalizing flow 베이스 priors를 unconditional variational autoencoders의 잠재 공간에 적용했습니다.  (→ discrete distributions와 character-level modeling을 위함)

해당 연구팀은 현재 존재하는 크로스 도메인 생성 모델들이 이질적인 분포를 포착하는 데 있어 가진 한계를 극복하기 위해 normalizing flows 사용을 제안합니다.

그리고 새로운 semi-supervised 프레임워크를 도입하는데, 이게 바로 *Latent Normalizing Flows For Many-to-Many Mapping(LNFMM)*입니다.

Normalizing flows를 모델의 잠재 공간에서 복잡한 joint 분포를 포착하기 위해 적극적으로 활용합니다.

더욱이, 고려되는 도메인들(이미지와 텍스트)들이 서로 다른 생성 과정을 가지므로, 각 분포에 해당하는 latent representation은 공유된 크로스 도메인 정보와 도메인별 정보를 모두 포함하도록 모델링됩니다.

이미지와 텍스트가 짝을 이룬 데이터로부터의 supervised 정보에 의해 학습되는 latent dimensions는 이미지와 텍스트의 공통된 의미론적 정보를 학습합니다.

이미지와 텍스트 분포 내의 다양성, (ex. 이미지의 다른 시각적 스타일 - 색상, 질감, 구성 등 혹은 텍스트 형태-문체, 어휘, 구조 등)는 residual latent dimensions로 인코딩됩니다. → 도메인 specific variation을 보존 가능 

⇒ *잔여 잠재 차원에서 각 도메인에 특화된 변화와 다양성을 보존, 이로 인해 이미지와 텍스트 각각의 고유한 특성을 더 잘 이해하고 반영할 수 있습니다.*

따라서 해당 연구팀은 다대다 매핑에서 다른 도메인의 reference point를 기준으로 주어진 분포에서 다양한 샘플들을 합성 가능했다고 이야기합니다.

*⇒ 즉, 다대다 매핑에서 주어진 이미지(reference point)를 기반으로 여러 관련 텍스트를 생성하거나, 주어진 텍스트(reference point)를 기반으로 여러 이미지 생성 가능*

*⇒ 즉, 하나의 입력(reference point)으로부터 다양한 관련 출력을 생성할 수 있는 능력을 가지게 되었다!*

COCO 데이터셋에서 real-world의 이미지 캡셔닝과 text-to-image 합성 작업에 대해 학습한 다대다 잠재 공간에 대한 이점을 보여줬다고 합니다.

이미지 캡셔닝 작업에서 정확도와 다양성 면에서 Bleu and CIDEr 평가 메트릭으로 sota 모델을 능가했다고 함, 또한 text-to-image generation에서도 다양한 메트릭에서 sota 모델보다 향상된 점을 보여줬다고 합니다.



## 3. Method

(Related Work는 생략했습니다.)          

텍스트와 이미지가 각각 서로 다른 생성 과정을 따르며, 각각의 ground truth 분포 $p_t(x_t)$와 $p_v(x_v)$를 가지는 상황에서, semi-supervised setting 하에 텍스트와 이미지의 joint distributions $p_\mu (x_t, x_v)$를 학습하기 위해, 변분 추론을 기반으로 하는 새로운 joint 생성 모델을 고안했습니다. : Latent Normalizing Flows for Many-to-Many Mappings(LNFMM)

해당 모델은 joint 확률분포를 데이터 ${x_t, x_v}$와 latent variables $z$를 $\mu$로 파라미터화 되는 분포 $p_\mu (x_t, x_v, z) = p_\mu(x_t, x_v\vert z)p_\mu(z)$와 함께 정의했습니다.

해당 모델은 joint 확률분포를 데이터 ${x_t, x_v}$와 latent variables 
$z$를 $\mu$로 파라미터화 되는 분포 $p_\mu (x_t, x_v, z) = p_\mu(x_t, x_v|z)p_\mu(z)$와 함께 정의했습니다.

여기서 $\theta$로 파라미터화 되는 variational posterior $q_\theta(z|x_t, x_v)$를 이용해 $p_\mu(x_t, x_v)$의 likelihood를 극대화합니다.

서로 다른 생성 과정을 가진 분포들, 예를 들어 이미지와 텍스트를 공동으로 모델링하는 데 관심이 있기 때문에, latent 분포의 선택이 매우 중요합니다. 

공유된 잠재 분포로의 매핑은 굉장히 제한적일 수 있습니다. 이 논문에서는 LNFMM 모델의 variational posterior $q_\theta(z\vert x_t, x_v)$과 그 factorization(분해)에서부터 논의를 시작하고, 그 다음 normalizing flow 기반의 priors에 대해 소개합니다.. priors는  $q_\theta(z \vert x_t, x_v)$가 더욱 복잡하도록, 그리고 multimodal로 만들어주어, 다양한 다대다 매핑을 가능하게 합니다.

### Factorizing the latent posterior

연구진은 새로운 posterior distribution을 선택했는데, 이는 공유된 요소와 도메인별(domain-specific) 요소로 나뉘어 있습니다.

공유 요소인 $z_s$는 이미지-텍스트 데이터 쌍으로부터 학습되고 두 도메인의 공통적인 정보를 인코딩합니다.

도메인별 요소는 각 도메인에 유니크한 정보를 인코딩함, 따라서 잠재 공간 내 이질적인 구조를 보존합니다.

특히, $z_t$와 $z_v$를 모델의 텍스트와 이미지 분포를 모델링하는 잠재 변수로 고려합니다.

위에서 언급한 바와 같이 $z_s$는 supervised learning을 위한 공유된 잠재변수로서, 데이터 포인트 $x_t$와 $x_v$간 공유되는 정보를 인코딩합니다.

이러한 supervised information이 주어지면, 각 도메인에 specific한 residual information은 $z'_t$와 $z'_v$로 인코딩됩니다.

이는 LNFMM 모델에서 variational posterior의 factorization를 일으킵니다. →  $z_t = [z_s~z'_t]$ 와 $z_v = [z_s~z'_v]$로!

>  $z_s$: 이미지와 텍스트 간 공통되는 정보들이나 패턴을 포착하고 인코딩함, 예를 들어 이미지와 그에 해당하는 설명 텍스트의 관계 $z'_t, z'_v$: 텍스트와 이미지를 인코딩해 얻은 각 도메인의 잠재 표현
>
>  $z_t, z_v$: 각각 텍스트와 이미지 도메인별(domain-specific), → $z_t$: 텍스트 데이터의 전체적인 잠재표현, $z_v$: 이미지 데이터의 전체적인 잠재 표현 
>
> $z_t$와 $z_v$는 각각  각각 $z_t = [z_s~z'_t]$ 와$ z_v = [z_s~z'_v]$로 분해됨, 여기서 ‘ 붙은 것들은 각 도메인의 잔여 정보를 나타내며, 공유된 컴포넌트 $z_s$에 의해 포착되지 않은 도메인별 고유 정보를 인코딩




다음으로, LNFMM 모델을 디테일하게 들어가봅시다.

variational posterior을 이용해 직접적으로  $p_\mu(x_t, x_v)$의 log-likelihood를 최대화하는 것이 intractable하기 때문에, log-evidence lower bound를 도출했는데, 이는 잠재변수 $z=\{z_s, z't, z'_v\}$의 posterior 분포 학습을 위함입니다.

> → $p_\mu$의 log-likelihood를 직접 최대화하는 것은 실현 불가능, 따라서 직접 최대화하는 대신 잠재 변수 z의 사후 분포를 학습하기 위한 ELBO를 유도 (이는 variational inference에서 일반적으로 사용되는 방법으로, 문제를 근사적으로 해결하는 방법), z는 $z_s, z’_t, z’_v$ 를 포함한 집합을 나타냄

*⇒ 복잡한 joint 분포 모델링을 위해 variational inference와 ELBO를 사용하며 이 과정에서 여러 잠재변수 사용*



### *3.1 Deriving The Log-Evidence Lower Bound*

$p_\mu (x_t, x_v)$ 의 marginal likelihood를 최대화하는 것은 intractable합니다. 따라서 variational inference framework를 개발했는데, 이는 데이터의 log-likelihood의 variational lower bound를 최대화합니다.   <br/><br/>

$$\begin{align} log{p_\mu} (x_t, x_v) \ge E_{q_\theta(z\vert x_t,x_v)}[logp_\mu(x_t, x_v\vert z)] + E_{q_\theta(z\vert x_t,x_v)}[logp_\phi(z) - logq_\theta(z\vert x_t, x_v)]\end{align} $$

<br/><br/>여기서 첫 번째 텀은 reconstruction error입니다. 두 번째 텀은 variational posterior과 prior간 KL-divergence를 최소화합니다. 앞서 말한 factorization을 통해 reconstruction term을 아래와 같이 작성할 수 있습니다.   <br/><br/>

$$\begin{align} Eq_θ(z_s,z'_t,z'_v \vert x_t,x_v)[logp_μ(x_t\vert z_s,z'_t,z'_v )+logp_μ(x_v\vert z_s,z'_t,z'_v )]\end{align}$$


<br/><br/>도메인별 잠재 차원 $z'_t, z'_v$ 와 공유된 잠재 차원 $z_s$의 조건부 독립을 가정한다면 아래와 같이 단순화 될 수 있습니다.  <br/><br/>
$$
\begin{align} E_{q_{θ_1}(z_s|x_t,x_v)q_{θ_2}(z'_t|x_t,z_s)}[logp_μ(x_t|z_s,z'_t)]+E_{q_{θ_1}(z_s|x_t,x_v)q_{θ_3}(z'_v |x_v,z_s)}[logp_μ(x_v|z_s,z'_v )]\end{align}
$$
<br/><br/>chain rule을 이용하여 KL-divergence term은 아래와 같이 단순화 될 수 있습니다.  <br/><br/>
$$
\begin{align} D_{KL}(q_θ(z_s, z'_t, z'_v|x_t, x_v) \Vert p_φ(z_s, z'_t, z'_v )) = D_{KL}(q_{θ_1} (z_s|x'_t, x_v)\Vert p_{φ_s} (z_s))+\\
D_{KL}(q_{θ_2}(z'_t|x_t,z_s) p_{φ_t}(z'_t|z_s))+D_{KL}(q_{θ_3}(z'_v|x_v,z_s) \Vert p_{φ_v}(z'_v|z_s))\end{align}
$$

<br/><br/>따라서 최종 ELBO는 다음과 같이 정의됩니다.<br/><br/>
$$
\begin{align}logp_μ(x_t,x_v) ≥ E_{q_{θ_1}(z_\vert x_t,x_v)q_{θ_2}(z'_t\vert x_t,z_s)}[logp_μ(x_t\vert z_s,z'_t)]\\
+E_{q_{θ_1}(z_s\vert x_t,x_v)q_{θ_3}(z'_v \vert x_v,z_s)}[logp_μ(x_v\vert z_s,z'_v )]−D_{KL}(q_{θ_1}(z_s\vert x_t,x_v)\Vert p_{φ_s}(z_s)) \\−D_{KL}(q_{θ_2}(z'_t\vert x_t,z_s) p_{φ_t}(z'_t\vert z_s))−D_{KL}(q_{θ_3}(z'_v \vert x_v,z_s) \Vert p_{φ_v}(z'_v \vert z_s))\end{align}
$$


<br/><br/>표준 VAE formulation에서는, 이미지와 텍스트에 해당하는 priors가 정규분포로 모델링되었습니다. 그러나, 가우시안 priors는 posterior에 강한 규제를 주기 때문에 모델의 표현력에 제한을 줄 수 있습니다. 

특히, 가우시안 Prior로 최적화를 시키는 것은 posterior를 mean쪽으로 push하기 때문에, 모델의 생성력을 저하시키며 다양성을 제한합니다. 이는 이미지와 텍스트를 함께 다루는 복합 멀티 모달에 특히 더 해당하는 이야기입니다. 더불어, 가우시안 mixture model-based priors와 같은 대체재들 또한 같은 약점을 가지고 있으며, 추가적으로 복합 모델에서의 요소의 개수와 같은 미리 정의된 휴리스틱에 의존합니다. 유사하게, VamPrior는 priors를 학습하기 위해 pseudo-inputs의 미리 정의된 숫자에 의존합니다. 

그래서 연구진은 exact한 inference model을 기반으로 priors를 학습하는 Ziegler & Rush(2019), Bhattacharyya et al.(2019)와 비슷하게, 이미지와 텍스트 조건부 priors를 variational posterior와 함께 normalizing flow를 사용하여 동시에 학습하는 방법을 제안합니다. 



### *3.2 Variational Inference with Normalizing Flow-Based Priors*

Normalizing flow는 exact inference model로서, 간단한 분포를 $K_t$ 가역 매핑을 통해 복잡한 분포로 매핑시킬 수 있습니다. 이는 산간한 분포를 복잡한 멀티 모달 priors로 변환할 수 있도록 합니다. change-of-variables formula를 통해 잠재 변수의 likelihood를 얻을 수 있습니다. 

data-dependent와 non-volume preserving 변환을 통해, 멀티모달 priors는 잠재 공간에서 동시에 학습될 수 있고, 또한 더욱 복잡한 posteriors와 더 나은 evidence lower bound를 얻을 수 있게 합니다.



### *3.3 Latent Normalizing Flow Model For Many-to-Many Mappings*

모델의 전체 구조는 아래와 같습니다.

<img width="255" alt="스크린샷 2023-12-10 오전 2 19 08" src="https://github.com/Yjinaa/Yjinaa.github.io/assets/71372857/dab8648e-8f38-40d0-9aa2-a86ef7c6deab">

두 개의 도메인별 인코더가 존재합니다. 이는 각 이미지와 텍스트의 posteriors를 학습합니다.

공유 잠재 변수 $z_s$는 두 도메인의 공통된 정보를 인코딩하기 때문에, 각 인코더는 모든 $(x_t, x_v)$ 쌍에서 공통된 supervised 정보를 모델링할 수 있어야합니다. 즉, 텍스트와 이미지의 각 인코더는 각각의 데이터로부터 공통 정보를 추출할 수 있어야 하며, 이는 짝지어진 데이터 쌍 $(x_t, x_v)$에 대해 일관되게 적용됩니다.

→ 텍스트 인코더는 $x_t$로부터, 이미지 인코더는 $x_v$로부터 공통정보를 추출할 수 있어야 합니다. 연구진은 이를 output demention을 splitting 함으로써 가능하게 했습니다. 각 인코더의 출력 차원을 $z_v=[z_s~z'v]$와 $z_t=[z_s~z'_t]$로 분할함으로써 $z_v$와 $z_t$를 생성했습니다.

공유된 잠재 차원 $z_s$는 두 도메인간의 공통 정보를 인코딩하도록 제약되며, 이는 인코더가 같은 종류의 정보를 zs에 인코딩하도록 보장합니다.

공유 잠재 공간의 sharing을 가능하게 하는 가장 간단한 방법은 두 인코딩 사이의 mse를 최소화하는 것이지만, 이는 $x_t$와 $x_v$가 서로 매우 다른 생성 과정을 거치기 때문에 이상적이지 못합니다. 따라서, 가역 매핑을 통해 가역 신경망에서 $z_s$가 도메인 $t$와 $v$ 사이에서 변환될 수 있도록 했습니다. 

($d>=d')$ 차원을 가진 잠재변수 $z_v$가 분포 $p_v(x_v)$에 대해 인코딩 됐다고 합시다.

일대일 대응(bijective mapping) $f_{\phi_s}:(z_v)*{d'} \mapsto(z_t)*{d'}$는 가역 매핑을 통해 학습됩니다.

여기서 $(\cdot)_{d'}$는 잠재 코드를 d’ 차원의 shared part로 제한하는 것을 의미합니다.

$f_{\phi_s}$는 affine coupling layers를 이용한 가역 신경망입니다.  $(f_{\phi_s}=f^{K}*{\phi_s}\circ f^{K-1}*{\phi_s} \cdots \circ f^{1}_{\phi_s})$

이는 Jacobians 연산을 계산하기 쉽도록 해주는데, Jabobian 연산은 가역 신경망의 계층에 대해 삼각행렬로써 표현되기 때문입니다.

이 때, $f^{0}_{\phi_s} = I$가 단위행렬(identity)이라고 가정합니다.

도메인 specific 정보의 차원 수는, ($z't, z'v)$ 두 도메인에 따라 다를 수 있으며, 이는 각 분포의 복잡도에 달려있습니다.

supervision signal로 차원을 conditioning함으로써, 연구진은 차원을 풀지 않고도 unsupervised information에 대한 차원에서의 중복성을 최소화했다고 합니다.

 $q_{\theta_2}(z'*t\vert z_s, x_t)$와 $q*{\theta_3}(z'_v\vert z_s, x_v)$의 멀티 모달 조건부 사전 확률은 non-volumne-preserving normalizing flow 모델을 통해 학습되며, 이는 각각 $\phi_t$와 $\phi_v$에 의해 파라미터화됩니다.

데이터의 log-likelihood terms는 (negative) reconstruction errors로 정의됩니다.

$\theta_v = \{\theta_1, \theta_3\}$ 일 때, $g_{\theta_v}:x_v \mapsto z_v$와,  $\theta_v = \{\theta_1, \theta_3\}$일 때, $g_{\theta_t}:x_t \mapsto z_t$가 각각 이미지와 텍스트 인코더라고 합시다.

$h_{\omega_v}:z_v \mapsto x_v$ 그리고 $h_{\omega_t}:z_t \mapsto x_t$ 는 각각 파라미터 $\omega_v$와 $\omega_t$를 가진 이미지와 텍스트 디코더입니다.

이 때  $\tilde{x}*v=h*{\omega_v}(g_{\theta_v}(x_v))$ 와 $\tilde{x}*t=h*{\omega_t}(g_{\theta_t}(x_t))$는 디코딩된 이미지와 텍스트 샘플을 의미합니다.

텍스트에 대해서는, 텍스트 디코더로부터의 이전 reconstruced words $(\tilde{x})_{0:j-1}$에서 주어진 ground truth 문장 $x_t$의 $j^{th}$단어에 대한 출력 확률 $(x_t)_j$를 고려하고, reconstruction error을 정의합니다.

 → $$\begin{align}L^{rec}_t(x_t, \tilde{x}*t)=-\sum_jlogp*\mu((x_t)_j\vert (\tilde{x}*t)*{0:j-1})\end{align}$$

이미지에 관해서는, input image $x_v$와 reconstructed 이미지 $\tilde{x}_v$ 사이의 reconstruction error는 $L^{rec}_v(x_v,\tilde{x}_v)=\Vert x_v-\tilde{x}_v\Vert ,$  이 때 $\Vert \cdot\Vert $는 $\ell_1, \ell_2$  둘 다 가능합니다.

더불어  $logq_{\theta_1}(f_{\phi_s}((z_v)*{d'})\vert x_t)$를 $(z_v)*{d'}$를 $f_{\phi_s}$ 아래의 텍스트의 잠재 공간으로의 매핑하는데의 코스트로 정의합니다.  짝을 이루는 데이터 $(x_t, x_v)$의 인코딩된 텍스트 표현 $(z_t)*{d'}$와 변환된 이미지 표현 $f*{\phi_s}((z_v)_{d'})$ 사이에 mse를 이용합니다.

논문 초반부의 ELBO, 학습된 latent priors, 가역 매핑, 그리고 방금 정의한 reconstruction terms를 연결하여, semi-supervised generative model framework의 목적함수는 아래의 식을 최소화하는 것이 됩니다.<br/>

$$\begin{align} L_\mu(x_t, x_v) = \lambda_1D_{KL}(q_{\theta_1}(z_s\vert x_t, x_v)\Vert p_{\phi_s}(z_s))+\\\lambda_2D_{KL}(q_{\theta_2}(z_t'\vert x_t, z_s)\Vert p_{\phi_t}(z_t'\vert z_s))+\lambda_3D_{KL}(q_{\theta_3}(z_v'\vert x_v, z_s)\Vert p_{\phi_v}(z_v'\vert z_s))+\\\lambda_4L^{rec}_t(x_t,\tilde{x}_t) + \lambda_5L^{rec}_v(x_v,\tilde{x}_v)\end{align}$$



> **Loss의 구성**
>
> - $\lambda_1D_{KL}(q_{\theta_1}(z_s\vert x_t, x_v)\Vert p_{\phi_s}(z_s))$: 공유된 잠재변수 $z_s$에 대한 사후 분포와 사전 분포 사이의 KL Divergence → $z_s$가 모델링하는 공유 정보가 얼마나 잘 학습되었는지를 나타내는 지표
> - $\lambda_2D_{KL}(q_{\theta_2}(z_t'\vert x_t, z_s)\Vert p_{\phi_t}(z_t'/vert z_s))$ : 텍스트 도메인에 특화된 잠재 변수 $z_t'$에 대한 사후 분포와 사전 분포 사이의 DKL → 텍스트 데이터의 도메인별 정보가 얼마나 잘 학습되었는지를 나타냄
> - $\lambda_3D_{KL}(q_{\theta_3}(z_v'\vert x_v, z_s)\Vert p_{\phi_v})$ : 이미지 도메인에 특화된 잠재변수 $z_v'$에 대한 사후 분포와 사전 분포 사이의 DKL → 이미지 데이터의 도메인별 정보가 얼마나 잘 학습되었는지를 나타냄
> - $\lambda_4L^{rec}_t(x_t,\tilde{x}_t)$ : 텍스트 데이터에 대한 재구성 손실을 나타냄, 이는 원래의 텍스트 데이터 $x_t$와 모델에 의해 재구성된 $\tilde{x}_t$ 사이의 차이를 측정
> - $\lambda_5L^{rec}_v(x_v,\tilde{x}_v)$ : 이미지 데이터에 대한 재구성 손실을 나타냄, 이는 원래의 이미지 데이터 $x_v$와 모델에 의해 재구성된 이미지 $\tilde{x}_v$의 차이를 측정
> - $\lambda$는 이러한 다양한 항목의 중요도를 조정하는 가중치



여기서 $\mu = \{\theta, \phi_s, \phi_v, \phi_t, \omega_v, \omega_t\}$는 학습되는 파라미터들이며, $\lambda_i, i=\{1,...,5\}$는 regularization 파라미터입니다.

$\tilde{x}_t$와 $\tilde{x}*v$ 는 텍스트와 이미지 샘플들로 디코딩된다는 점을 상기시켜봅시다.  연구진은 공유된 잠재 공간에 대한 사전 확률 $p*{\phi_s}(z_s)$을 균일 분포로 가정합니다. 이러한 공유된 차원의 균일 사전 확률의 강도는 regularization 파라미터 $\lambda_1$를 통해 조절될 수 있습니다.

연구진은 모델이 양방향 다대다 매핑을 허용한다고 말합니다. 예를 들어, 이미지 도메인으로부터의 데이터 포인트 $x_v$가 주어지고 이로 부터 잠재 인코딩 $z_v$가 주어지면, 이를 텍스트 도메인으로 매핑합니다. 이 때, 가역 변환을 통해 매핑합니다. ($z_s = f_{\phi_s}(z_s)$) 

따라서 학습된 잠재 사전 확률 $p_{\phi_s}(z'_t\vert z_s)$로부터의 샘플링을 통해 다양한 텍스트를 생성해낼 수 있다고 합니다. 텍스트를 주어 이미지를 샘플링 하는 경우에도 학습된 사전 확률 $p_{\phi_s}(z'_v\vert z_s)$를 통해 유사한 절차를 따릅니다.

조건부 생성 작업의 경우, supervised 잠재 공간에서 샘플링할 필요가 없기 때문에, 균일 사전 확률 $p_{\phi_s}(z_s)$가 실제 응용에서 이점이 됩니다. ( → 디코더에 대한 제약을 완화하므로)

여기에 대안으로, 공유된 의미론적 공간에서의 샘플링을 가능하게 하는 더 복잡한 플로우 기반의 사전 확률도 사용될 수 있습니다.

연구진은 joint 잠재 normalizing flow 기반 사전 확률이 실세계 작업에 효과가 있다는 것을 보였다고 합니다. (ex. 다양한 이미지 캡셔닝과 text to image 합성)





# 마치며

&nbsp;&nbsp;&nbsp;이번 글에서는 normalizing flow에 many-to-many 매핑을 어떻게 가능하게 했는지 논문을 보며 알아보았습니다. 모델의 구조가 꽤나 복잡해서 적용을 해보기에는 조금 시간이 걸릴 것 같지만, 코드 분석부터 천천히 시작해보려고 합니다.

지적은 언제나 환영이며, 읽어주셔서 감사합니다. 😄





​        



  

