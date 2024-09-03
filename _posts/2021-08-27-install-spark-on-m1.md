---
title: 맥북 M1 스파크 설치기
date: 2021-08-27 23:23:00 +0300
description: 우당탕탕 스파크 설치기 & 하둡 삽질기 2탄
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [] # add tag
categories:
  - Debugging Diaries
sidebar:
    nav: "blog"

---

## Spark 설치 & Hadoop 삽질기 2탄

  이전 글에서 하둡과 스파크에 관한 특강을 들을 수 있는 좋은 기회가 생겨 참여하게 되었다고 언급했다. 첫 글은 하둡 설치기, 그리고 다음 글인 이번 글은 스파크 설치기이다. 하둡에 비해서는 아주 수월하게 설치되었다! 그래서 저번 포스팅에서 다 담지 못했던 하둡 삽질기(?)를 조금 추가해 글을 써보려고 한다.

## Hadoop 삽질기 2탄

  저번 포스팅에서 하둡을 모두 설치하고 나서, mapper를 사용하고자 이런 저런 일들을 벌렸다. 역시나 mapper 사용도 한 번에 쉽사리 되진 않았고(😂) 중간에 씅이 난 나는

~~~sh
hdfs namenode -format
~~~

이렇게 포맷을 해버리고 말았다... (오류만 났다 하면 원인 파악도 전에 먼저 껐다 켜보는 본능) 그러고 나니 ㅠㅠ 제대로 실행되던 하둡이 실행이 안됐다. 분명 총 5개의 항목이 돌아가고 있어야 하는데,

<img width="567" alt="스크린샷_2021-07-27_오전_11 44 52" src="https://user-images.githubusercontent.com/71372857/131144003-e7dc6e83-481e-4701-9598-0d991544570b.png">

위처럼 데이터 노드가 돌아가지 않고 있는 대참사

구글링했더니 처음에는 env.sh의 HADOOP_OPTS 환경변수를 

~~~sh
export HADOOP_OPTS="$HADOOP_OPTS -Djava.library.path=$HADOOP_PREFIX/lib"
~~~

이렇게 변경하라고 해서 바꿨는데 똑같이 실행되지 않았다. 그래서 원래대로 다시 바꾸고 한번 더 하둡 포맷(??? 포맷때문에 이 참사가 일어났는데..)을 해줬다. 그러고 재실행했는데 역시나 될리가 없었다.

다시 구글링해보니 env.sh 파일에 아래 문구를 추가하면 된다고 해서 해봤는데 역시 안됐다.

~~~sh
export HADOOP_COMMON_LIB_NATIVE_DIR=${HADOOP_PREFIX}/lib/native
export HADOOP_OPTS="${HADOOP_OPTS} -Djava.library.path=$HADOOP_PREFIX/lib"

출처: https://abc2080.tistory.com/entry/경고-Unable-to-load-nativehadoop-library-for-your-platform [Another Windows]
~~~

되다가 갑자기 안되니까 멘붕이 왔다... 근데 다시 자세히 살펴보니 종료할 때는 stopping datanode라고 뜨고, 구글링 했을 때 저 에러는 경고문일 뿐 데이터노드가 실행되긴 하므로 신경 안써도 된다는 글을 본 기억이 났다. 추론 결과 datanode 자체는 실행되는데 뭔가 매핑이 안되는 것 같아 하둡 데이터노드 오류로 구글링해보니 드디어 원인을 찾았다.

바로 하둡 구동중에 (종료하지 않고) namenode format을 하면 주로 발생하는 오류라는 것 ㅠㅠ

namenode namespaceID와 datanode namespaceID가 일치하지 않을 경우 발생한다고 한다. 초기 설치가 아닌 하둡 사용 중 format을 해야한다면 hdfs의 data폴더를 지우고 다시 생성한 후 포맷해야 정상작동 한다고... 또 하나 배웠다. ㅠㅠ

그래서 데이터를 지우기 위해 hdfs폴더를 들어가보니,,

<img width="282" alt="스크린샷_2021-07-27_오후_12 06 08" src="https://user-images.githubusercontent.com/71372857/131144002-3970e7b9-90af-47df-9e17-cfc59aea6ec8.png">

이렇게 두 개 밖에 없었다. 뭘 지워야 하는거지 고민하다가 dfs안에 data폴더가 있길래 그냥 지워봤다. 

근데 삭제 후 다시 실행하려 했더니 먼저 stop 하라는 경고 문구가 뜸....... 그렇다... 또 하둡 종료 전 데이터를 삭제해버림.......🤯 아니 종료 안하고 포맷해서 이 사달이 났는데 또 이런다고...🤬 좌절을 느끼면서 제발 별문제 없길 바란다는 주문을 외며 종료하고 재실행했다.

