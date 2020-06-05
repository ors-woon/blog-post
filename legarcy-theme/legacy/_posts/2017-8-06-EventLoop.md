---
layout: post
title: Event Loop에 대하여
tags:  Js EventLoop
categories:  Js
---


[Promise에 대하여](/js/2017/08/03/Promise/)라는 글을 작성하면서, 큰 삽질을 했습니다.

	var check1 = function(data){
		return  new Promise(function(resolve,reject){
			if(data !==0){
				setTimeout(function() {
	            	resolve(data*2);
	        	}, 2000);
			}
			reject("실패");
		});
	};


	check1(4).then(function(data){
		console.log(data);
	}, function(error){
		console.log(error);
	});

위 코드를 실행 시키면, 실패라는 글이 출력됩니다.

> 코드를 작성한 제 의도는, 8이 출력되는 것 입니다.

' 왜 그럴까 ? ' 라는 생각을 하다가, Event Loop 때문임을 알게됐고 해당 내용을 조금더 자세하게 공부해보려 합니다.

본 포스팅은 [자바스크립트와 이벤트 루프](http://meetup.toast.com/posts/89)을 보고 작성했습니다. 보다 더 자세한 내용은 다음을 참고하시면 될것 같습니다.

> 편의상 JavaScript를 Js라고 표기하겠습니다.

#### Js와 단일 스레드

기본적으로 Js는 단일 스레드 기반 언어로, 단일 호출 스택을 사용하고, 다른 프로그램 언어들처럼 요청이 들어올때마다 순차적으로 호출스택에 담아서 처리한다고 합니다.

> 하나의 스레드 만으로, 수많은 이벤트와 비동기처리를 동시에 수행 할 수 있을지 의문이 생깁니다.

Js는 어떻게 동시성을 지원할까요 ?
여기에 대한 답이 바로 **Event Loop** 입니다.

> 한가지 짚고 넘어가야할 부분은, Js가 단일 스레드로 동작한다는 말은 Js Engine이 단일 호출 스택을 사용한다는 의미입니다.

#### Browser 구조

<img src = "/legacy/public/img/browserStructure.png">

위 그림은 Js를 구동하는 Web Browser 의 구조입니다.  위에서 볼 수 있듯이, Js Engine 이외에도 API,Task Queue, Event Loop등 다양한 영역을 볼 수 있는데, 많이 사용하는 비동기 방식인 XMLHttpRequest는 Js Engine이 아닌, 별도의 Web API에 구현되어 있습니다.

> 많은 부분들이 Js에 내장되어 있는게 아닌, WebBrowser에 의해 구현됩니다.

기본적으로 Js를 구동하는 환경들은(Node 및 Browser 등) 멀티 스레드를 지원합니다.
멀티 스레드인 구동환경과 단일 스레드인 Js Engine을 연동하기 위해 추가된 개념이 Event Loop 입니다.

> Event Loop 나 Task Queue를 학습하기 전에 Js의 실행 방식을 알아보겠습니다.

#### Js의 실행 방식

Js의 함수가 실행되는 방식을 Run to Completion 이라고 말합니다. Js Engine에서 함수가 실행되면, 함수가 끝날 때 까지 다른 어떤 작업도 중간에 끼어 들 수 없다는 것을 뜻합니다.

> 즉 Call Stack 에 쌓여잇는 작업들이 모두 끝나기 전까지는 어떤 작업도 중간에 끼어들 수 없습니다.

#### Event Loop와 Task Queue

이제 초기 예제를 그대로 가져오겠습니다.


	var check1 = function(data){
		return  new Promise(function(resolve,reject){
			if(data !==0){
				setTimeout(function() {
	            	resolve(data*2);
	        	}, 2000);
			}
			reject("실패");
		});
	};


	check1(4).then(function(data){
		console.log(data);
	}, function(error){
		console.log(error);
	});

여기서 Chek1 의 Stack 구조를 간단하게 표현하자면, 다음과 같습니다.

<img  src = "/legacy/public/img/taskQueue.png">

그림을 간단히 설명하자면,

1. 넘겨받은 data가 0이 아니므로 setTimout을 실행시킵니다.
2. 다만 이때, setTimeout의 callback 함수는 시간이 지났다고 해서 바로 실행되지 않습니다.
(**Run to Completion**이 이때 작용합니다. )
3. resolve가 바로 실행되지 않았기에 함수는 계속 진행됩니다.
4. reject()를 호출합니다. 결국 check1(4)의 결과는 **Onrejected**를 부르게 됩니다.
5. Call Stack이 비어있을 때, 뒤늦게 resolve가 실행됩니다. 다만 이미 함수는 끝났으므로, 아무런 영향을 주지 못합니다.

위 과정 중 , 2번에서 setTimeout의 Callback 함수는 Task Queue라는 곳에 저장됩니다.
그곳에서 Call Stack에 아무것도 없을때 까지 대기하게 되죠.


> 여기서 알수 있는 점은 비동기 함수의 응답이 온다고해서, 바로 실행되지 않는다는 점과 Task Queue에 비동기의 Call back 함수가 쌓인다는 점입니다.

setTimeout를 포함한  비동기 함수들은 바로 실행되는 것이 아닌 Task Queue에서 대기하고, Call Stack이 비어있으면 실행됩니다.

이 과정에서 Call Stack이 비어있는지를 확인하고 (1), 비어있다면 Task Queue에 있는 Call back 함수를 실행(2)하는 역할을 **Event Loop**가 합니다.

정리하자면 Task Queue는 비동기의 Call back 함수들을 저장하는 역할을 수행하고, Event Loop는 Call Stack 과 Task Queue를 감시하여 Task Queue의 함수를 꺼내와 실행하는 역할을 합니다.


#### 비동기의 예외처리

ajax에서 예외처리가 힘든점도 Event Loop 및 Task Queue와 연관이 있습니다.



	try{
		$.ajax({
			success: function(){
				throw "error";
			}
		});
	}catch(exception){
		console.log(exception);
	}

위 코드의 경우 예상했던 에러처리를 하지 못합니다. 앞서 말한것 처럼 try안의 ajax 부분은 바로 실행되는것이 아닌 Task Queue에서 Call Stack이 비어있기를 기다립니다. 즉, try - catch문이 끝난 뒤에 ajax가 호출되고 throw를 뱉는 시점엔 이미 catch문은 반환되어 존재하지 않습니다.
만약 예외처리를 하고 싶다면 다음과 같이 코드를 작성해야합니다.

	$.ajax({
		success: function(){
			try{
				throw "error";
			}catch(exception){
				console.log(exception);
			}
		}
	});

만약 여러개의 ajax에 대해 예외처리를 해야한다면, 코드가 깔끔하진 않겠네요.

> Promise는 예외처리를 이쁘게(?) 제공합니다 .. !

#### 랜더링 엔진

	setTimeout(fn, 0)

많은 Js 코드에서 위와같은 코드를 볼 수 있다고합니다.

> 사실 저는 처음봤습니다 ...

만약 Event Loop에 대한 개념이 없다면 이해하기 힘든 코드입니다.

예를들어 특정 스크립트가 완료되기 전까지 화면을 검게만들고, 작업이 끝나면 원상태로 돌리는 script를 작성한다고 합시다.

	$(function(){

		$("body").css("background-color","black");
		for(var i =0; i <10000; ++i){
			console.log(1);
		}
		$("body").css("background-color","white");
	});

> 불과 얼마전 이와같이 작성한 기억이 있습니다.

위 코드를 돌려보면 DOM에는 아무런 변화가 없습니다. console에는 숫자가 찍히는데 말이죠.

이유는 UI rendering 또한 **Task Queue**에 저장되기 때문입니다.
즉, Call Stack이 비기 전까지는 rendering이 수행되지 않으며, Call Stack이 비는 순간 black / white 이벤트가 수행되기 때문에 사용자에게는 아무런 변화가 없는 것으로 보입니다.

이와같은 이유로 setTimeout(fn, 0) 을 사용합니다.

	$(function(){

		$("body").css("background-color","black"); // (1)
		setTimeout(function(){ // (2)
			for(var i =0; i <10000; ++i){
				console.log(1);
			}
			$("body").css("background-color","white");
		},0);

	});

즉, (2)를 Task Queue에 추가함으로, 먼저들어온 (1)이 수행된 후 , (2)가 수행됩니다. 위와같이 작성하면 원하는 결과를 볼 수 있습니다.

> 여기서 알아야할 점은 UI rendering 또한 Task Queue에 의해 관리된다는 점입니다.


#### MicroTask

Task Queue 이외에도  MicroTask Queue 라는 개념이 존재합니다. 말하기 앞서 간단한 예제로 정리하겠습니다.

	function check1(){
		return new Promise(function(resolve,reject){
			if(true){
				resolve("promise");
			}else{
				reject("false");
			}
		});
	}

	setTimeout(function() {
	    console.log('setTimeout');
	}, 0);

	check1().then(function(data) {
	    console.log(data);
	}).then(function(data) {
	    console.log("promise2");
	});

위에서 배운 내용대로라면 위 코드의 결과는

	setTimeout
	promise
	promise2

이처럼 나와야 합니다.
하지만 **chrome**에서의 결과는 조금 다르게 나옵니다.

	promise
	promise2
	setTimeout

이와같이 나오는 이유는 MicroTask 때문입니다. Promise는 기존의 Task Queue가 아닌 Micro Task Queue에 저장되는데, Micro Task Queue란 Task Queue보다 우선순위가 높은 Queue 라고합니다.

> 즉, Call Stack이 비었을때, Task Queue 보다 Micro Task Queue에서 먼저 call back 함수를 실행시킵니다.

때문에, Task Queue에 저장된 setTimeout 보다 promise가 먼저 실행되는 결과를 출력합니다.

> 위에서 chrome을 언급한 이유는, 브라우저마다 호출 순서가 조금씩 다를 수 있기 때문입니다.



조금 더 자세한 내용은 다음을 참조해 주세요.
[Difference between microtask and macrotask within an event loop context](https://stackoverflow.com/questions/25915634/difference-between-microtask-and-macrotask-within-an-event-loop-context)




#### 추가적으로

> 개발자가 직접 멀티스레드를 사용할 방법이 아예 없는건 아닙니다.
> HTML5 API에 나온 Web Worker를 이용하면 멀티 스레드를 구현 할 수 있다고 합니다.
> 엄밀히 말하면 이부분은 Js에서 지원하는 것이 아닌, Browser에서 지원하는 부분입니다.





#### 참고한 글

Event Loop에 대한 전반적인 글

[자바스크립트와 이벤트 루프](http://meetup.toast.com/posts/89)

microtask 와 관련된 부분

[Tasks, microtasks, queues and schedules](https://jakearchibald.com/2015/tasks-microtasks-queues-and-schedules/)
[Difference between microtask and macrotask within an event loop context](https://stackoverflow.com/questions/25915634/difference-between-microtask-and-macrotask-within-an-event-loop-context)


Promise를 정리하다가 삽질한 것때문에 Event Loop에 대해 생각하는 계기가 되었습니다.
언젠가 promise를 자유롭게(?) 사용할 수 있으면 좋겠습니다.

2017 - 08 - 06
lusiue@gmail.com
