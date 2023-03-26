---
layout: post
title: 깃 블로그 파비콘 변경하기, 변경 적용 안됨 오류
date: 2023-03-26 17:02:07 +0900
description: 파비콘을 바꿔보았습니다.
img:  # Add image post (optional)
fig-caption: # Add figcaption (optional)
tags: [gueltto,writing,post,retrospective] # add tag
use_math: true
---

## **파비콘 변경**하기

&#160;  최근 깃 블로그에 변화를 주고 싶어 테마를 바꾸었습니다. 오랜만에 바꾸는 테마라 그런지 버전 충돌이 여럿 생겨 몇시간이 걸렸답니다.. 그런데.. 이내 다시 원래 테마로 변경하게 되었습니다.. 

테마 디자인만 봤을때는 괜찮았는데, 제 블로그에 적용시키기에는 몇몇 문제점이 있었습니다. 그래서 다시 원래 테마로 돌아왔다는 이야기..

테마 변경 과정에서도 엄청난 오류들이 있었습니다. 다른 분들은 간단히 하시는 것 같은데 왜 저는 항상...

아무튼 이 문제들은 다음 포스팅에서 후술하기로 하고, 오늘은 파비콘을 변경 방법과 변경하며 겪었던 오류에 대해 포스팅해봅니다.                                

​                                                 

​                                    

### 1. 파비콘으로 적용할 이미지 선택 & 파비콘 다운로드

우선 파비콘으로 적용하고자 하는 이미지를 선택합니다. 

https://realfavicongenerator.net 위 링크로 접속합니다.

<img width="784" alt="스크린샷 2023-03-26 오후 5 10 29" src="https://user-images.githubusercontent.com/71372857/227763497-48ebac91-f580-4899-8eb1-efec79b6c19c.png">

'Select your Favicon image' 를 클릭하고 원하는 이미지를 업로드합니다.

​                                                   

<img width="741" alt="스크린샷 2023-03-26 오후 5 11 49" src="https://user-images.githubusercontent.com/71372857/227763524-9bde0926-9845-4f69-922e-caee97105b17.png">

이렇게 완료되었다는 창이 뜨면 가장 아래로 스크롤합니다. 

​                                           

![스크린샷 2023-03-26 오후 5.12.23](/Users/yeajin/Library/Application Support/typora-user-images/스크린샷 2023-03-26 오후 5.12.23.png)

'Generate your Favicons and HTML code' 를 클릭합니다.

​                                                             

![스크린샷 2023-03-26 오후 5.13.27](/Users/yeajin/Library/Application Support/typora-user-images/스크린샷 2023-03-26 오후 5.13.27.png)                                         

'Favicon package' 를 눌러 파일을 다운로드 합니다. 하단 html 코드가 필요하므로 이 창은 아직 닫으시면 안됩니다. 

​                               

​                                         

### 2. 파비콘 적용하기

이제 파비콘을 적용해볼건데요, 이미 테마에 적용되어있냐 아니냐에 따라 변경 방법이 달라집니다.

​                                            

#### a) 파비콘이 적용되어 있지 않은 테마의 경우

이 경우에는 오히려 단순합니다. 다운로드 받은 파일을 깃 폴더의 assets내에 저장하면 됩니다.

<img width="340" alt="스크린샷 2023-03-26 오후 5 18 55" src="https://user-images.githubusercontent.com/71372857/227763811-6f6a698f-3edc-47ab-a04c-0ce6267ed807.png">

제 테마같은 경우 저렇게 assets 내 'img' 폴더가 있습니다. img 폴더 안에 'favicon' 폴더를 만들어 붙여넣어주세요.

​                                      

<img width="306" alt="스크린샷 2023-03-26 오후 5 19 58" src="https://user-images.githubusercontent.com/71372857/227763858-67dc52ad-7b20-427d-8143-71869d724301.png">

저 같은 경우 저렇게 assets > img > favicon에 저장해두었습니다. 경로는 기억해두셔야 합니다. 

​                                             

<img width="445" alt="스크린샷 2023-03-26 오후 5 20 16" src="https://user-images.githubusercontent.com/71372857/227763875-bdcf6530-4d3e-4e37-acd0-abc923aedb6a.png">

이렇게 파비콘 파일이 생겼습니다. 저는 필요없는 건 다 지워서 네개만 보이는데, 지우지 않아도 상관 없습니다.

다음으로 적용해보겠습니다. 다시 favicon generator 사이트로 돌아갑니다.