<img width="569" alt="스크린샷_2021-07-27_오후_12 12 01" src="https://user-images.githubusercontent.com/71372857/131143992-751baf1c-4246-4d4a-8b20-e95baf6cf560.png">

신도 불쌍하셨는지 다행히 제대로 잘 돌아갔다. 이 날의 교훈.. 하둡 구동중 포맷은 절대로 하지 맙시다. 그리고 종료 명령을 생활화 합시다😂



## Spark 설치 시작

* mac OS Big Sur 11.1에서 진행했습니다.
* Homebrew가 설치되어 있어야 합니다.

이제 스파크 설치로 돌아왔다. 홈브루로 설치할지, 직접 파일을 다운받을 지 고민하다가 하둡도 홈브루로 설치했기 때문에 혹시 모를 충돌을 대비하여 그냥 홈브루로 설치하기로 했다.

혹시 파일을 직접 다운받으실 분들은 아래의 주소에서 하둡 버전과 맞는 스파크 버전을 받아주면 된다.

http://spark.apache.org/downloads.html

<img width="706" alt="_2021-07-21__12 47 13" src="https://user-images.githubusercontent.com/71372857/131146343-924e7ae6-591d-48cd-b6b2-38f7a77b91fd.png">

이런식으로 각자 맞는 버전을 찾아 설치해주시면 됩니다!

## 설치 과정

#### **_1. 스파크 설치_**

   우선 터미널을 열고 스파크 인스톨 명령을 내렸다. arm 기반 맥은 하둡과 동일하게 설치 명령이 조금 다르다.

```sh
arch -arm64 brew install apache-spark
```

이 명령어로 설치를 시도했다.

설치가 완료되면 아래 명령어로 info를 확인할 수 있다.

~~~sh
arch -arm64 brew install apache-spark
~~~



<img width="567" alt="_2021-07-21__12 59 26" src="https://user-images.githubusercontent.com/71372857/131146340-cfbc637c-2991-4d9e-b70a-1eeae93ca803.png">

/opt/homebrew/Cellar/apache-core/blob/HEAD/Formula/apache-spark.rb

위 경로에 서치가 되었음을 확인할 수 있다.



#### **_2. 환경변수 설정_**

이제 환경변수를 설정해야한다.

```sh
vi ~/.zshrc
```

언제나 그랬듯이 위 명령어로 zshrc 파일에 접속해서, 환경변수를 추가해준다.

~~~sh
export SPARK_HOME=/opt/homebrew/Cellar/apache-spark/3.1.2/libexec
export PATH=${PATH}:$SPARK_HOME/bin

# 파이썬3 버전을 사용한다면 아래도 추가
export PYSPARK_PYTHON=python3
# 주피터 랩을 실행시키기 위해 아래 환경변수 추가
export PYSPARK_DRIVER=jupyter
export PYSPARK_DRIVER_PYTHON_OPS="lab"
~~~

<img width="543" alt="_2021-07-21__1 10 16" src="https://user-images.githubusercontent.com/71372857/131146338-dde5e5bf-62e5-42fe-b31d-bb7230f6fd79.png">

이제 주피터 랩을 통해서 pyspark를 실행할 수 있다. 노트북을 주로 사용한다면 lab을 notebook으로 바꾸어주면 된다.

~~~sh
source ~/.zshrc
~~~

수정 후에는 위 명령어를 반드시 실행시켜주어야 한다.

~~~sh
pyspark --version
~~~

위 명령어를 입력해주면 아래와 같이 파이스파크 버전 확인이 가능하다.

<img width="568" alt="_2021-07-21__1 18 07" src="https://user-images.githubusercontent.com/71372857/131146334-1744eec2-ba6e-41be-820a-6e67d8683be2.png">

그리고

~~~sh
pyspark
~~~

로 실행시켜주면,

<img width="568" alt="_2021-07-21__1 20 17" src="https://user-images.githubusercontent.com/71372857/131146333-d7b71e04-03be-46f0-bace-38fe4413a7ce.png">

이와 같이 실행된다. 다만..

<img width="566" alt="_2021-07-21__1 20 53" src="https://user-images.githubusercontent.com/71372857/131146330-7c4b8e86-2238-4cbc-bcba-83ad5d528d36.png">

엄청난 오류들이 나긴 함.............

하지만 못본 척 하고 막상 랩 열어서 import 해보면 실행은 잘 된다.^^

<img width="529" alt="_2021-07-21__1 33 51" src="https://user-images.githubusercontent.com/71372857/131146325-4ac6f037-85aa-4545-9f95-128441a0c72b.png">

이렇게 ㅠㅠ 근데 spark-shell은 따로 열리지 않음.

