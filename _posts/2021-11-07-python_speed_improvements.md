---
title: 파이썬 속도 개선
date: 2021-11-07 23:10:00 +0300
description: 파이썬의 여러가지 속도 개선 방법
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [python]
categories:
   - Study Notes
sidebar:
   nav: "blog"
---

## **들어가면서** 

&#160;&#160;이번 글은 강의 시간에 배운 파이썬의 속도 개선에 대해 정리를 하고 넘어가려고 합니다. 특히 이미지 처리를 자주 하시는 분이라면 도움이 될 것으로 생각되네요! 그러면 이제 시작해보겠습니다. 🙂

​       

​              

## **파이썬의 속도**

&#160;&#160;다들 아시겠지만, 파이썬은 인터프리터 언어이기 때문에 for loop 연산에 몹시 취약합니다. 다른 부분에서도 그렇지만 특히 이미지 처리에 관해서는 더욱 그렇습니다. 예를 들어 이미지에 메디안 블러를 적용하고 싶다면, 필터를 사용해 이미지의 모든 픽셀에 대해 연산해주어야 하므로 RGB 3차원값에 대한 루프 하나, 이미지의 너비, 높이를 도는 루프 두개, 마지막으로 필터를 뽑아내는 루프까지 한 픽셀값 당 루프 다섯개를 돌게 됩니다. 어마어마한 시간이 걸리게 되는 것이죠... 이를 해결하기 위해 여러 방법들이 있는데, 이번에 소개해 볼 것은 Cython, f2py, jpype 그리고 numba 입니다.

​       

​              



## Cython

&#160;&#160;Cython은 파이썬 코드를 C로 바꾼 뒤 C로 실행합니다. C로 작성된 코드를 컴파일하여 pyd 파일을 생성하고, dll 방식으로 파이썬에서 import 할 수 있게 해 사용합니다. dll이란 dynamic link library의 약자로, 프로그램이 실행될 때 동적으로 호출될 수 있게 하는 라이브러리입니다. Cython은 C에 대해 잘 아시는 분이 쓰기 수월합니다. 

​       

​              

## f2py

&#160;&#160;&#160;&#160;f2py는 fortran을 사용합니다. fortran은 과학 기술의 연산을 위해 발명된 언어로, 컴파일러 언어이므로 빠른 속도를 보입니다. 또한 코드도 비교적 쉬운 편입니다.  코드에서 필요한 부분을 fortran subroutine으로 바꾼 뒤 cython과 마찬가지로 dll 방식으로 파이썬에서 import 할 수 있게 하여 사용합니다. f2py를 사용하는 절차는 아래와 같습니다.

1. _포트란 컴파일러 설치_
2. _포트란 컴파일 확인_
3. _포트란 컴파일러 Path 추가 확인_
4. _아나콘다 프롬프트에서 f2py 작동 확인_
5. _visual studio 확인_
6. _포트란 함수 작성, 확장자를 f95로 저장_
7. _컴파일을 통해 dll 생성_
8. _파이썬에서 생성한 포트란 라이브러리 호출_

​       

​              

## jpype

&#160; &#160; jpype는 자바 클래스를 파이썬에서 사용할 수 있도록 해주는 라이브러리입니다. 자바를 사용하면 파이썬보다는 빠르지만, C나 fortran과 같은 컴파일러 언어보다는 느립니다. 그러나 비교적 연결이 쉽다는 장점이 있습니다. 실행 과정은 아래와 같습니다.

​         

1. _java 프로그램 컴파일러 class 파일 생성_

2. _jar 파일 생성_

3. _jar 파일이 생성된 폴더에서 JVM 실행_

3. _py 파일 생성_

4. _파이썬에서 import_

5. _java.jLib에서 static 함수 호출하여 사용_

​       

​              

## Numba

&#160; &#160; 드디어 오늘의 주인공입니다! 넘바는 위 세 방법보다 훨씬 쉽게 설치하고 사용할 수 있습니다. 과정은 아래와 같습니다.

1. _pip install numba 를 이용하여 설치_

2. _from numba import jit을 이용하여 import_

3. _넘바를 사용할 함수 앞에 @jit(nopython=True)로 파이썬이 아님을 명시_

   _혹은 상황에 따라 @nb.jit을 사용_

4. _한 번 함수를 실행하여 컴파일_

5. _재실행하여 컴파일된 코드 실행_

4, 5번 과정을 보시면 알 수 있듯이, 넘바를 사용할 때는 해당 함수를 2번 실행해주어야 합니다. 첫 실행 때 넘바는 함수를 C코드로 바꾸어주고, 재실행 할 때 컴파일된 코드를 실행하기 때문이죠! 다른 과정에 비해 간단한 것이 보이시죠?

​       

​              

## 마치며

&#160;&#160;오늘은 파이썬 속도 개선에 대해 네 가지 방법을 알아보았습니다! 우선 Cython은 파이썬보다 약 100배 정도 빠르며, f2py는 90배 정도 빠릅니다. jpype를 사용하면 약 3.8배 빠르지만, 넘바를 사용하면 약 280배의 속도 개선 효과가 있습니다. Cython, f2py, numba 모두 월등한 속도 개선을 보이지만 적용 용이성에서 보면 numba가 가장 좋습니다. 혹시 속도 개선 고민으로 앓고 계시는 분이 있다면 numba 사용을 강력히 추천드립니다!  🙂

​       

​              

## Reference

* [김승환의 이것저것 연구실](https://m.blog.naver.com/swkim4610)        

​               

​         

​         

​         

 
