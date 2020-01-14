---
layout: post
title:  You don't know Js-property-attribute
tags: JS
categories: Book
---

이번 장에서는 property 와 attribute에 대해서 학습합니다.
property / attribute의 차이와 나아가 브라우저에서 어떤 차이를 갖고있는지 알아보죠.

> 책에서는 property 와 `속성` 이라고 언급합니다. 다만, 저렇게 읽다보니 점점 헷갈려져서 `attribute`라고 표현하겠습니다.

#### property or attribute

property는 DOM이 생성될때 삽입되는 부분을 의미하고, attribute는 HTML에 삽입되어 있는 속성을 의미합니다.

```
  <div id = "divId" class = "divClass"></div>
```

HTML 내부에 위 같은 Node가 선언되어있다고 가정합시다.
여기서 property 와 attribute를 통해 id를 가져오는 방식은 아래와 같습니다 .

```
  var test = document.getElementById("divId");
  test.id // property
  test.getAttribute("id"); // attribute
```

여기까지 봤을 때, attribute와 property의 차이는 직접 접근하느냐, getter를 이용하여 접근하느냐의 차이로 생각됩니다.

#### performance

attribute를 사용해서 값을 얻어오는 것과, property로 값을 얻어오는 것에는 분명한 속도차이가 존재합니다.

개인적으로는 극단적으로 dom의 속성값을 가져오는 경우가 없을거라 생각되지만, 성능이 정말 중요할 경우 property 사용을 고려하거나 property를 사용하는 library 를 고려하는것도 방법입니다.

말로하는것보다 간단한 코드를 가져와서 봅시다.

> property

```
console.time("#1");

for(var i = 0; i< 1000000; i++){
	test.id;
}
console.timeEnd("#1");


// 18.23095703125ms
```

> attribute

```
console.time("#2");

for(var i = 0; i< 1000000; i++){
	test.getAttribute("id");
}
console.timeEnd("#2");

// 82.51904296875ms
```

공정한 심사(?)를 위해 아무짓도 하지않고 단지 `접근`만 했을 떄의 차이입니다.
100만번 접근할 경우 5배 정도의 차이가 나는데요.

> 사실 100 만번 ... 접근 할 일은 만들면 안되겠죠 ..?

이 부분은 사실 `성능 vs 가독성` 이슈와 연결해서 생각해봐도 좋을듯 합니다.

> 그 이유는 아래에 나옵니다.

#### expression

위에서 `성능 vs 가독성`을 언급한 가장 큰 이유는 `표현의 차이` 때문입니다.
모든 property와 attribute가 동일한 이름을 갖고있다면 정말 좋겠지만 ..  아쉽게도 그러진 않습니다.
js 언어에서 갖고있는 예약어 와 표기법의 차이 때문인데요.

예를들어 모든 Node에 선언될 수 있는 `class` 는 js에서 존재하는 예약어 이므로 사용하지 못합니다.

> 이부분은 es6의 스펙이긴하지만 ..

때문에 property에서 `class`에 접근하기 위해서는 다른 이름을 이용해서 접근해야합니다.

```
var test = document.getElementById("divId");
test.className
test.getAttribute("class"); // attribute
```

`class`를 비롯한 다양한 keyword에서 property , attribute의 선언차이가 존재합니다.

> 심지어는 브라우저 마다도 property가 다를 수 있습니다.

이러한 이슈들 때문에 개인적으로는 property를 선호하지는 않습니다.


#### difference result

표현에만 차이가 있으면 좋겠지만, 반환 값들에서도 차이가 존재합니다.


> HTML

```
<a id = "aTag" href ="/test" > </a>
```

> Script

```
aTag.href
print :: "file:///test"
aTag.getAttribute("href")
print :: "/test"
```

> JQuery의 prop / attr 를 사용해본 사람이라면, 겪어봤을 문제입니다.

결과값이 다른 이유는 DOM이 생성될때 내부적으로 값을 저장하는 방식이 달라 발생한다고 추측됩니다.


#### etc

책에서 짧게 언급되는 내용인데 `form 의 name / id 값`이 겹칠 경우 이상하게 동작하게 됩니다.

```
<form id ="forms" action="/">
  <input name = "id">
  <input name ="action">
</form>
```

위 같은 상황에서

```
var forms = document.getElementById("forms");
forms.id
forms.getAttribute("id");
```

위 처럼 코드를 작성할 경우, form 내부에서 name 으로 선언된 Node를 가져오게 됩니다.
때문에 `<form>`의 id 값이 아닌 `<input name ="id">`를 가져오게 됩니다.

> 이 부분은 생각도 못한 .. 내용이네요


개인적으로는  위 같은 이유 (result / expression) 들로 attribute를 조금 더 선호합니다.

> 요 부분은 함께 이야기해봐요 :D


이것도 글정리해야함 ...
TODO 
