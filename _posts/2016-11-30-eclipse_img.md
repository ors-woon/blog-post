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

자세한 내용은 [okky]('http://okky.kr/article/245013')을 참고해주세요

   
> 요약   preferences->General->Workspace에서 상단에 refresh 체크박스 두개 체크



+덧   

**DB에는 경로를 저장하고, img파일은 resources 폴더내에 저장하는 방향으로 개발을 하고있습니다.  **   

이 과정에서 업로드 경로에 관해서 고민을 했는데요 ..

1. "C:\\Users\\lusiu\\Desktop\\dev_work\\spring\\BuskingRoad\\src\\main\\webapp\\resources\\img\\";  

	프로젝트 경로를 따서 저장하는 방향으로 진행하고 있었습니다.   
	다만 실제 서버에 올릴 경우, 위 경로는 의미가 없어지기에 다른 방법을 찾던 도중 sevletcontext의 getRealPath()를 발견했습니다.   
  
2. context.getRealPath("/resources/img/")     

	sevletContext를 컨테이너에서 가져와 위와같이 사용했습니다.   
	이렇게 사용 할 경우,   **C:\Users\lusiu\Desktop\dev_work\spring\.metadata\.plugins\org.eclipse.wst.server.core\tmp0\wtpwebapps\BuskingRoad\resources\img\**   
	의 경로를 가져오게 되네요.     


자세한 내용은 [스프링 팁 Spring에서 파일 업로드를 위한 세팅]('http://androphil.tistory.com/entry/%EC%8A%A4%ED%94%84%EB%A7%81-%ED%8C%81-Spring%EC%97%90%EC%84%9C-%ED%8C%8C%EC%9D%BC-%EC%97%85%EB%A1%9C%EB%93%9C%EB%A5%BC-%EC%9C%84%ED%95%9C-%EC%84%B8%ED%8C%85')을 참고해주세요


lusiue@gmail.com    
2016-11-29 장철운. 


