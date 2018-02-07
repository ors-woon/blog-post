---
layout: post
title:  High Performance Web Sites-4
tags: self-study
categories: book-webPerformance
---

#### High Performance WEB    

#### Rule.4 Gzip 컴포넌트     

Rule 1.과 3.이 Http Call 수를 줄이는 방법을, Rule 2. 가 구성요소와 서버간의 거리를 줄이는 방법에 대해 말했다면, 이번 Rule은 `Client`와 `Server`간의 주고받는 `Data 크기`와 관련된 Rule 입니다.    

> 정확히는 응답 크기를 줄이는 방법입니다. 

일단적으로 Browser와 Server간의 압축 관련 Header는 다음과 같이 이루어집니다.   
**Request**

	Accept-Encoding: gzip, deflate
　 
**Response**

	Content-Encoding: gzip

Client가 Server에게 자신이 해독가능한 압축 방법을 명시하면, Server는 그 중 하나를 선택해서 압축한뒤 Response에 사용한 압축 방법을 알려줍니다.   

모든 요소들을 압축하면 응답의 크기는 분명 줄겠지만 어느정도 고려해야하는 요소들이 있습니다.  

바로 `CPU 비용`과 `파일 크기` 입니다.    

일반적으로 1KB - 2KB 보다 큰 파일들을 압축하며, 높을 효율을 위해 어떤 파일들을 압축할 것인지 선택해야하며, 압축에 관련된 작업들은 CPU를 사용하기에 신중해야 합니다.   
 
> 주의할 점은 Img 및 pdf등은 이미 압축된 형태이기에 적용해서는 안됩니다.   
> 기본적으로 Apache는 50KB 이상 파일들만 압축한다고 합니다. (default)   

#### Proxy 난입    

보통의 경우 gzip은 큰 문제는 없습니다. 다만 중간에 Proxy (+ cache)가 껴버리면 이상한 그림을 그리게 됩니다.    


> 보통의 경우    

	Client A -> Proxy -> Server   
	Client B -> 

이와같은 구조에서 A는 gzip를 지원하고 B는 gzip를 지원하지 않을때, A가 먼저 Proxy에게 요청을 보내면 Proxy는 gzip된 응답을 내부적으로 갖고있게 됩니다. (Cache)   
이때 Client B가 요청을 하면 Proxy는 gzip 된 응답을 보내주게 됩니다.  

> 이때 Client B는 gzip을 해결할 수 없으므로 에러가 나겠죠 ..   
> 반대의 경우도 마찬가지입니다.   

이와같은 상황때문에 `Vary` 라는 Header를 사용해야합니다.  

Vary는 서술된 Header에 따라 각각의 Cache를 갖게 할 수 있는 Header 입니다.   
위와같은 상황에서 Vary를 사용하면 제대로된 응답을 받을 수 있습니다.  

	Vary : Accept-Encoding

> 이는 웹서버에서 Proxy로 응답을 보낼때 서술할 수 있습니다.

위 방식은 Accept-Encoding에 따라 각각 Cache를 저장하겠다는 의미입니다.

Vary는 이런 관점에서 정말 좋은(?) Header 이지만, Vary 속성이 늘어날 수록 Proxy 쪽의 Cache Data가 증가하고 Cache 메모리 영역이 꽉 찰 경우, 이를 비웁니다.

즉 불필요하거나 너무 많은 Header를 추가할 경우 Cache Hit  비율이 급격히 떨어질 수 있습니다. 

> 조심히 사용해야한다고 하네요  

#### 추가적으로   

예전  ie 5.5 등에서 gzip을 허용한다고 해놓고 해석하지 못하는 버그가 존재했다고 합니다.
때문에 대규모 브라우저에서는 `브라우저 화이트리스트` 라는걸 만들어 해당 버전에 대해서만 압축결과를 보여주는 등의 작업을 진행했다고 합니다.  

마찬가지의 이유로 Vary에 user-agent 속성까지 추가해야 할수도 있는데, 이는 위에서 언급한 이유때문에 좋지 않으며, 모든 브라우저를 포함해야하는 경우 Cache를 지원하지 않는것도 방법이라고 합니다.  

> 이건 상황에 맞게 사용해야 할것같습니다.   

	오래된 브라우저를 지원하지 않겠다 -> user-agent 사용 X / Cache 사용
	그래도 모든 브라우저를 지원하겠다 -> Cache 미사용

여기서는 각 WS , WAS 에서 gzip을 다루는 법에 대해 정리하진 않았습니다. ~ 


Header 와 각 서버 사이에서 발생 할 수 있는 문제에 대해 학습할 수 있는 좋은 시간이였습니다. 

2017 .12 .19
