---
layout: post
title: 맥북 M1 하둡 설치기
date: 2021-08-15 18:55:00 +0300
description: 우당탕탕 하둡 설치기
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [install,hadoop] # add tag

---

## Hadoop 설치

  최근 하둡과 스파크에 관한 특강을 들을 수 있는 좋은 기회가 생겨 참여하게 되었다. 그런데, 설치 과정부터 녹록치 않았다. 왜냐하면 수업은 윈도우 기반으로 진행되었기 때문에 맥 환경에 맞게 따로 설치했어야 했기도 하고, M1의 특성상 프로그램 설치 시 이전 맥 환경에서 했던 방법에 추가 조치가 필요하거나, 아예 방법 자체가 달랐던 경험이 많았기 때문이다. 비록 웹상에 하둡 설치에 관련된 포스팅은 많지만, M1에서 설치했다는 글은 보지 못한 것 같아 글을 작성해본다. 설치를 완료한 지금 결론부터 말하자면, 이전 인텔 맥들에서 설치한 방법과 크게 다르지는 않다. 그치만 중간 과정에서 상당히 많은 오류를 맞닥뜨렸기 때문에.. 혹시 미래에서 동일한 오류를 마주치게 되는 분들이 나와 같은 삽질을 하지 않길 바라는 마음으로 작성하려고 한다. 참고로 본인은 터미널 명령도 아직 능숙히 쓰지 못하는 초보이기 때문에 더 돌아갔을 수도 있을것이다ㅠㅠ

## 설치 시작

* mac OS Big Sur 11.1에서 진행했습니다.
* Homebrew가 설치되어 있어야 합니다.

우선 첫 번째 관문은 윈도우가 아닌 맥에서 설치해야 한다는 것... 아무것도 몰랐기 때문에 윈도우에서처럼 vmware를 사용해야하는 줄 알았는데, 하둡은 리눅스 기반 운영체제만 지원하기 대문에 사용해준 것이고 맥은 그럴 필요가 없다는 것도 한참의 서치 끝에야 깨달을 수 있었다. (😂) 그래서 터미널을 열고 바로 설치를 시작했다.

## 설치 과정

#### **_1. 하둡 설치_**

   우선 터미널을 열고 하둡 인스톨 명령을 내렸다.

```sh
brew install hadoop
```

그러나..

<img width="546" alt="_2021-07-20__7 32 27" src="https://user-images.githubusercontent.com/71372857/129475182-6e3b455e-ab03-48f7-a2f2-55638968ed59.png">

_cannaot install under Rosetta 2 in ARN default prefix_

역시 쉽지 않을 줄은 알았지만... 우선 현 운영체제는 arm 기반이므로 위의 명령어를 사용하여 다시 시도했다. 

```sh
arch -arm64 brew install hadoop
```

<img width="565" alt="_2021-07-20__9 48 57" src="https://user-images.githubusercontent.com/71372857/129475181-1186f73d-5935-4f62-9fd8-8cbb75e61103.png">



또 오류가 나긴 했는데, 91.9퍼센트에서 뭔가 충돌이 난 것 같아서 그냥 다시 똑같이 입력하고 재설치했다.



<img width="574" alt="_2021-07-20__11 07 51" src="https://user-images.githubusercontent.com/71372857/129475179-6a80d7d3-e905-4917-9439-7e468e88d1e8.png">

드디어 성공! 설치 경로를 제대로 파악하기 위해

```sh
brew info hadoop
```

<img width="539" alt="_2021-07-20__11 08 31" src="https://user-images.githubusercontent.com/71372857/129475178-8e1efc33-8f67-49a8-bf88-a4091872c2bd.png">

잘 된 것 같다. 7번째 줄에 보면 설치 경로가 opt/로 시작하는데, 이 경로를 복사해두는 것이 좋다. 이전의 인텔 맥북과 m1 맥북에서 주의해야 할 점은 이것 뿐이다. 구글링 해본 결과, 인텔 맥북에서는 usrs 이하의 경로에 설치되는 것 같다. 다음으로 몇 가지 파일을 수정해주어야 한다.



