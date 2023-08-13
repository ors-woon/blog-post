+++ 
author = "chulwoon.jang" 
title = "Linux Command Tips And Tricks" 
date = "2020-06-14" 
description = "리눅스 커맨드 팁 정리" 
tags = ["Linux","Trick"] 
series = ["Linux"] 
categories = ["Linux"] 
summary = " "
+++


본 포스트는 [20 Linux Command Tips and Tricks That Will Save You A Lot of Time](https://itsfoss.com/linux-command-tricks/)에 나오는 일부 팁들을 정리한 글입니다.


> 자세한 내용은 위 포스트를 참고해주세요 (_ )

#### Linux Tips and Tricks

개인적으로, 새로운 IDE를 쓰거나, Tool 혹은 언어를 배울때면, 단축키나 Trick을 찾아 공부하고 있습니다. 

> 어느정도 생산성을 올려줘서 찾는 것도 있지만, 사실 멋잇어보여서 .. 

필요할때마다 찾아보고있는데, 한번 정리해두면 보기 편할거 같아, 글로 정리해봅니다 :) 


#### Running multiple commands in one single command

여러개의 명령어를 한줄로 치기위한 방법입니다.

예를들어, 여러 component를 command line 으로 빌드하려할떄, 아래처럼 명령어를 입력 할 수 있습니다.

```
build a; build b; build c
```

> 다만 위 명령어는 앞의 명령어의 성공 여부와는 상관 없이 다음 명령어를 실행 시킵니다.

만약 의존성을 갖는 component 의  build 라면 아래 처럼 AND 연산자를 사용해야합니다.

```
build a && build b && build c
```


#### Easily search and use the commands that you had used in the past

(개인적으로 자주있는 상황인데..) grep / cut 을 이용해 검색을 한 후에, directory를 넘다드는 과정에서 이전에 친 명령어가 다시 필요한 적이 있습니다.

보통 history 명령어로 다시 찾곤하는데, 그럴 필요 없이 아래 tricks 로 쉽게 찾을 수 있습니다.


```
ctrl + r serach_term
```

#### Reuse the last item from the previous command with !$

이전 명령어에 대한 argument 를 사용하고 싶을떄는 아래처럼 !$ 을 쓸 수 있습니다.

> 당장 떠오르는 상황은 아래같은 상황이네요

```
cd SQLGateDriver_1.1.log
cd: not a directory: SQLGateDriver_1.1.log
vi !$
vi SQLGateDriver_1.1.log
```

#### Reuse the previous command in present command with !!

이전 명령어를 !! 로 불러올 수 있습니다. 


```
vi kmplex-hugo-bloc
vi /etc/hosts
sudo !!
```
