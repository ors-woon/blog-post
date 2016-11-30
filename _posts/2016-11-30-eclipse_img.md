---
layout: post
title:  "[project] eclipse 이미지 업로드문제"
date:   2016-11-29 10:32:00 +0200
tags: ['Java', 'eclipse', 'img upload']
author: "Jang chulwoon"
---

   

   
이클립스 및 스프링(sts)를 이용하여 프로젝트를 진행하고 있습니다.   
이미지를 업로드하는 부분을 진행하고 있는데요.
(Spring 환경에서 multipartResolver를 이용하고있습니다.)  
   
업로드하는 부분을 진행하면서, DB에 img를 넣는 것보다 img경로를 저장하는 편이 낫다는 조언을 받았습니다.  

DB에는 경로를 저장하고, img파일은 resources 폴더내에 저장하는 방향으로 개발을 하고있습니다.   

이 과정에서 File I/O를 이용하여 프로젝트내에 저장하는 부분까지는 문제가 없었으나,   
해당 resources 폴더를 refresh 하지 않으면 img 파일을 불러올 수가 없어, 구글링을 하던 도중 해결방법을 찾았습니다.
 
문제는 workspace에서 파일을 인식 못하기때문이라고 하네요     
실제 서버에 올릴때는 문제가 없다고하네요.

자세한 내용은 [Java doc]('http://okky.kr/article/245013')을 참고해주세요

   
> 요약   preferences->General->Workspace에서 상단에 refresh 체크박스 두개 체크



lusiue@gmail.com    
2016-11-29 장철운. 


