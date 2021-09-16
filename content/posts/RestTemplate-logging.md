+++ 
author = "chulwoon.jang" 
title = "RestTemplate Request / Response Logging" 
date = "2021-09-15" 
description = "RestTemplate Logging 예제" 
tags = ["RestTemplate"] 
series = ["Spring"] 
categories = ["Kotlin","Spring"] 
+++

QA 및 개발 환경에서 외부 API에 대한 요청/응답을 로깅해야하는 상황이 자주 발생합니다. 이 글에선 RestTemplate 을 Logging 하기위한 설정을 다룹니다.

> Log 크기에 따라 real 환경에선 사용을 피하는 편이 좋습니다.



## IO Stream ??

- 왜 한번만 읽나? 



## RequestFactory ?? 


## 설정 







```java 
public class BufferingClientHttpRequestFactory
extends AbstractClientHttpRequestFactoryWrapper
Wrapper for a ClientHttpRequestFactory that buffers all outgoing and incoming streams in memory.
Using this wrapper allows for multiple reads of the response body.
```