![스크린샷 2023-03-26 오후 5.13.27](/Users/yeajin/Library/Application Support/typora-user-images/스크린샷 2023-03-26 오후 5.13.27.png)

3번의 코드를 모두 복사해주세요. 이 코드들을 깃 내 html 파일에 붙여넣어 파비콘을 적용해야합니다.

저같은 경우 github.io > _includes > head.html 와 같은 경로로 접근하여 html 파일을 찾을 수 있었습니다. 

테마마다 약간씩 다르긴 하지만 대부분 **_includes > _head > custom.html** 혹은 저처럼 **_includes > head.html** 와 같은 경로로 찾을 수 있는 것 같습니다. 

html 파일을 열어 가장 하단에 복사했던 코드를 붙여넣어 주시면 되는데, 다만 herf= 이하를 수정해주셔야합니다.

아까 파비콘을 넣은 경로가 assets > img > favicon 폴더이므로 각 파일명 앞에 경로를 추가해주면 됩니다.

<link rel="apple-touch-icon" sizes="180x180" href="**_{{site.baseurl}}/assets/img/favicon_**/apple-touch-icon.png"**>** 

위와 같이 볼드 처리된 ''{{site.baseurl}}/assets/img/favicon' 만 각각 앞에 추가해주시면 됩니다.

​                                                 

​                                                

#### b) 파비콘이 이미 적용되어 있는 테마의 경우

이 경우 깃에서 파비콘 파일이 어디 있는지 찾아야합니다. 아마 대부분 a) 에서의 경우처럼

* github.io > _includes > head.html

혹은

- github.io > _includes > _head > custom.html

이와 비슷한 경로에 있으실겁니다.

​                                  

html 파일을 찾았다고 가정하면, favicon이 적용된 코드가 어디인지 찾아야합니다. 

#### <img width="740" alt="스크린샷 2023-03-26 오후 5 36 39" src="https://user-images.githubusercontent.com/71372857/227764644-94d60452-2a74-46ce-8985-e93c9091609a.png">

실제 저의 html 파일 중 일부입니다. 이 링크를 보시면 파비콘 저장 경로가 나와있습니다. 

동일한 경로로 가서 원래 있던 파비콘 이미지를 원하는 이미지로 바꿔치기 해주시면 됩니다. 이 때 파일명도 동일하도록 유의해주세요.

코드를 지우고 a) 처럼 해도 되겠지만, 저는 왜인지 해당 html 코드가 if 코드 안에 위치해있어 코드를 수정하지 않고 진행했습니다. 

​                                      

#### 오류

제가 겪었던 오류는 아무리 똑같은 위치에 같은 파일명으로 파비콘을 넣어도 파비콘이 바뀌지 않는다는 것이었습니다.

<img width="150" alt="스크린샷 2023-03-26 오후 5 43 33" src="https://user-images.githubusercontent.com/71372857/227764974-230d91bd-efe6-4678-8198-28993bdf600d.png">

분명히 앵두 모양으로 파비콘을 바꾸었는데도 불구하고 위처럼 기존 파비콘이 계속 뜨곤 했습니다.

이걸로 꽤나 오래 씨름했는데... 결론은 제가 일으킨 오류가 아닌 브라우저의 잘못(?)이었습니다. 

<img width="603" alt="스크린샷 2023-03-26 오후 5 42 30" src="https://user-images.githubusercontent.com/71372857/227764938-7acd87ef-8a90-4644-bc6b-bc9d630aa3fd.png">

https://stackoverflow.com/questions/19104795/browser-doesnt-show-favicon-ico-but-it-exists



이유는 이전에 해당 사이트에 접속한 이력이 있을 경우, 브라우저가 파비콘을 새로 불러오지 않고 캐싱된 데이터를 이용하기 때문에 바뀌지 않았던 것이었습니다. 

올바르게 한 것 같음에도 불구하고 파비콘이 바뀌지 않으신다면 다른 것들을 하기에 앞서 [이 사이트](https://www.browserling.com)에서 먼저 정상적으로 작동하는지 확인해보시기 바랍니다. 





​                       

​                         

## 마치며

오늘의 포스팅은 여기까지입니다. 파비콘이 적용되지 않아 당연히 무언가가 잘못되었다고 생각하고 한참 찾았지만 오류에 대해서는 잘 나오지 않아서 꽤나 오랜 시간을 허비했는데, 이유를 알고나니 무척 허탈했어서 꼭 공유하고 싶었습니다. 누군가에게는 도움이 되길 바라며 이 글을 마칩니다. 😄

읽어주셔서 감사합니다. 😃

​                    

​                              









 

​                                     
