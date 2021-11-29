---
layout: post
title: M1 텐서플로우 설치기
date: 2021-11-29 22:11:00 +0300
description: arm64 기반 M1에서 텐서플로우를 설치해봅시다.
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [tensorflow,M1,arm64] # add tag
use_math: true
---

## **들어가면서** 

&#160;&#160; 이전의 텐서플로우(2.4 이하) 버전들은 M1에서 설치하기가 까다로웠고, 실제로 저는 여러 충돌로 실행이 안되어(...) 계속 미루다가 이제는 더 이상 미룰 수 없게 되어 도전해본 텐서플로우 설치기입니다. 다행히도, 2.5버전 이후부터는 설치가 굉장히 간단해졌습니다. 하지만 불행하게도 miniforge가 아닌 anaconda로 사용하고 계셨던 유저분들께서는... Anaconda 삭제부터 다시 진행하셔야합니다. 눈앞이 깜깜해지죠😭 하지만 저도 했으니 여러분들도 하실 수 있습니다! 시작해보겠습니다.

​       

​              

## 아나콘다 삭제

&#160;&#160;자, 위에서 기존 anaconda 환경을 사용하고 계셨던 분들은 삭제 후 miniforge를 재설치하셔야한다고 언급드렸었는데요, 이유는 바로 이러합니다. M1의 arm64 칩은 일반 칩과 작동 방법이 다릅니다. 일반 텐서플로우와 맞지 않기 때문에 다른 유저분들이 설치하듯 pip install tensorflow 했다가는 큰일이 납니다...ㅠㅠ 그래서 현재 애플에서도 아나콘다가 아닌 miniforge3를 공식적으로 지원하고 있습니다. 사실 miniforge나 anaconda나 우선 conda 환경 기반인 건 같으니까 그냥 진행해도 되지 않을까? 하시겠지만..

~~~shell
conda install -c apple tensorflow-deps
~~~

이따가 위 코드를 입력하실 때 **"PackagesNotFoundError: The following packages are not available from current channels: - tensorflow-deps"** 오류를 만나게 되실 것입니다..!

그러니 아나콘다 삭제부터 차근차근 해보도록 하겠습니다!

​              

### 1. 파일 및 관련 디렉토리 제거

1) 터미널에서 직접 삭제

<img width="567" alt="스크린샷 2021-11-25 오후 11 39 12" src="https://user-images.githubusercontent.com/71372857/143876961-4b9632c9-b5c3-4352-beb1-f9a9fe547c96.png">

위 코드를 따라 쳐주세요. 혹시 이 이후 폴더가 그대로다, 하시는 분은 2번 방법을 참고해주세요!

2) 아나콘다 라이브러리를 이용한 삭제

https://docs.anaconda.com/anaconda/install/uninstall/?highlight=uninstall

위 링크를 보면 option B에 **anaconda-clean** 라이브러리를 사용해 제거하라고 적혀있습니다.

~~~sh
conda install anaconda-clean
anaconda-clean # 각각을 삭제하기 전에 확인 메시지와 함께 모든 관련 파일 및 디렉토리 제거
anaconda-clean --yes # 각각을 삭제하라는 메시지가 표시되지 않고 관련 파일 및 디렉토리 제거
# 두 명령어 중 원하는 것을 선택
~~~

<img width="550" alt="스크린샷 2021-11-25 오후 11 40 20" src="https://user-images.githubusercontent.com/71372857/143876959-a93d0511-268f-4f92-93a3-56ed9820d7f8.png">

저는 위 명령어로 제거해주었고, 공홈에서 라이브러리로 제거한 이후 option A의 지침에 따라 마저 제거하라고 해서 그대로 했습니다!

​              

### 2. 환경 변수 제거

이후에는 환경 변수 제거를 해주어야합니다. 저는 zsh 기반인데, 혹시 몰라 bash_profile도 동일하게 해주었습니다.

1. bash_profile 환경변수 제거

~~~sh
open -e .bash_profile
~~~

