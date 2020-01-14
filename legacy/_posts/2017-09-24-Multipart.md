---
layout: post
title:  Multipart 요청에 대하여
tags: HTTP 
categories: HTTP
---    

최근 파일 업로드 및 다운로드 기능을  REST API로 구현한 적이 있습니다. 그 과정에서 Spring의 다양한 Annotation을 비롯한, 몇가지 에러사항을 겪을 수 있었습니다. 그 중, 가장 많은 시간을 쏟은 MultiPart에 대해 리뷰해보려 합니다.                 

(2017-09-24 ~ 작성 중.. )   
-> 아직 작성하지 않은 글 입니다.  

# HTTP multipart      

multipart는 HTTP를 통해 File을 Server로 전송하기 위해 사용되는 Content-Type 입니다. 

(muti-part의 form 전송시 요청 message 도 볼것. )

> 개인적으로는 HTML Form을 이용해서 File을 전송할때 사용했습니다.

#### Message    

	POST /files HTTP/1.1
	Host: localhost
	Cache-Control: no-cache
	Content-Type: multipart/form-data; boundary=----WebKitFormBoundary7MA4YWxkTrZu0gW
	
	------WebKitFormBoundary7MA4YWxkTrZu0gW
	Content-Disposition: form-data; name="file"; filename="sav.txt"
	Content-Type: text/plain
	
	나는 데이터 입니다. 
	------WebKitFormBoundary7MA4YWxkTrZu0gW
	Content-Disposition: form-data; name="file"; filename="otherFile.txt"
	Content-Type: text/plain
	
	나는 다른 데이터입니다.
	------WebKitFormBoundary7MA4YWxkTrZu0gW--     


> 시작줄     

HTTP 1.1 버전으로 POST Method를 사용해 '/files'에 요청을 보냅니다. 

여기까지는 다른 request와 크게 차이가 없습니다.   

> Header        

대부분의 요청이 포함하는 몇가지 Header를 볼 수 있습니다.     
Host는 요청한 Server의 주소를 의미하며, Cache-Control: no-cache를 통해 Cache를 사용하지 않는다는 것까지 유추할 수 있습니다. 

> Cache 부분은 추후 정리해보도록 하겠습니다. 
 
여기서 주목해야할 부분은 **' Content-Type: multipart/form-data;'** 입니다. 이 부분을 통해 Body의 Type이 multipart임을 알 수 있습니다.    

mutipart는 말 그대로, **Multi - Part**로, 단일 Body에 다중 Resource로 설계된 Type입니다. 이렇게 하나의 body내에서 여러개의 Resource를 사용하는 이유는 '파일'을 전송하기 위해 설계됐기 때문입니다. 만약 전송하는 파일의 크기가 패킷의 최대크기보다 큰 경우, 해당 Body를 쪼개서 전송하기 위해 multi Resource 방식으로 사용된다고 합니다.     

이때 분리된 본문을 구분하기 위해 구분자로 **boundary= ~**를 사용합니다. 위 예시에선 ----WebKitFormBoundary7MA4YWxkTrZu0gW 으로, 특정 multipart의 Body임을 명시하는 역할을 합니다.  

마지막 Multipart인 경우 --  을 추가적으로 붙여 끝을 알려줍니다.    

>  boundary의 값은 계속 바뀌며, 특정 Multipart의 body를 찾기위해 사용됩니다.     


#### PUT       

 ~~ 작성중 


[The Multipart Content-Type](https://www.w3.org/Protocols/rfc1341/7_2_Multipart.html)      
[[HTTP] 멀티파트 미디어 타입 (Multipart Media Type)[EminentStar's Dev Wiki]](http://eminentstar.tistory.com/47)     

 