---
title: CUDA, CuDNN, Pytorch 환경 세팅
date: 2023-09-18 21:11:00 +0300
description: CUDA, CuDNN, Pytorch 환경 세팅
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [setting] # add tag
use_math: true
categories:
  - Debugging Diaries
sidebar:
    nav: "blog"
---
## 1. Download Dataset

> MS-COCO 데이터와 wikiart 데이터 받아오기
> 
- google gsutil: 실패
- curl 명령어로 시도해서 성공
![1](https://github.com/user-attachments/assets/6c0bdd2c-350e-4a83-aa2c-f927e44a7d1b)
![2](https://github.com/user-attachments/assets/d38c1fd2-27db-4a98-a717-51b255549977)

⇒ test 파일만 사용하면 돼서 데이터셋 필요가 없었다

## 2. CUDA 설치

모든 오류 해결하고나니까 cuDNN관련 문제 뜸

처음에는 쿠다 12.2, 드라이버 535, cudnn X

쿠다 삭제 후 재설치 진행, pytorch CUDA 드라이버 cudnn 버전 호환 다 시켜야함
위 글에서 우리 그래픽카드 버전 확인해보니 버전 11.1 ~ 12.2 사용 가능

근데 구현 논문에서는 pytorch 1.5.0 사용 ⇒ 쿠다 10.2 사용해야함, python=3.6

그냥 3.8, 조교님이 버전 11이면 다 될거라고 하셔서 11.2로 정하고 설치 시작$

CUDA 11.2 ⇒ 드라이버 470 이상이면 됐는데 혹시 11.8로 업그레이드 할 것을 대비해서 525 로 설치

### CUDA 삭제

https://teddylee777.github.io/linux/ubuntu2004-cuda-update/

https://ingu627.github.io/tips/install_cuda_linux/

쿠다를 재설치하려면 기존 CUDA 삭제 필요, usr 폴더 하위의 cuda 폴더 싹 다 삭제해야 함

![스크린샷 2023-09-10 오후 6.47.41.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/c5122a34-e0ea-4e49-a2f7-079b27e47585/ebd740e0-c0e8-4a96-ac41-6c3ee9e68644/%E1%84%89%E1%85%B3%E1%84%8F%E1%85%B3%E1%84%85%E1%85%B5%E1%86%AB%E1%84%89%E1%85%A3%E1%86%BA_2023-09-10_%E1%84%8B%E1%85%A9%E1%84%92%E1%85%AE_6.47.41.png)

이후 nvidia 접속 ⇒ 원하는 버전 및 컴퓨터 운영체제 선택 ⇒ local로 다운

### CUDNN (https://normal-engineer.tistory.com/356)

1. 쿠다 버전에 맞는 CUDNN 리눅스 tar 파일로 다운
2. 해당 경로에 들어가서 압축 풀고 CUDA에 복사
    
    ```python
    sudo cp cudnn-*-archive/include/cudnn*.h /usr/local/cuda/include 
    sudo cp -P cudnn-*-archive/lib/libcudnn* /usr/local/cuda/lib64 
    sudo chmod a+r /usr/local/cuda/include/cudnn*.h /usr/local/cuda/lib64/libcudnn*
    ```
    
3. 환경변수 설정
    
    ```python
    export PATH=/usr/local/cuda-11.8/bin${PATH:+:${PATH}}
    export LD_LIBRARY_PATH=/usr/local/cuda-11.8/lib64${LD_LIBRARY_PATH:+:${LD_LIBRARY_PATH}}
    ```
    
4. 아래 경로로 환경 변수 추가 (a 누르면 수정 wq는 저장하고 나가기)
    
    ```python
    $ sudo apt-get install gedit
    $ gedit ~/.bashrc
    
    export PATH="/usr/local/cuda-11.2/bin:$PATH"
    export LD_LIBRARY_PATH = "usr/local/cuda-11.2/lib64:$LD_LIBRARY_PATH"
    
    위 두줄 추가
    $ vi ~/.bashrc 로 확인
    $ source ~/.bashrc로 적용
    ```
    

### 드라이버 설치

- mismatch 오류날 경우

```python
lsmod | grep nvidia
  886  sudo rmmod nvidia_drm # unload
# nvidia_drm is in use라고 뜰 경우
  887  sudo lsof /dev/nvidia* # nvidia 사용중인 PID 확인 후 kill
	848  dmesg
  849  lsmod | grep nvidia
  850  rmmod nvidia_drm
  851  lsof /dev/nvidia*
  852  kill 22638
  853  lsof /dev/nvidia*
  854  kill 22604
  855  lsof /dev/nvidia*
  856  kill 21926
  857  kill 8851
  858  lsof /dev/nvidia*
  859  kill 7850
  860  lsof /dev/nvidia*
  861  kill 7832
  862  lsof /dev/nvidia*
  863  rmmod nvidia_drm
  864  modprobe -r nvidia_drm
  865  lsof /dev/nvidia*
  866  who -r
  867  systemctl isolate multi-user.target
  868  rmmod nvidia_drm
  869  rmmod nvidia_modeset
  870  rmmod nvidia_drm
  871  modprobe -r nvidia_drm
  872  $ lsmod | grep nvidia
  873  lsmod | grep nvidia
  874  sudo rmmod nvidia_drm
  875  sudo rmmod nvidia_modeset
  876  sudo rmmod nvidia_uvm
  877  sudo rmmod nvidia
  878  lsmod | grep nvidia
```

### 기타

- nvcc —version 안될 때

⇒ 환경변수 추가a

```python

$ sudo apt-get install gedit
$ gedit ~/.bashrc

export PATH="/usr/local/cuda-11.2/bin:$PATH"
export LD_LIBRARY_PATH = "usr/local/cuda-11.2/lib64:$LD_LIBRARY_PATH"

위 두줄 추가
$ vi ~/.bashrc 로 확인
$ source ~/.bashrc로 적용

```

- nvidia-smi 안될 때

```python
sudo lsof /dev/nvidia*
kill PID

rmmod unload
```

- GLIBCX 관련 오류

```python
strings /usr/lib/x86_64-linux-gnu/libstdc++.so.6 | grep GLIBCX
sudo add-apt-repository ppa:ubuntu-toolchain-r/test
sudo apt-get update
sudo apt-get install gcc-4.9
sudo apt-get install --only-upgrade libstdc++6
strings /usr/lib/ your path /libstdc++.so.6 | grep GLIBCX
strings /usr/lib/x86_64-linux-gnu/libstdc++.so.6 | grep GLIBCX
```

### Pytorch 설치

https://daeun-computer-uneasy.tistory.com/60

- CUDA 버전 및 pytorch 검색해서 맞는 버전 다운로드

```python
# 터미널에서
$ python
$ import torch
$ torch.version.cuda
$ torch.backends.cudnn.version()
$ torch.cuda.is_available()
```

## 모델 돌리기

- nohup 터미널에 표시하기

```python
tail -f nohup.out
```

- 테스트 py 파일 돌리기

```python
bash test_wikiart.sh
```

- background 실행중 태스크 확인 및 중지

```python
$ bg
$ disown
```

- loader 오류

```python
load()를 safe_load()로 바꿔줌
```

- wikiart 못찾는다는 오류
```
wikiart_origin.yaml 파일을 wikiart.yaml로 바꿔줌
```