위 명령어로 환경변수 설정창을 열어주고,

<img width="669" alt="스크린샷 2021-11-25 오후 11 42 05" src="https://user-images.githubusercontent.com/71372857/143876956-cb33f331-76e1-4ca5-b1e4-56bdaa3a562c.png">

위처럼 conda 관련 부분을 모두 주석처리 해주었습니다.

2. zshrc 환경변수 제거

저와 같이 zsh 기반을 사용하시는 경우 zshrc의 환경변수도 제거해주어야 합니다.

~~~sh
open -e .zshrc
~~~

동일하게 위 명령어로 설정창을 열어주고,

<img width="671" alt="스크린샷 2021-11-25 오후 11 43 46" src="https://user-images.githubusercontent.com/71372857/143876954-97943774-1972-498d-ac42-c16882cd1507.png">

conda initialize 부분을 모두 주석처리하도록 하겠습니다.

3. source

이후 **반드시** source 명령어를 사용해주어야합니다. 안그러면 환경변수 삭제가 적용되지 않아 miniforge 설치 이후에도 자꾸 anaconda 폴더를 찾습니다. ㅠㅠ

~~~sh
source ~/.bash_profile
source ~/.zshrc
~~~

이후 혹시 모르니 open ~/.zshrc로 다시 설정창을 열어 제대로 주석처리가 되어있는지 확인도 한번 해주도록 합시다.

이제 아나콘다 삭제는 완료되었습니다! 짝짝짝~

​                     



## Xcode 설치

&#160;&#160;앱스토어에 들어가서 xcode를 설치하기만 하면 됩니다. 용량이 커서 생각보다 꽤나 긴 시간이 걸리므로 다른 할 일이 있으시다면 병행하시는 것을 추천드립니다.

<img width="374" alt="스크린샷 2021-11-26 오전 12 11 28" src="https://user-images.githubusercontent.com/71372857/143876951-9d07e54a-a89a-4e35-b71f-7a77533a0cdc.png">



​       

​              

## Miniforge 설치

&#160;&#160;&#160;&#160;드디어 Miniforge 설치 차례입니다! 이제부턴 정말 간단합니다.

https://github.com/conda-forge/miniforge

위 링크로 접속해서, 

<img width="671" alt="스크린샷 2021-11-25 오후 11 53 23" src="https://user-images.githubusercontent.com/71372857/143876946-2b3b4894-fbc8-40b4-83cb-0c878fc045fb.png">

위에서 다섯번째 줄에 있는 apple silicon이라고 쓰여있는 파일을 다운로드합니다.

~~~sh
cd Downloads
bash Miniforge-MacOSX-arm64
~~~

위 명령어를 입력하고, 아래 지문이 나올 때 까지 엔터를 입력해줍니다.

<img width="394" alt="스크린샷 2021-11-25 오후 11 55 01" src="https://user-images.githubusercontent.com/71372857/143876945-1a1b8a0a-a90b-4e9f-8c69-e2165e438a37.png">

yes를 타이핑한 후 엔터를 한번 더 누르면 설치가 시작됩니다.



혹은, homebrew가 설치되어 있다면

~~~sh
brew install miniforge
~~~

위 명령어로 더욱 간단한 설치가 가능합니다!

​       

​              

## Tensorflow 설치

&#160; &#160;이제 본격적으로 텐서플로우 설치에 들어가보겠습니다.

우선 가상환경을 생성합니다.

~~~sh
conda create -n snoopy python=3.8
~~~

반드시 파이썬 3.8 버전을 명시해주셔야합니다!

snoopy는 제가 지은 가상환경의 이름입니다.. tmi지만 스누피를 좋아하기때문에.. 고된 텐서플로우와의 싸움 앞에 가상환경 이름이라도 보고 힘내려고 지었습니다.

<img width="539" alt="스크린샷 2021-11-26 오전 12 07 53" src="https://user-images.githubusercontent.com/71372857/143876940-b2df6b3b-1dc6-4c98-b39d-33557b06db99.png">

