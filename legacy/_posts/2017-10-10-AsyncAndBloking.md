---
layout: post
title:  Async&Sync || Blocking & Non Blocking
tags: 비동기
categories: 비동기
---

길게는 2년 짧게는 1년정도 웹개발, 특히 JSP/Spring을 공부하면서 Thread에 대해 고민해본 적이 없었습니다. 변명을 하자면 .. Tomcat에서 요청마다 Thread를 사용하기에, 단순 요청에 대한 응답의 경우 Thread를 사용할 이유가 없었습니다.

> Tomcat은 Thread pool을 이용해서 Thread를 관리합니다.

하지만 최근에 Thread를 공부해야할 명분(?)이 생겼고 학습을 진행하고 있습니다.

#### Blocking & Non Blocking || Synchronous & Asynchronous

비동기에 빠지지않고 나오는 개념들입니다. 한번쯤은 들어본 단어들로,

	Blocking과 Sync가 같은 개념이고, Non-Blocking과 Async가 같은 개념이다 !

라고 생각하고 있었습니다.  ....

꽤나 잘못된 생각이였고, 각각의 개념은 관점부터가 다르다는걸 알게 되었습니다.


> 관련 내용에대해 잘 정리된 글입니다. [Blocking-NonBlocking-Synchronous-Asynchronous](http://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/)


<center>
<img src="/legacy/public/img/Thread.jpg"/>
[함수 호출에 대한 그림입니다.]
</center>

##### Blocking & Non Blocking


Blocking과 Non-Blocking의 관점은

	호출되는 함수가 바로 Return 되느냐 ?

입니다.

A함수가 B함수에게 request를 보낼 경우,

Blocking은 함수가 완료될 때까지 제어권을 A함수에게 주지 않습니다. 즉, 함수가 완료된 뒤에 제어권을 넘깁니다.

다만, Non -Blocking의 경우 함수의 완료 여부와는 상관없이 바로 결과를 리턴합니다.

> Blocking 과 Non-Blocking의 관점은 호출되는 함수가 바로 Return하느냐 , 마느냐 입니다.


#####  Sync & Async

Blocking 의 관점과는 다르게, Sync와 Async의 관점은

	호출되는 함수의 완료 여부를 누가 신경쓰느냐?

입니다.

A함수가 B함수에게 요청을 보낼 경우 작업 완료여부를 A가 확인하면 **Synchronous**,
A가 완료여부를 신경쓰지않고, Callback등을 이용해 진행하면 **Asynchronous** 입니다.

> 즉 함수의 완료여부를 신경쓰느냐? 의 관점입니다.

Async와 Non-Blocking은 분명 비슷한 동작을 합니다. 다만 전혀 다른 관점이라는 것을 숙지해야합니다.


#### Non-Blocking 과 Sync의 조합

앞서 언급한 바와 같이, Non-Blocking은 함수를 바로 Return 하는것을 의미하고,
Sync는 함수의 완료여부를 호출한 쪽에서 확인하는 것을 의미합니다.

예시로는 Java의 Future - isDone()을 들 수 있습니다.

> Future는 비동기의 결과를 갖고 있는 Interface 입니다.

	    ExecutorService es = Executors.newSingleThreadExecutor();

        Future f = es.submit(()->{ // A
            Thread.sleep(5000);
            return "Hello?";
        });
        while(!f.isDone()){  // B
            // other Task
            log.info("Other Task");
        }

        log.info("Complete " + f.get());
        es.shutdown();

위 코드는 비동기의 작업이 끝났는지 계속 확인하고, 끝나지 않았으면 그동안 다른 작업을 진행하는 코드입니다.  NonBlocking 방식으로 함수는 바로 반환 되지만 (A), sync 방식으로 결과를 호출하는 함수 쪽에서 확인을 하는 로직(B) 입니다.


#### Blocking 과 Async의 조합


Blocking은 함수가 완료될때까지 기다리는 방식이고, Async는 결과를 호출한 쪽에서 확인하지 않는 방식입니다. 사실 위 방식은 Blocking - sync와 크게 성능적으로 차이가 없다고합니다.
주로 위 방식은 의도하고 사용하는게 아닌, 의도치않은 상황에서 사용된다고 합니다.

> Non Blocking - Async 방식을 사용하는 과정에서 중간에 Blocking이 한번이라도 발생하면, Blocking - Async가 된다고 합니다.

(이 부분은 아직 경험해본적이 없어, 크게 와닿지는.... )



#### 정리하며


그 동안 Blocking이랑 Sync랑 같고 Non-Blocking이랑 Async랑 같은거 아닌가? 라는 생각을 갖고 있었고, Non-Blocking I/O를 공부하면서 왜 Async I/O가 아닐까 라는 .. 의문점을 갖고있었습니다. 그러던 중, [Spring Camp의 영상](https://www.youtube.com/watch?v=HKlUvCv9hvA)을 보게됐고 Async와 Non-Blocking은 관점부터가 다르다는 말을 듣게 되었습니다.

두 개념이 어떤 관점을 갖고있는지, 하루정도 헤매다가 좋은 글을 발견해서 제가 이해한 것을 토대로 재정리해봤습니다.

아래 글을 보고 쉽게 이해할 수 있었습니다.

[Blocking-NonBlocking-Synchronous-Asynchronous](http://homoefficio.github.io/2017/02/19/Blocking-NonBlocking-Synchronous-Asynchronous/)



2017 .10 .10
lusiue@gmail.com




