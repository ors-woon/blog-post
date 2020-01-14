---
layout: post
title:  You don't know Js-Function-eval
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

`Function`과 마찬가지로 동적으로 코드를 생성하는 함수입니다.

```
  eval("var a = 5;");
  console.log(a);
  // print :: 5
```

위 코드를 보고 예상할 수 있겟지만, eval을 사용해서도 함수를 정의할 수 있습니다.

```
  eval("function evalTest(){ console.log('hello');}");
```

그럼 Function 대신 eval을 쓰면 되지않을까요 ??

#### Eval vs Function

Eval과 Function의 가장 큰 차이는 `Scope` 입니다.


> Function

```
  #case1
  var name = "global";
  function wrapperFunc(){
    var name = "local";
    var call = new Function("console.log(name); ");
    call();
  }
```

> Eval

```
  #case2
  var name = "global";
  function wrapperFunc(){
    var name = "local";
    eval("var call = function(){ console.log(name); }")
    call();
  }
```

위 두 코드를 실행해보면 `Function` 과 `eval`의 다른 점을 알 수 있습니다.
Function은 어디서 선언되었든, global scope를 바라보지만, eval은 선언된 scope의 변수를 바라봅니다.

이는 기본적인 컨셉의 차이일뿐, eval로 global을 바라보는 방법이 없는건 아닙니다 !

> 약간의 편법(?)으로 global 변수를 바라볼 수 있습니다.

#### Indirect call

직역하자면 간접 호출이라는 의미로, eval을 간접적으로 하면 global scope를 바라볼 수 있습니다.

```
  function wrapperFunc(){
    var name = "local";
    var indirectEval =  eval;
    indirectEval("var call = function(){ console.log(name); }");
    call();
  }
```

> 정말 이상하게도 위 처럼 eval을 다른 변수에 담아 실행 할 경우, global을 바라봅니다.

(여기서 끝나면 정말 좋겠지만,) 2줄의 Indirect call()을 1줄로 줄이는 방법이 존재합니다.


```
  function wrapperFunc(){
    var name = "local";
    (0, eval)("var call = function(){ console.log(name); }");
    call();
  }
```

> Js에서 변수를 `,`로 연결하면 마지막 변수가 반환 됩니다.

```
  var a = 4;
  var b = 5;
  a,b
  // print 5
```

이러한 특성을 이용한 `indirect` 코드 입니다.

`(0, eval)(dynamicCode);`

괄호 후에 eval이 반환되고, 반환된 eval에 동적 코드를 삽입하게되면, 2줄짜리 코드처럼 간접 호출이 일어납니다.
때문에 global scope를 바라보게되고, Function과 유사한 동작을 수행할 수 있습니다.


#### Issue.1 performance

Js를 Interpreter 언어라고 많이들 알고있지만, compile 언어 입니다.

> 과거엔 Interpreter 언어였지만, 모던 엔진들은 JIT를 사용합니다.

많은 엔진들이 compile 과정에서 최적화를 수행하는데요.
만약 함수 내부에 local scope를 바라보는 `eval`이 정의되어 있을 경우, 최적화를 할 수 없습니다.
때문에 많은 사람들이 Function 사용을 권장합니다.

> Function은 지역변수를 바라보지 않기때문에, 함수를 최적화하는데 아무런 문제가 없습니다.

만약 eval을 꼭 써야만 한다면, indirect call 사용을 권장합니다.


#### Issue.2 Security

> 사실 이 부분이 제일 이슈 이긴 합니다 ..

`동적으로 코드를 생성한다.`라는 의미는 쉽게 오염가능하다라는 의미로도 해석가능합니다.
즉, 불순한 의도를 가진 사용자가 이를 이용할 경우 어떤 이슈가 생길지 장담할 수 없습니다.

때문에 많은 eval 코드들은 사용전 정규식 등을 이용해 유효성 검사를 진행하거나 문자열을 삭제/수정 해야할 수도 있습니다.

> 분명 추가적 비용이 들며, 정상적으로 동작하리라는 보장을 할 수 없습니다.

#### 마치며

이러한 이유(`Issue1,2`)들로 eval보단 Function 사용을 권장하며, 되도록 둘다 사용하지 말라고 말합니다.
특별한 이슈가 아니라면 eval/ Function을 사용해선 안됩니다.


> 그래도, 작성된 코드를 보게될 순있으니 위 내용들을 숙지해둬야할거 같네요 :D

읽어주셔서 감사합니다.

lusiue@gmail.com  