설치가 다 되었다면 해당 가상환경을 activate 해줍니다.

~~~sh
conda activate snoopy
~~~

이후 기본적인 라이브러리도 설치해줍니다.

~~~shell
conda install numpy pandas scikit-learn scipy seaborn matplotlib jupyter
~~~



이제 정말 텐서플로우 설치로 들어가겠습니다!

### 1) tensorflow dependencies

~~~shell
conda install -c apple tensorflow-deps
~~~

 anaconda 기반으로 설치 시 오류가 발생하는 그 요주의 명령어입니다..!

기다리다가 아래와 같이 계속 진행하겠냐는 알림이 뜨면 y를 타이핑해 계속 하겠다는 의사를 밝힙니다.

<img width="564" alt="스크린샷 2021-11-26 오전 12 17 05" src="https://user-images.githubusercontent.com/71372857/143876932-73458962-8fab-4646-bcdf-955ff4aa6798.png">

​                   

### 2) tensorflow macos

~~~shell
python -m pip install tensorflow-macos
~~~

위 명령어를 입력해줍니다. 이제 정말 거의 다 왔습니다!

​                      

### 3) metal plugin

~~~shell
python -m pip install tensorflow-metal
~~~

위 명령어까지 입력해주면 설치는 전부 끝났습니다!!

이제 jupyter에서 실행해보겠습니다.

​                      



### Bonus :: Jupyter lab

보통 주피터 노트북이 아닌 랩에서 작업하는게 익숙해져서, 랩을 사용하기 위한 설치까지 끝마쳐보겠습니다.

~~~shell
conda install jupyter lab
~~~

네.. 사실 이거면 끝입니다. 

​                            

​                        

### 실행

이제는 실행해보겠습니다! 아, 한 가지 기쁜 소식이 더 있습니다. 

이제는 keras도 tensorflow 라이브러리 내에서 import 할 수 있습니다.

이전에는 케라스를 따로 또 설치했었어야했는데, 지금은 keras의 창시자가 구글로 입사하며 tensorflow 안에 편입되었습니다.



솔직히 너무 신나고 2.5 버전이 나와서 너무 감격스럽다.

이전 버전들은 설치 과정도 너무 복잡한데다가 하나라도 버전이 어긋나면 실행조차 못하고 설치 단계에서 끙끙 앓아야 했기 때문이다...

이제는 원격 붙을 윈도우 서버도 없어서 정말 걱정하며 텐서플로우 설치법을 구글링했는데, 아나콘다 때문에 조금 험난하긴 했지만 그래도 이정도면 선방인 것 같다.

<img width="593" alt="스크린샷 2021-11-26 오전 12 27 15" src="https://user-images.githubusercontent.com/71372857/143876928-71e0015f-2efb-4f23-b2ad-8b5aae630e27.png">

그런데 와중에... 설치하는 와중에 버전업이 되었나보네요.

2.6 버전으로 잘 설치된 것을 확인할 수 있었습니다!

​                     

​                                                    

## 마치며

&#160; 개인적으로 이번에 2.5버전이 나와서 정말 기쁘고 신이 납니다! 

이전에 M1으로 자연어 처리를 해야할 일이 있어 텐서플로우를 설치할 때 뭣모르고 아나콘다 베이스에서 pip install tensorflow로 설치했다가 jpype오류로 일주일 가까이 고생하고, 스택오버플로우도 전부 찾아봤지만 해결 못하고 결국 window에 원격으로 붙어서 작업했던 경험이 있었거든요...

저에게는 악마의 명령어 pip install tensorflow ㅠㅠ

아무튼 여기까지 해서 M1 텐서플로우 설치기를 끝마쳐보려고 합니다. 

모두들 읽어주셔서 감사합니다.

​       

​              

## Reference

* [[heiswicked.log]](https://velog.io/@heiswicked/M1-Tensorflow-깔끔하게-설치하는-방법)  - M1, Tensorflow '깔끔하게 설치하기'       

​               

​         

​         

​         

 

