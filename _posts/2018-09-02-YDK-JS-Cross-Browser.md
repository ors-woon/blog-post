---
layout: post
title:  You don't know Js-Cross-Browser-be-writing
tags: JS
categories: Book
---

웹 개발을 하다보면, 여러가지 Browser Issue에 대응해야하는 경우가 생깁니다.
이번 글에선 Cross Browser에 대응하기 위한, 혹은 대비하기위한 방법들을 정리했습니다.

#### 브라우저 버그를 해결하려 하지말아라

대부분의 브라우저는 버그를 가만히 내버려두진 않습니다.
브라우저의 버그는 언젠가 고쳐질 것이며, 버그를 의존하는 전략은 꽤나 무모하고 위험한 전략이라고 말합니다.

> 브라우저의 버그가 해결될 경우, 기존코드가 동작하지 않을 수도 있습니다.

다만, IE라는 복병이 있기에 .. 브라우저의 버그를 해결하기보단, 회피하는게 옳은 방법이라고 생각되네요.

#### 캡슐화 하라

이 부분은 cross browser와 크게 연관은 없지만, 브라우저가 지원하는 함수와 API 혹은 개발자가 작성한 코드가 충돌이 나지 않도록 하는 기법(?)입니다.

코드를 모듈화하고 최소한의 property를 제공하여, 의도치않은 동작을 막아야합니다.

> 꼭 cross browser Issue만은 아니며, (Js를 사용한다면) 전역 오염을 주의해야합니다.

#### Browser Issue

모든 Browser(정확히는 Engine)가 제공하는 함수도 있지만, 그렇지 않은 경우가 더 많습니다.
특히나, 한국에서는 이제는 지원하지도 않는 IE를 계속 사용하고있어 많은 cross browser Issue를 겪게됩니다.

물론 babel(es6 -> 5) 이나 polyfill을 사용하여 `어느정도` 문제를 해결 할 수는 있으나 근본적인 해결책은 되지못합니다.

이번 챕터에서는 Cross browser Issue를 대응할때, 주의점과 사용할 수 있는 기법에 대해 학습합니다.


#### avoid to detect User-Agent  

웹, 특히나 Front 는 여러 종류의 Client(Browser)들을 지원해야합니다. modern client들은 대부분의 JS 함수들을 지원하지만, IE 계열(특히 8-9)은 update를 멈춘 상태이기에, 발전하는 Js를 따라가지 못하고 기능이 제한됩니다.

> 만약 서비스에서 IE8을 지원할 경우, Array.filter, Array.reduce 등을 포함한 수많은 함수들을 사용하지 못합니다.

이러한 기능들을 사용하기위해, `polyfill`, `Fallback` 등의 방안을 준비해야하는데요.
이때 사용해선 안되는 방법중 하나가, User-Agent(이하 UA)를 확인해 Fallback을 구현하는 것입니다.

User-Agent란, client의 정보를 담고있는 속성으로 브라우저를 식별할 수 있는(?) 정보를 제공합니다.


```
if(navigator.userAgent.toLowerCase().indexOf('firefox')>-1){
  // someThing01
}else{
  // someThing02
}

```

UA를 사용하여 fallback을 구현하는 행위는 꽤나 위험할 수 있습니다.

#### userAgent history

[userAgent history](https://webaim.org/blog/user-agent-string-history/)를 보면 Netscape 출현 당시, Netscape는 frame을 지원했고, Mosaic browser는 지원하지 않았기에, 웹서버들은 Netscape의 UA인 `Mozilla`로 frame의 유무를 판단하고 있었습니다.

이미 많은 곳에서 UA로 frame을 분기처리했기에 이 후 나오는 브라우저(`IE,firefox,chrome..`)들은 모두 `Mozilla/`UA를 붙여 나오게 됩니다.

> 즉, 대부분의 브라우저들이 Netscape의 UA를 모방하고 있습니다.

위 사례처럼 UA는 쉽게 변경 될 수 있고, 모방되어질수 있습니다. 대부분의 Browser가 Mozilla로 시작하는 이유가 여기에 있었죠..
때문에 UA를 이용한 분기 처리는 꽤나 위험하여 되도록 사용되어선 안됩니다.


#### object detection

Object Detection 방식은 Js의 `truly` , `falsy` 특성을 이용한 방식입니다.

```
  if(Object.keys){
    // 지원 할 경우  
  }else{
    // 지원하지 않을 경우
  }
```

`Object.keys`의 함수는 IE9 부터 지원하여, IE8에서 사용할 경우 undefind error를 뱉습니다.
이를 방지하기 위해, 위처럼 object detection으로 분기처리 할 수 있습니다.  

> Js의 falsy 특성을 이용한 것으로 함수가 존재하지 않을 경우, false를 뱉게 됩니다.

Object Detection을 사용할 경우, 가장 보편적 혹은 점유율이 높은 브라우저 순으로 분기처리를 진행하는편이 좋습니다.

> 가장 Best는 모든 브라우저에서 실행 가능하도록 코드를 변경하는 것이지만 ... 그러지 않은 경우가 간혹 있습니다.

#### regression Test

regression Test 를 직역하면 회귀 시험으로, client가 업데이트 되었거나 환경등에 변화가 있을 때, 이전 기능이 제대로 동작하는지 확인하는 Test 입니다.

브라우저는 계속 버전업 되고, 그때마다 모든 변경점을 파악하여 대응하기란 쉽지않습니다.
때문에 사용성을 보장하기위해 regression Test는 필수 입니다.

>  Clean Code 에서 언급되는 학습테스트 또한 꽤나 유용한 방법입니다.

학습테스트란 특정 lib / api를 사용할 때, 내가 사용하려는 방식대로 test case 작성하고 input/output을 확인하는 기법(?)입니다.
이럴 경우, 내가 사용하려는 기능을 Test할 수 잇기때문에 앞서 언급한 regression Test를 진행하기에도 용이합니다.


#### 마치며

책이나 많은 자료들이 cross browser Issue들을 해결할 수 있는, 엄청난 기법(?) 같은걸 알려주진 않습니다.

> 애초에 엄청난 기법은 없죠.

다만, 변화에 대응하기위한 방법 및 주의점들을 알려줄 뿐입니다.

읽어주셔서 감사합니다.    
lusiue@gmail.com
