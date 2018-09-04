---
layout: post
title:  You don't know Js-Cross-Browser-be-writing
tags: JS
categories: Book
---


웹 개발을 하다보면, 여러가지 Browser Issue에 대응해야하는 경우가 생깁니다.
이번 글에선 Cross Browser에 대응하기 위한, 혹은 대비하기위한 방법들을 정리했습니다.

(제 경험상) 사파리나, chrome 에서도 이슈를 일으키지만, 사실 대부분의 이슈는 IE 에서 일어납니다.

> 특히 IE8 / 9  

예를들어, css selector를 `aria-*`로 선언 후, event를 통해 `aria-*` 값을 바꿀 경우 IE 8 에서는 간헐적으로 동작하지 않습니다.
위 예시 외에도 css의 animiate 와 JQuery의 animate를 동시에 사용하면, IE / Edge 계열에서 정상적으로 동작하지 않습니다.

> 최근에 겪은 이슈라 너무 생생하네요.

이렇듯, client(Browser)들이 모두 다른 방식으로 동작하기때문에 Front에서는 모든 Browser가 만족할만한 코드를 작성해야합니다.


#### avoid to confirm User-Agent  

UserAgent를 확인하며 이벤트를 작성할 경우는 피해야함.

> 특정 브라우저만 꼭 이슈가 있으리라는 보장이 없고, UserAgent가 변경될 여지또한 있음

또한 가정 자체를 줄이는게 매우 중요

#### searching object

객체 사용전, 객체를 확인하여 코드를 돌릴수 있음

> 이때 보편적 경우를 맨위에 두고 실행


#### regression Test

이전 스펙과 동일한지 확인하는 테스트

학습테스트도 좋을듯 
