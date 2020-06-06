+++ 
author = "chulwoon.jang" 
title = "Boxing 과 UnBoxing" 
date = "2016-12-09" 
description = "Java 의 Wrapper class에 대한 정리글" 
tags = ["Java"] 
series = ["Java"] 
categories = ["Java",] 
+++

Java에는 Primitive 라고 불리는 `원시 티입`과 Wrapper 라고 불리는 `Object Type`이 존재합니다.  
이번 포스트에서는 Wrapper, Primitive와 관련된 boxing과 unboxing, 나아가 Autoboxing에 대해 이야기할 예정입니다. 

#### Primitive type   

코드에서 흔히 볼 수 있는 `int , double, boolean, char` 등을 Primitive type, 다른 말로는 원시타입이라고 부릅니다.    

Java에서의 Primitive type은 비객체 타입이며, Null 값을 가질 수 없습니다.   

> NULL 은 "초기화되지 않은 상태"와는 다른 의미로, "객체가 아무것도 가르키고 있지 않다." 라는 의미로 사용됩니다.

```
// 초기화하지 않은 상태로는 사용할 수 없으며, 사용시 compile error 를 뱉게 됩니다.
int notDeclaredValue; 
// null 은 초기화된 상태로, 사용가능합니다.
Integer nullValue = null;
```

#### Wrapper Type   

Wrapper type, 즉 참조형은 java.lang.Object을 상속받은 자료형으로 String, Integer, Double 등이 여기에 해당됩니다.

> 앞서 설명했듯, Wrapper Type 은 null로 초기화 될 수 있습니다.

아래처럼 Primitive Type에는 각각 대응되는 Wrapper Type 이 존재하는데요.

<p align="center">
	<img src="/images/primitive-wrapper.png">
</p>

`문득..` primitve 와 Wrapper Type을 분리한 이유가 궁금해지지않나요?

#### [알쓸신잡] Boxing 과 UnBoxing   

Boxing은 간단하게 말해서 primitive 값을 Wrapper 값으로 변환하는 과정입니다. UnBoxing 은 Boxing과 반대로 Wrapper 값을 primitive 값으로 변환하는 과정이구요.   

```
	Integer integer = new Integer(5); // Boxing
	int a = integer.intValue(); //unBoxing
```

> jdk 1.5 이상부터는 위 예시처럼 Boxing / UnBoxing을 위한 메서드를 호출할 필요가 없습니다.

```
	Integer integer = 5; // AutoBoxing
	int a = integer //AutounBoxing
```


#### Primitive와 Wrapper class    

여기서 아까의 의문을 가져오겠습니다.      

> primitvie와 Wrapper type의 차이점은 무엇이고, 왜 구분해 두었는가?

사실 답은 위에서 이미 언급된 내용입니다. 바로 primitive가 Object type이 아니라는 점이죠.   

Java의 API를 보다보면 다음과 같은 Method를 많이 볼 수 있습니다.    

```
	boolean contains(Object o);
```

대부분의 Collection에서 지원하는 Method 중 하나인데, 눈에 띄는것은 인자의 형태가 Object라는 점입니다. Java 언어에서 Object는 참조형의 최상위 클래스입니다.   

즉, 저 함수의 인자로 모든 primitive type이 들어갈 수 있다는 의미입니다.      
 
> 객체의 다형성과 연결되는 이야기입니다.

만약 API를 디자인할때, Primitive type이 쓰일 경우 int, double, boolean 등 인자 마다 Method를 정의해야합니다.   
	
```
	boolean contains(int a);
	boolean contains(double b);
```

반면에, Wrapper class를 만들어 두고 인자형을 Object로 사용한다면
다음과 같이 하나의 메소드만으로 모든 Wrapper type을 받아 처리할 수 있습니다.    

이러한 이유때문에 Java 언어에서는 Wrapper class를 지원하고 있습니다.

> Q. 그럼 Wrapper만 쓰면되지않나 ? 

Wrapper Type 만으로 개발을 하게되면, 크게 2가지 정도의 문제점에 봉착하게 됩니다.

```
1. equals 연산을 사용해야한다.
2. Null check 를 해야한다.
```

> todo string == 연산과 Null check 코드    

#### [알쓸신잡] Integer Caching

Integer는 Wrapper Type이지만, == 을 사용할 수 있는 범위가 존재합니다. `-128 ~ 127` 까지의 범위에서는 사용이 가능한데, 그 이유는 Integer 내부에서 IntegerCache 를 통해 일정 범위를 저장해두기 때문입니다.

```
	private static class IntegerCache {  
	 	//Integer을 까보시면 정의되어있습니다.
	} 
```
     
> Integer class는 -128~ 127의 범위를 cache로 저장해둔다.    
> 때문에 해당 범위 내에선 == 를 사용 가능하다.   

jdk 1.5 에서부턴 AutoBoxing이 지원되어, 큰 불편함이 없지만   
알아두면 좋을 것같아서 정리해봤습니다. ! 

#### [알쓸신잡] Java Generic 에서의 차이  

// todo



참조   
[1] [Java doc](https://docs.oracle.com/javase/8/docs/api/)     
[2] [[JAVA] Wrapper class 란? 그리고 AutoBoxing](http://hyeonstorage.tistory.com/168)     
[3] [Why we need wrapper class [duplicate]](http://stackoverflow.com/questions/20697868/why-we-need-wrapper-class)    
[4] [자바에서 primitive type과 래퍼 클래스 중 무엇을 사용하나요?](https://slipp.net/questions/66)


 


