---
layout: post
title:  You don't know Js-Function-eval-beWriting
tags: JS
categories: Book
---

eval에 대해 학습을 하고 있던 도중, 많은 사이트에서 eval 과 Function을 비교하는 글들이 많아 한번 정리해볼까 합니다.

> 점점 책의 내용과는 상관없는 글들을  쓰고있네요 .. :D

#### Function

> 이전에 [YDK-JS3](/book/2018/08/19/YDK-JS3/)에서 짧게 정리를 했었지만, 한번더 적어보죠!

Function은 Js에서 제공하는 native로 동적(문자열)으로 함수를 생성할 때 사용됩니다.

```
  Function([...arg],body);
```

함수의 선언부는 위와 같으며, 마지막 인자가 body가 되고 그 외 모든 arg는 함수의 argument가 됩니다.

```
  var callName = new Function("name","console.log(name);");
  callName("chulwoon");
  //print :: chulwoon
  //print :: undefined (함수의 반환값이 없으니까요 :D)
```

이런 코드는 동적으로 함수를 생성하기 위해 사용됩니다.

> 사용 시 주의점은 eval을 설명한 뒤 정리하겠습니다.   


#### Eval


> 웃긴건 Eval로도 Function 정의 가능.

#### Performance Issue

> 엔진에 의해 최적화 하지 못함

#### Eval vs Function

> scope에 대하여

#### Indirect call

> 함수 내부에서 사용시 최적화 하지못함.
> 때문에 앵간하면 전역에서 쓰자 ~
> + use strict ? 에 대해서도 학습을 해야할거 같애
