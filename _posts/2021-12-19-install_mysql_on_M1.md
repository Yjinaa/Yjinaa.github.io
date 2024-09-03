---
title: M1 mysql 설치기
date: 2021-12-19 21:54:00 +0300
description: arm64 기반 M1에서 mysql을 사용해봅시다.
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
categories:
  - Debugging Diaries
sidebar:
    nav: "blog"

---

## **들어가면서** 

&#160;&#160; 안녕하세요, 이전의 텐서플로우 설치기에 이어 이번엔 mysql 입니다! 항상 삽질을 하곤 하지만 점점 견고해져 가는 제 M1 환경을 보면 뿌듯해지네요..! 설치 과정과 함께 간단한 사용법을 함께 포스팅할 예정입니다. 그럼 바로 시작하겠습니다😀

​              

​                       

## 1. My SQL 설치하기

&#160;&#160;우선 가장 먼저 MySQL을 설치해보도록 하겠습니다. (저는 **Homebrew**를 통해 설치했습니다..! 혹시 홈브루가 설치되어있지 않으신 분들은 먼저 설치하시는 것을 추천드립니다. mysql 뿐만 아니라 다른 프로그램들도 손쉽게 설치할 수 있도록 도와준답니다:) ) 터미널을 켜주세요!

~~~shell
arch -arm 64 brew intall mysql
~~~

위 명령어를 입력해줍니다. 일반 다른 맥들과 다른 arm 칩을 사용하므로 앞에 arch -arm 64를 입력하여 arm 전용 프로그램을 설치하도록 명시해주어야 합니다..!

~~~sh
brew list
~~~

이제 위 명령어로 설치가 잘 되었는지 확인해보겠습니다.

<img width="568" alt="스크린샷 2021-10-01 오후 8 23 22" src="https://user-images.githubusercontent.com/71372857/146675950-07e004ae-98ad-4f2b-81ed-5ed6bc315622.png">

mysql이 보이네요! 잘 설치된 모습입니다.

​                    

​                       

​                              

## 2. 각종 환경 설정

&#160;&#160;이제 실행시켜 볼텐데요, 그전에 환경설정을 해주겠습니다.

~~~sh
mysql_secure_installation
~~~

위 명령어를 실행하면 아래의 설정사항이 나옵니다.

패스워드 : No (간단히 설정)

anonymous users 사용 : Yes (실행 시 mysql-ruser -p로 실행)

disallow root login(원격) : No (원격 접속 허용)

테스트 데이터셋 삭제 : No (남겨두기)

저는 위와 같이 설정해주었습니다.                     

​                        

​              

​                         

## 3. MySQL 실행

~~~sh
mysql -u root -p
status
~~~

위처럼 mysql을 실행해주고 status를 확인합니다.

<img width="570" alt="스크린샷 2021-10-01 오후 8 25 49" src="https://user-images.githubusercontent.com/71372857/146675948-c4165740-d146-426e-8ee5-035ead0721a2.png">

저는 요렇게 떴네요 ㅎㅎ                         

​                         

​                                                

​                        

## 4. 데이터베이스 생성해보기

1) 데이터베이스 목록 보기

~~~sql
SHOW DATABASES;
~~~

2. 데이터베이스 생성

~~~sql
CREATE DATABASE iris;
~~~

3. 데이터베이스 사용

~~~sql
USE iris;
~~~

4. 테이블 생성

~~~sql
CREATE TABLE DATASET (SL float, SW float, PL float, PW float, Species int)
~~~

5. csv 불러오기

~~~sql
LOAD DATA LOCAL INFILE("파일 경로")
INTO TABLE DATABASE
FIELDS TERMINATED BY ",";
~~~



**[참고사항]** 저는 여기서 오류가 발생했는데요, 아래와 같습니다.

<img width="582" alt="스크린샷 2021-12-19 오후 10 18 33" src="https://user-images.githubusercontent.com/71372857/146676275-0bd52cc0-760a-4fb3-b5e5-f697b8f293bc.png">

ERROR 1290 (HY000) : The MySQL server is running with the --secure-file-priv option ,,,

저기서 @@GLOBAL.secure_file_priv아래 칸에 무엇이 적혀있느냐에 따라 권한이 달라집니다.

공백 : 아무 경로나 읽고 쓸 수 있음

NULL : 아무 경로에서도 읽고 쓸 수 없음

특정 경로 : 특정 경로에서만 읽고 쓸 수 있음

저는 NULL로 되어있으므로 아무 경로에서도 읽고 쓸 수 없는 상태입니다. ㅠㅠ 그래서 불러와지지 않고 오류가 난 것입니다..

따라서 my.cnf 파일을 찾아 저 변수를 수정해주어야 합니다!

​                      

**📌 my.cnf 찾기**

~~~sh
# sql exit으로 종료 후
mysql --help | grep my.cnf
~~~

<img width="518" alt="스크린샷 2021-10-01 오후 8 30 31" src="https://user-images.githubusercontent.com/71372857/146675946-d17ce139-1607-4f74-a0e9-b688e29be914.png">

이렇게 알려준 경로로 들어가서, 

[mysqld]

secure_file_priv=""

​                         

이렇게 추가해주고 저장하고 나온 뒤 mysql 서버를 재실행해줍니다. (그냥 실행 아니고, 서버를 재실행해주셔야 적용됩니다!)

~~~sh
mysql.server restart
~~~

이후 mysql을 다시 실행해줍니다.

~~~sh
mysql -uroot -p
~~~

<img width="381" alt="스크린샷 2021-10-01 오후 8 31 55" src="https://user-images.githubusercontent.com/71372857/146675943-eb4a4920-b39b-498c-9ae4-eaa699b608af.png">

그러면 아까와는 다르게 요렇게 공백으로 잘 설정된 모습을 보실 수 있습니다!

다시 csv를 불러와보겠습니다.

​                              

<img width="556" alt="스크린샷 2021-12-19 오후 10 26 39" src="https://user-images.githubusercontent.com/71372857/146676528-76a9c353-54ce-4b1d-9c20-191c9593ce74.png">                        

이렇게 잘 불러와진 모습을 확인할 수 있었습니다! (참고로 헤더가 존재한다면 헤더는 지우고 업로드하셔야 잘 업로드됩니다)

​                       

​                  

​                                                    

## 마치며

&#160; 이번엔 순탄하게 마치나 했더니 역시 마지막에 오류가 나서 조금 당황했지만 그래도 수월했던 mysql 설치 및 간단 사용기입니다! 생각보다 매우 간단하고 쉬우니까 mysql 관심있으셨던 분들은 도전해보시면 좋을 것 같습니다.😊

여기까지 읽어주신 모든 분들, 글 쓰는데 도움을 주신 아래 글쓴분들께 감사의 말씀 드립니다!

​                   

​                         

​                                

## Reference

* [개발자 Sunho Lee](https://sssunho.tistory.com/56)  - [MySQL] Load Data 시도 중 secure_file_priv 문제 직면
* [Data Science Lab](https://sungwookkang.com/1328) - MySQL/MariaDB 파일 읽기/쓰기시 발생할 수 있는 secure-file-priv 오류
* [linked2ev](https://linked2ev.github.io/database/2021/06/19/MariaDB-6.-MySQL-%EC%84%A4%EC%A0%95-%ED%8C%8C%EC%9D%BC-my.cnf-%EC%9C%84%EC%B9%98/) - [MariaDB] 6. MySQL 설정 파일 my.cnf 위치

​               

​         

​         

​         

 

