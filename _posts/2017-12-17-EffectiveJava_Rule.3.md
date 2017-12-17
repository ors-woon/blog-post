---
layout: post
title:  Effective Java Rule.3  
tags: Effective Study 
categories: Effective
---   


#### Rule. 3 private 생성자 및 enum자료형은 싱글턴 패턴을 따르도록 설계하라.    

개발을 하다보면 필요에 따라 Singleton Pattern을 사용해야만 하는 경우가 있습니다. 

싱글톤을 생성하는 방법에는 크게 2가지 방법이 존재하는데, 다음과 같습니다.   

	1. public 변수로 객체를 생성해두고 접근
	2. 정적 팩터리 메서드 사용    

	// 1
	public ClassName{
		public static final ClASS_NAME = new ClassName():
		private ClassName(){}
		...
	}

	// 2  

	public ClassName{
		private static final ClASS_NAME = new ClassName():
		private ClassName(){}
		public static final getInstance(){ return ClASS_NAME}
	}


문제(?)는 위 방법 둘다 Reflection을 이용하면 Singleton이 깨진다는 점입니다.  

> 사실 그렇게 악의적으로 사용할 이유가 있나 싶긴한데, 해킹쪽은 공부를 해본적이 없어서 잘 모르겠습니다.     

어쩃든, 책에서는 이 점을 막기위해 생성 시, 예외처리로 막는다거나 하는 방법을 사용하라고합니다.   

첫번째 case의 장점은 `가독성`에 있습니다. final static 으로 선언된 부분만 봐도 Singleton이라는걸 알 수 있기 때문이죠.   

두번째 case의 장점은 API를 변경하지 않고도 싱글턴 패턴을 포기할 수 있다고 합니다.   
> 아직 이해하진 못했는데, 규칙 27 부분에서 설명할 제네릭 타입을 수용하기 쉽다고 합니다.    

이 부분은 Field 와 Method의 변경에 대한 차이라고 생각합니다.   
Field의 경우 변경이 일어났을 때, **Side Effect**가 발생할 확률이 높지만, Method의 경우 올바른 값을 리턴한다는 가정하에 내부 알고리즘이 바꿔어도 **Side Effect**가 발생할 확률은 낮다고 생각하기 때문입니다.    


또한 두가지 방법을 비교해 봤을 때, " 어떤게 더 성능이 좋은가 ? "에 대한 고민을 해볼 수 있습니다.   

개인적으로 봣을 때, 당연히 첫번째 경우가 더 메모리를 적게 먹을 것 같습니다. 함수를 호출 하지 않기 때문이죠.  일반적으로 2번쨰 방법은 함수를 호출해야하기에 추가적인 함수 스택을 요구하며, 이는 앞선 경우에 비해 Overhead가 존재한다고 할 수 있습니다.    

하지만 예상과는 다르게, 둘은 성능차이가 없습니다.  그 이유는 JVM은 정적 팩터리 메서드 호출을 거의 Inline으로 처리하기 때문입니다.    

[about inline the call from effective_java book](https://stackoverflow.com/questions/47384768/about-inline-the-call-from-effective-java-book)    


Singleton을 구현하는 두가지 Case에 대해서 상황에 맞춰 선택하면 될 것 같습니다.   


다만 위 두가지 방식은 `직렬화`관점에서 좋지 못한 코드입니다.    

기본적으로 Java class에 Serializeable 이라는 interface를 추가하면, JVM 내에서 직렬화를 진행하는데, Singleton의 경우 이와 같은 방법이 통하지않습니다.   
직렬화 후 역직렬화 할때 새로운 객체가 생성되기 때문이죠.    
그래서 transient keyworld를 모든 field에 붙이고 readResolve라는 Method를 제공하여 이와같은 문제를 해결할 수 있습니다.    

> 직렬화 과정에 대해서는 토론 및 별도의 학습을 해야할 것 같습니다.    

	Q. 왜 역직렬화시에 객체가 생성되는가 ?   

이와같은 문제를 해결하기위해 enum 방식을 통해 진행한다고 한다.    


#### LazyHolder   

이 방식이 가장 좋은 방식이라고 한다. Thread-Safe 하며 private 기법도 막을 수 있음.


	public class Viewer {
		
		public static getInstnace{
			return Singletone.INSTANCE;
		}
		private static class Products{
	   		private static final Viewer INSTANCE = new Viewer();
		}
	}


Viewer라는 Class 내부에는 Products type의 변수가 없기때문에 초기에 로딩되지 않으며, getInstnace라는 함수를 호출했을 때 비로소 로딩된다.   

> 로딩 동작 방식은 함께 토론해 봅시다 .    


2017 .12 .17    
lusiue@gmail.com   
   