<img width="894" alt="_2021-07-21__1 38 04" src="https://user-images.githubusercontent.com/71372857/131146322-c11fdfb4-26eb-4bf0-8ec0-caa5362b718b.png">

너무 힘들어서 못본 척 그냥 되겠지 하고 그만하려다가 불안해서 교수님이 주신 예제를 실행시켜보니...

~~~python
import pyspark
from pyspark import SparkContext
sc = SparkContext.getOrCreate()
licLines = sc.textFile("/example/LICENSE")
linecnt = licLines.count()
linecnt
~~~

<img width="924" alt="스크린샷_2021-07-25_오후_5 17 07" src="https://user-images.githubusercontent.com/71372857/131146317-4ea177b0-d96c-4acf-8118-9438ab5eec25.png">

두둥.. sparkcontext 개체 자체가 불러와지지 않았다.ㅠㅠ 그럼 그렇지..!!!!!!!!!!! 어째 쉽게 된다 했더니.

오류 메세지를 잘 살펴보니 호스트 주소에 뭔가 잘못된 것이 있는 것 같아 구글링해봤다.

알고보니 호스트 주소를 수정해주어야 한다고 하더라.



#### **_3. 호스트 주소 수정_**

우선 아래 경로의 [env.sh](http://env.sh) 파일을 찾아주어야 한다. 파인더에서 shift+command+g 를 눌러 경로 이동을 해주고 찾아준다.

~~~sh
/opt/homebrew/Cellar/apache-spark/3.1.2/libexec/conf/spark-env.sh.templete
~~~

파일 확장자가 templete이지만 다음으로 열기를 누르면 텍스트 편집기로 열 수 있다.

<img width="504" alt="스크린샷_2021-07-25_오후_5 20 30" src="https://user-images.githubusercontent.com/71372857/131146314-665f61d1-03c5-4523-9124-03cfceaca89e.png">

맨 아랫줄을 추가로 넣어주고, 저장했다.

그리고 이제 맥 호스트 파일을 수정해야한다.

우선 터미널에 hostname을 입력해서 호스트네임을 받아온다.

<img width="342" alt="스크린샷_2021-07-25_오후_5 23 34" src="https://user-images.githubusercontent.com/71372857/131146306-8b0fd541-0ae3-4a12-a9d6-2b62a8e4008f.png">

아래 출력된 호스트 네임을 복사해서 /etc/hosts 파일을 수정해주면 된다.

https://magiclee.tistory.com/34

나는 위 블로그를 참조했다. (감사합니다...)

```sh
sudo nano /etc/hosts
# 비밀번호 입력
# 호스트 내용 수정 후
control + x 동시 입력
Y 입력
enter로 종료
```

<img width="562" alt="스크린샷_2021-07-25_오후_5 22 44" src="https://user-images.githubusercontent.com/71372857/131146304-8df2046f-ad54-421b-9094-48c1f9555f55.png">

이렇게 [localhost](http://localhost) 부분을 주석처리하고 아랫줄에 hostname을 넣어주었다.

그러고 다시 pyspark를 실행했다.

그랬더니

~~~sh
ValueError: Cannot run multiple SparkContexts at once
~~~

알고보니 한 번 실행시키면 종료를 해주어야하는 듯 싶다..^^ㅎ

종료 지옥...

~~~sh
sc.stop()
~~~

위 코드 실행 후 다시 해봤더니 잘 되었다. 그리고 교수님이 주신 예제에서의 경로는 '/example/LICENSE' 였는데, 나는 맥이라서 그런가 경로가 달라 알맞게 수정한 뒤 실행했다.

<img width="601" alt="스크린샷_2021-07-25_오후_5 26 03" src="https://user-images.githubusercontent.com/71372857/131146294-302e492f-22ae-4019-ac47-2ca375809eab.png">

위 코드의 경로를 보면 수정된 것을 알 수 있고, count가 잘 먹혀 576이 출력된 것을 볼 수 있다.



## 마치며

  하둡보다는 훨씬 괜찮았으나 역시 어려운 스파크 설치였다. 하둡에서 많이 데인 탓에.. 스파크 설치는 좀 더 수월할 수 있었던 것 같다. 또, mapper를 따로 사용해야하는 하둡과 달리 주피터 상에서 바로 사용 할 수 있다는 메리트가 굉장히 큰 것 같다. 개인적으로 설치 과정 내내 버전을 띄우면 나오는 스파크 로고가 멋지다고 생각했다.😎

<img width="568" alt="_2021-07-21__1 18 07" src="https://user-images.githubusercontent.com/71372857/131146334-1744eec2-ba6e-41be-820a-6e67d8683be2.png">

