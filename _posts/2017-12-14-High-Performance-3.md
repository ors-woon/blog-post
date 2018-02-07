---
layout: post
title:  [Rule.3] High Performance Web Sites
tags: self-study
categories: book-webPerformance
---

#### High Performance WEB    

#### Rule.3 Header에 만료기한을 추가하라. 

그동안 대부분의 로직들을 서버단에서 Caching하는 작업을 진행했었습니다. 주로 했던 프로젝트들이 다른 사이트의 결과물을 파싱하고, 그걸 알맞은 형태로 보여주는 프로젝트였기에 Server to Server에서 Connection 맺는 작업을 피하기 위해 결과들을 Caching하는 작업을 진행했었습니다.
한마디로 말하면, Browser 쪽에서는 전혀 Caching작업을 진행하지 않았습니다.   

책에서는 Header에 만료기한을 추가하여 Cache설정을 하라는 말을 합니다.  
이때 사용하는 Header가 Expires인데 해당 요소가 언제까지 유효한지를 표시한 Header 입니다.  

	Expires: Wed, 21 Oct 2015 07:28:00 GMT

즉, 21 Oct 2015 이후에는 해당 정보는 신선하지 않다고 판단하며, Browser는 새로운 정보를 받기위해 요청을 날리게 됩니다. 이는 If-Modified-Since / Last-Modified 의 대안으로 조건부 Get 요청을 날리지 않고 Browser 스스로가 Cache 데이터를 판단할 수 있습니다.    

(부록)

If-Modified-Since / Last-Modified 는 마지막으로 파일이 마지막으로 업로드된 상태를 의미하며, 브라우저는 이를 확인하기 위해 한번의 Request를 날리게 됩니다.
다만 이는 'Rule.1 요청을 줄여라'에 위반되는 내용일 뿐더러 불필요한 행위입니다.  

#### Expires와 Cache-control    

다시 돌아와서, If-Modified-Since / Last-Modified의 문제를 해결한 Expires에도 몇가지 문제점이 존재하는데 Server 와 Client간의 시간을 맞춰야한다는점과 시간 만료시 요청을 통해 갱신을 해야된다는 점입니다.   

이를 해결하기위해 
	
	Cache-controle : max-age = 0 

라는 헤더가 나왔고, 이는 Browser가 해당 자원을 갱신 할 시간을 정의합니다.    
이 방법을 사용하면 Expires의 문제점을 막을 수 있습니다 !    
다만 문제는 이 방법이 HTTP 1.1 부터 지원한다는 점인데, 1.0 버전을 사용할 경우, 일차적 대안은 Expires 와 Cache-control Header 2가지 모두를 추가하는 방법입니다. 이렇게 사용하면, 브라우저가 Cache-control을 지원하지 않을 경우, Expires Header를 사용합니다. 

> 즉 우선순위가 Cache-control이 더 높습니다.    

다만 이럴 경우, 마찬가지로 Expires로 실행되어 문제가 재발하는데 Apache에서는 이런 문제를 해결하기 위해 mod_expires라는 모듈을 제공하고, 해당 모듈은 Expires를 max-age와 비슷하게 상대적인 시간으로 지정할 수 있게해줍니다.  

즉 Cache-control 에 상대적 시간을 기준으로,

	Expires : ( 현재시간 + 상대시간 ) 

위와 같이 설정해주는 module이 존재합니다.  

> 해당 설정 방법은 여기서 설명하진 않습니다.   

#### Empty Cache or Primed Cache      


지금까지 설명한 Cache 관리법은 사실, 사이트에 처음 접근한 사용자에게는 효력이 없습니다. 


> 첫 요청에는 Cache 처리가 안되어있으니 ... 

즉, 이 방법으로 효력을 얻기 위해서는 `꽉찬 캐시`를 갖고 있는 사용자가 얼마나 자주 들어오느냐에 달려있습니다.

책에서는 `빈 캐시`와 `꽉찬캐시`의 여부는 WEB APP의 성격에 따라 달라진다고 말하는데, 오늘의 XX 같은 서비스들은 한 세션에 한 번의 페이지 뷰 (PV)만을 받을 수도 있습니다. 즉 이런 경우엔 `꽉찬 캐시`를 볼 확률이 낮아지게 됩니다.


( 개인적 생각 )     

일반적으로 사용하는 포털사이트들은 비교적 꽉찬 캐시가 될 확률이 높다고 생각되는데, 아무래도 Naver 메인만 보고 사이트를 나가는 사람은 비교적 소수이기 때문입니다. 
    
> Naver 에서 메인만 보고 나가는 사람이 많을까요 ..?


#### Cache 기간은 얼마로 정할 것인가 ? 

Browser가 Cache를 저장하는 방법은 여기까지로 하고, 조금 더 신경써야 할 부분은, Cache의 저장 기한에 대한 문제입니다. Cache는 불필요한 Request를 줄일 수 있다는 장점이 존재하지만, 이전 버전의 File을 Browser가 들고 있어 문제를 일으킬 수 있다는 단점 또한 존재합니다.     

> "Cache에 대한 Version 관리는 어떻게 할 것인가?"


책에서는 대부분의 사이트들이 30일 이상의 Expires를 갖고있다고 말합니다. 즉 30일 동안은 사용자가 Cache를 비우거나, Browser가 Cache 메모리 부족으로 해당 부분을 삭제하기 전까진 구성요소들이 Browser에 남아있게 됩니다.    

그럼 얘네들은 File 관리를 안하는 건가요 ?   

Naver 사이트를 가서 Network 창을 열어보면 답을 알 수 있습니다.  

	JsFileName_v1?v=2017121412 
	JsFileName_v2?v=2017121412   

네.    
Version 관리를 통해 캐시를 관리할 수 있습니다.    
즉, 파일명이 바뀌면 Browser는 당연히  해당 파일을 다른 파일로 인식하고, 새롭게 캐싱하게 됩니다. 

> 버전관리가 이렇게 중요합니다.     



#### 마무리    

이미 구현된 시스템의 경우 해당 부분을 건들 이유는 없을거 같습니다.   
이미 다 구현되어 있으니까요 ..   
하지만 웹 개발자라면 ... 알고 있는것도 좋은거 같습니다.