#### **_2. 파일 수정 및 환경변수 설정_**

**파일 수정**

https://tariat.tistory.com/492 나는 이 블로그를 참조했다. 그리고 yarn-site.xml 파일을 추가로 수정해주어야 한다.

~~~sh
<property>
    <name>mapreduce.framework.name</name>
    <value>yarn</value>
  </property>
  <property>
    <name>mapreduce.application.classpath</name>   
	  <value>$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/*:$HADOOP_MAPRED_HOME/share/hadoop/mapreduce/lib/*</value>
  </property>
~~~

위 내용을 configuration 사이에 넣어주었다.

**_* 주의 사항_**

위에서 m1의 설치 경로는 opt 이하에 설치된다고 언급했다. 다른 파일들은 문제가 없는데, core-site.xml 파일을 보면 usrs/ 이하로 시작하는 경로가 보인다. 이 경로를 지금 설치된 경로로 수정해주어야 한다. 나는 처음에 이걸 몰랐어서 삽질을 엄청 했다... 수정하지 않으면 엄청난 오류가 발생하며 하둡이 제대로 실행되지 않는다. ㅠㅠ

<img width="475" alt="_2021-07-21__12 08 55" src="https://user-images.githubusercontent.com/71372857/129475162-4a60d3db-c0f3-415d-96da-d137ac44017b.png">

이렇게!

마지막으로 hadoop-env.sh 파일에 JAVA_HOME의 경로를 추가해준다.

<img width="523" alt="_2021-07-20__11 24 13" src="https://user-images.githubusercontent.com/71372857/129475664-94797d82-9877-4447-a81c-9cb2be29af3b.png">



**환경변수 설정**

~~~sh
vi ~/.zshrc
~~~

<img width="568" alt="_2021-07-20__11 30 50" src="https://user-images.githubusercontent.com/71372857/129475174-da79c593-3beb-48f1-9faa-eb5f7e25db11.png">

**하둡 초기화**

모두 완료되었다면, 최초 실행 전 하둡을 초기화해주어야 한다.

~~~sh
hdfs namenode -format
~~~



#### **_3. 원격 설정_**

이제 준비는 모두 끝났다. 실행시켜보자!

~~~sh
start-all.sh
# 이 글에서는 위 명령어를 사용했는데,
./start-dfs.sh
# 또는
./start-yarn.sh
# 위 명령어들도 사용 가능하다고 한다.
~~~



<img width="569" alt="_2021-07-20__11 35 19" src="https://user-images.githubusercontent.com/71372857/129475172-98f5b0fa-81a1-483f-aa51-343389704b3f.png">

그래.. 쉽게 되면 내가 아니지...

start-dfs.sh 명령어를 사용해보았다.

<img width="568" alt="_2021-07-20__11 36 59" src="https://user-images.githubusercontent.com/71372857/129475171-8f23f382-cb46-4093-9c8c-dfa89eb85168.png">

위 문구를 잘 보면 ssh: connect to host localhost port 22: Connection refused 라는 오류 문구가 보인다. 원격 설정을 해주지 않아서 발생하는 오류라고 한다. 이를 해결하기 위해서는 맥북 자체 설정 수정이 필요하다.



**맥북 설정**

우선 맥북 자체 설정은 간단하다. 맨 왼쪽 상단의 애플 아이콘 > 시스템 환경설정 > 공유로 들어가면 '원격 로그인'이라는 체크 박스가 있을 것이다. 체크하여 허용해주자.

그리고 다시 실행시켜보면...

<img width="571" alt="_2021-07-20__11 40 35" src="https://user-images.githubusercontent.com/71372857/129475170-862c17b6-9e0f-4b53-884b-96550d8f5342.png">

... 자 보면 permission denied, warning 등이 뜬다... 원격 설정과 더불어 ssh를 설정해주어야 한다고 한다. 

**ssh 설정**

**다시 포맷을 진행하고** (포맷 절대 함부로 하면 안된다... 이유는 다음 포스팅에서...)아래 명령어로 ssh 키를 설정해주었다.

~~~sh
ssh -keygen -t rsa
~~~

<img width="569" alt="_2021-07-20__11 50 29" src="https://user-images.githubusercontent.com/71372857/129475169-cd5ca585-aa5f-4af7-9745-1932635cca7f.png">

위 명령어를 치고 빈칸에 엔터를 친 후 아래 명령어를 입력해준다.

~~~sh
cat ~/.ssh/id_rsa.pub >> ~/.ssh/authorized_keys
~~~

다시 하둡을 실행해본다.

<img width="571" alt="_2021-07-20__11 54 09" src="https://user-images.githubusercontent.com/71372857/129475166-53d439eb-6340-44d8-9b78-9654d2ae0efc.png">
<img width="566" alt="_2021-07-20__11 50 46" src="https://user-images.githubusercontent.com/71372857/129475167-6b72bebb-a54d-4957-a7ab-e5c8a030f87b.png">

일단 stop it first라니까 모든걸 종료시켜줬다..

~~~sh
stop-all.sh
~~~

jps 명령어를 이용해 실행되고 있는 노드들을 확인해보았다.

<img width="302" alt="_2021-07-20__11 56 19" src="https://user-images.githubusercontent.com/71372857/129475165-0e1600fa-a576-41a2-96d1-cb24e339f8ea.png">

모두 종료된 것을 확인하고 재실행.

~~~sh.
/opt/homebrew/Cellar/hadoop/3.3.1/libexec/sbin/start-all.sh
~~~

<img width="566" alt="_2021-07-21__12 01 07" src="https://user-images.githubusercontent.com/71372857/129475163-7a844fb7-6393-4594-a3dc-f84f2edac7ea.png">

자 보통의 경우라면 여기서 실행이 정상적으로 되어야한다.!! 하지만 앞서 언급했듯이 나는 core-site.xml 파일의 경로를 수정해줘야 되는 것을 몰랐고, 만일 수정하지 않는다면 namenode와 datanode가 돌아가지 않고 위의 cannot set priority 오류가 발생하는 것 같다.



나는 이 상태에서 core-site 파일만 수정해준 뒤 다시 실행시켜보니,

<img width="567" alt="_2021-07-21__12 13 30" src="https://user-images.githubusercontent.com/71372857/129475161-5b637ef3-f9f7-4502-8e20-e80d26dd3dd2.png">

이렇게 6개 노드 모두 잘 실행이 되었다.



### 마치며

  물론 쉽지 않을거라고 생각은 했지만.. 생각보다 더 힘들었던 하둡 설치였다.. 거의 하루를 꼬박 설치로 보냈다. 이 글에 있는 삽질 외에도 엄청난 삽질들을 했는데, 글에 첨부할 필요는 없을 것 같아서 생략했다. 예를 들면 파일 수정 해줄 때 오탈자가 있다거나... 파일을 잘못 수정했다거나...ㅜㅜ 그래도 이번 하둡을 설치하면서 좀 더 터미널과 친숙해진 것 같다. 예전에는 터미널로 어떤 파일을 설치해야하거나 작업을 해야한다고 하면 부담부터 앞서고 마음이 무거웠다(한 번도 깔끔하게 완료해본 적이 없기 때문.. 몇시간이고 무한 삽질). 인터넷을 보면 다른 분들은 터미널 잘 사용하시는데.. 세상에 맥북 쓰면서 터미널 제대로 활용 못하는 사람은 나밖에 없을 것 같다고 좌절하기도 했었다. 그치만 지금은 좀 더 용기를 가지고 도전할 수 있을 것 같다. 다음 글은, 하둡 삽질 제 2탄과(ㅠㅠ) 스파크 설치기로 돌아옵니다!