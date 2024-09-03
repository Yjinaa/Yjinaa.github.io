---
title: 강력한 터미널 멀티플렉서, zellij 사용법
date: 2024-02-04 12:22:00 +0900
description: About redundancy error and penalization term in attention mechanism
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
categories:
  - Productivity Hacks
sidebar:
    nav: "blog"

---

# **들어가면서**

&#160;&#160;&#160; 서버 원격을 이용해 모델 학습을 시켜놔도 가끔 윈도우가 업데이트 되거나, 어떠한 이유로 로컬 컴퓨터가 나가면 학습이 중단 되는 것이 고민이었습니다. 그런데 사수분께서 zellij라는 좋은 툴을 알려주셔서 해당 툴의 사용 방법을 작성해보았습니다. 

<br/>

<br/>

# Zellij란?

zellij란 터미널 멀티 플렉서로, 하나의 터미널 화면에 여러 개의 터미널을 각각 독립적으로 관리할 수 있습니다. 또한, 원격 터미널 세션 안에서 여러 별도의 터미널 세션에 액세스 할 수 있으며, 하나 이상의 세션을 attach 혹은 detach하여 관리할 수 있습니다. 이를 활용하면 나의 로컬 컴퓨터가 꺼지더라도, 모델은 zellij에서 계속 학습되게 됩니다. 내가 할 일은? 그냥 꺼진 컴퓨터를 다시 켠 후 zellij를 attach 하면 됩니다!

<br/>

<br/>

## zellij 설치하기

설치에는 cargo를 이용하는 방법과, Binary로 다운로드 하는 방법 두 가지가 있습니다.

처음에는 binary 파일로 설치를 도전했는데, 왜인지 제대로 설치되지 않아 cargo를 이용했습니다.

<br/>

<br/>

## cargo 설치

우선 cargo를 이용해 zellij를 설치하려면, cargo를 먼저 설치해야합니다. 

리눅스 혹은 맥의 경우, 터미널에 아래와 같이 입력하여 설치할 수 있습니다.

```sh
curl https://sh.rustup.rs -sSf | sh
```

  

위 명령어로 설치하고 나면, 아래의 명령어가 표시될 것입니다.

```shell
Rust is installed now. Great!
```

설치를 성공적으로 마친 모습입니다! 만일 윈도우를 사용하고 계시다면, [이 링크](https://win.rustup.rs/)를 클릭해 exe 파일을 받아주셔야합니다.

<br/>

<br/>

## zellij 설치

zellij를 설치하는 방법도 아주 간단합니다. 아래 명령어를 입력하면 됩니다.

```shell
cargo install --locked zellij
```

혹시 오류가 발생한다면, 업데이트를 진행해보시길 바랍니다.

```shell
rustup update
```

<br/>

<br/>

## 세션 attach 하기

기본적으로 zellij에는 여러 세션들을 관리할 수 있습니다. 아래와 같은 명령어를 입력하면, attach 할 수 있는 세션들의 목록이 나타납니다.

```shell
zellij list-sessions
```

<img width="625" alt="스크린샷 2024-02-04 오후 2 30 37" src="https://github.com/Yjinaa/Yjinaa.github.io/assets/71372857/334b7507-9183-4d1e-bb72-583f4a57de4e">

여기서 해야 할 것은, 하나의 세션을 골라서 attach하는 것 뿐입니다! 저는 이미 create된 세션들이 몇 개 보이네요.

<br/>

profound-elephant 세션을 attach 해보겠습니다.

```shell
zellij attach profound-elephant
```

<img width="581" alt="스크린샷 2024-02-04 오후 2 33 15" src="https://github.com/Yjinaa/Yjinaa.github.io/assets/71372857/5cba59ca-758e-41e5-8388-7776ea8a2fa8">

attach가 완료된 모습입니다. 이제 터미널을 원하는대로 사용하시면 됩니다!

<br/>

<br/>

## tab 여러개 생성해서 하나의 터미널에서 관리하기

zellij에서는 하나의 터미널 세션에서 tab을 여러개 생성하여 여러 터미널에 접근할 수 있습니다. 하나의 화면에서 각기 다른 일을 하고 있는 터미널들에 접근이 가능하다는 뜻입니다. 새 탭을 생성하려면, `ctrl + t`를 누르고 `n`을 눌러주시면 됩니다.

그런데 여기서 저처럼 vscode를 사용하시는 분들의 경우, 이미 생성되어있는 키보드 shortcut 때문에 엉뚱한 기능이 실행될 수 있습니다. 그럴 때는 `ctrl + shift + p`에서 shortcut을 검색하여 기존에 사용되는 `ctrl + t`에 해당하는 단축키를 바꿔주시는 것이 좋습니다.

아래는 탭을 생성한 모습입니다.

<img width="575" alt="image" src="https://github.com/Yjinaa/Yjinaa.github.io/assets/71372857/1846b2cd-b560-44ed-a91d-cf9dd9df2e04">

세 개의 탭이 생성된 것을 보실 수 있습니다! 탭간 이동은 마우스 클릭으로도 가능합니다. 그리고 위의 pane이라는 글자가 보이시나요? 하나의 터미널 안에서 구역을 나눌 수도 있습니다.

<br/>

<br/>

## pane 추가하기

탭을 생성할 때와 비슷하게, `ctrl + p`를 누른 후 `n`을 누르면 됩니다. 아래는 Pane을 추가한 모습입니다.

<img width="578" alt="image" src="https://github.com/Yjinaa/Yjinaa.github.io/assets/71372857/1c1f5751-94d0-4925-b438-7987744af27a">

<br/>

<br/>

## detach 하기

모든 작업을 마치고, 이제 모델이 이대로 돌아가기만 하면 됩니다. 그럴 땐 `ctrl + q`를 사용해 detach를 할 수 있습니다.

<img width="475" alt="image" src="https://github.com/Yjinaa/Yjinaa.github.io/assets/71372857/e0198dfb-fa14-47d8-8c13-daf6841159d1">

이렇게 귀여운 작별인사를 남겨줍니다. 이후 다시 attach 할 때는, 위의 attach 명령어를 동일하게 사용하시면 됩니다!

<br/>

<br/>

## 필요하지 않은 세션 kill 하기

필요하지 않은 세션을 kill 하려면 아래와 같은 명령어를 사용합니다.

```shell
zellij kill-sessions [세션명]
```

<img width="591" alt="스크린샷 2024-02-04 오후 2 42 32" src="https://github.com/Yjinaa/Yjinaa.github.io/assets/71372857/1cc8623e-cb5c-4060-9d09-4437d740e27a">

kill 명령어를 준 후 list-sessions를 확인하니, 맨 아랫줄의 profound-elephant가 비활성화 된 것을 볼 수 있습니다.

<br/>

<br/>

## 마치면서

zellij를 알고 나서, 저에게는 새로운 모델 학습의 장이 열렸습니다... 여러분들도 함께 체험해보셨으면 좋겠네요! 😸



​       

​       

​      







