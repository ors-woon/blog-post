---
layout: post
title:  Effective Java Rule.6  
tags: Effective Study 
categories: Effective
---   



#### Rule.6 유효기간 지난 객체는 폐기하라     

일반적으로 Stack 구현시, Refference를 사용하면 의도치 않게 GC가 작동하지 않을 수가 있습니다.  

	private int index = 0;
	private Integer[] arrays = new Integer[SIZE];

	public Integer pop(){
		return arrays[index--]; 
		// 이때 arrays[index]의 참조가 지속 될 경우 
		// 의도치 않은 참조가 살아있게된다. 
	}

이때의 문제점은 해당 Refference뿐만 아니라, Refference가 참조하는 모든 객체가 살아있게되고, 몇개만 쌓여도 메모리를 많이차지한다는 점입니다.  

> 이런 상황이 발생하는 이유를 만기참조를 제거하지 않아서라고 언급합니다.   
> 만기참조란 다시 이용되지 않을 참조(Refference) 입니다. 

다시 이용하지 않을 경우, 참조를 제거해야하는데 위 예시에서는 제거하지 않습니다.   
(의도치 않은 객체 보유)  

이런 문제의 경우 null을 적어주면 되는데, 

	public Integer pop(){
		Integer returnedValue = arrays[index];
		arrays[index--] = null;
		return returnedValue; 
	}

> 변수명이 이상해도 ..  

위와같이 작성하면 됩니다.  
다만 모든 코드에 null을 작성한다는건, 코드가 난잡해짐과 동시에 불편해집니다.  

> 책에서 이 방법을 규범이 아닌 예외적조치라고 합니다.    

만기참조에 대한 가장 좋은 처리방법은 " 해당 참조를 보관하는 변수가 유효범위를 벗어나게 두는 것 " 이라고합니다.   

개인적으로 이해하기를 ...      
참조를 보관하는 변수는 Stack 영역에 쌓이기 때문에 해당 부분이 쉽게 삭제 될수 있도록, 사용하기 직전에 선언하라.

> Java는 Block Scope이기에 사용하는 Block안에 변수를 저장해 둔다.    

라고 이해했습니다.   

( 이 부분은 스터디원들과 토론을 했었는데 다시보니까 이해가 된 .. )    

다시 돌아와서, 그럼 null 처리는 언제 이루어져야하는가를 살펴보면, 

	자체적으로 관리하는 메모리가 있는 Class를 만들때 

라고합니다.

즉 직접 구현한 Stack 처럼 자체적인 자료구조(?)를 구현하거나, Cache같은 Class를 구현할때 null 처리를 해야한다고 합니다.  

#### 메모리 누수    

보통 이와같은 메모리 누수는 Cache system에서 자주 발생합니다.    
Cache를 넣어두고, 사용하지 않은 부분들을 지워주지 않으면 GC가 발생하지 않기 때문입니다.   

이와같은 해결 방법으로는 Spring-scheduler 같은 별도의 Thread로 Cache를 관리하거나, Java의 WeakHashMap을 이용하면 편리하다고 합니다.    

> WeakHashMap은 한번도 안써본 ...

또한 Listener등의 역호출자에서도 발생한다고 합니다. 
( Servlet의 Listener 같은 건가 ? )    

#### 정리    

메모리 누수는 뚜렷한 `오류`가 아니기에 발견하기 어렵습니다. 때문에 코드를 작성할때 이를 고려해서 만들어야한다고 합니다. 


해당 내용들을 조금더 정리해봐야겠다 ... 


01.02   
lusiue@gmail.com    






