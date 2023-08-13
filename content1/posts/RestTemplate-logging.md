+++ 
author = "chulwoon.jang" 
title = "RestTemplate Request / Response Logging" 
date = "2021-09-15" 
description = "RestTemplate Logging 예제" 
tags = ["RestTemplate"] 
series = ["Spring"] 
categories = ["Kotlin","Spring"] 
summary = " "
+++

QA 및 개발 환경에서 외부 API에 대한 요청/응답을 로깅해야하는 상황이 자주 발생합니다. 
이 글에선 RestTemplate 을 Logging 하기 위한 설정을 다룹니다.

> 메모리를 사용하는 방식이기에, Log 크기에 따라 real 환경에선 사용을 피하는 편이 좋습니다.

## 설정 요약 

> interceptor 


```kotlin
class HttpResponseLoggingInterceptor : ClientHttpRequestInterceptor {
    companion object {
        val log = LoggerFactory.getLogger(this::class.java)
    }

    override fun intercept(request: HttpRequest, body: ByteArray, execution: ClientHttpRequestExecution): ClientHttpResponse {
        if (body.isNotEmpty()) {
            log.info("reqeustBody : $body")
        }

        val response = execution.execute(request, body)

        log.info("response Header : ${response.headers}")
        log.info("response body : ${IOUtils.toString(response.body, UTF_8)}")

        return response
    }
}
```

> restConfig 

```kotlin
@Configuration
open class GitBlogRestTemplateConfig {

    @Bean
    open fun gitBlogRestTemplate(restTemplateBuilder: RestTemplateBuilder): RestTemplate = restTemplateBuilder
            .requestFactory { BufferingClientHttpRequestFactory(SimpleClientHttpRequestFactory()) }
            .additionalInterceptors(listOf(HttpResponseLoggingInterceptor()))
            .build()

}
```



## IO Stream ??

Stream이란 운영체제에 의해 생성되는 가상의 통로로, 프로그램과 I/O 사이의 중간 매개자 역할을 수행합니다. Stream 은 단방향으로만 통신이 가능하다는 특성을 갖고 있는데요. 때문에 Java 에서는 java.io package 안에 InputStream 과 OutputStream을 각각 제공하고 있습니다.   

> InputStrema 과 OutputStream 은 추상 클래스로, 일부 함수를 재정의해야합니다. 

앞서 말했듯, Stream 은 가상의 통로로, 별다른 설정을 하지 않는다면 한번 소비된 byte를 다시 읽어 올 수 없습니다. 

> mark / reset 등의 함수를 이용하여, 원하는 지점부터 다시 읽을 수 있으나, 추가 구현이 필요하며, 데이터를 memory에 저장하여 관리해야합니다.

이러한 특성으로, RestTemplate 의 request / response 를 logging 하기위해선 `별도의 설정` 이 필요합니다.

## ClientHttpRequestFactory ?? 

ClientHttpRequestFactory는 Connect / Socket timeout 및 connectionPool 등의 설정을 지원하며, ClientHttpRequest를 생성하는 factory class 입니다.
개인적으로 혼란스러웠던 부분은, `RequestFactory 가 Response Logging 에 무슨 영향이 있지?` 인데, ClientHttpRequest가 execute 함수를 제공하며, response 를 리턴하기 때문에, 이를 생성하는 Factory 구현에 영향을 받습니다.

> BufferingClientHttpRequestFactory 주석에도 이 내용이 적혀있는데요.

```java 
public class BufferingClientHttpRequestFactory
extends AbstractClientHttpRequestFactoryWrapper
Wrapper for a ClientHttpRequestFactory that buffers all outgoing and incoming streams in memory.
Using this wrapper allows for multiple reads of the response body.
```


RestTemplate의 getBody함수는 InputStream 을 통해 결과를 읽어오는데, 앞서 말했듯 Stream 은 한번 소비되고 다시 읽을 수 없기때문에 interceptor를 이용해 body를 logging을 하게되면, 실제 response 를 가져올땐 body가 빈 상태로 서비스 코드에 전달됩니다.


이를 막기위해 `BufferingClientHttpRequestFactory` 를 설정해줘야하며, QA / Local 환경에서만 설정하는걸 권장드립니다. 

`주의`

BufferingClientHttpRequestFactory를 타고 가다보면, Response 를 BufferingClientHttpResponseWrapper로 감쌓는걸 볼 수 있습니다.
해당 클래스는 body 값을 property로 들고 있어, 응답이 클 경우 메모리 관리에 영향을 주게 되는데요.
특별한 이유가 아니라면, real 상황에서는 사용을 자제해야합니다.