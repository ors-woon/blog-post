---
layout: post
title:  Effective Java Rule.5  
tags: Effective Study 
categories: Effective
---   


#### Rule.5 불필요한 객체는 만들지마라     

책에서는 기능적으로 동일한 객체를 만드는 것보다 재사용하는게 더 빠르고 더 우아하다 라고 표현합니다. 특히 Immutable 객체의 경우 언제나 재사용 할 수 있다고 말합니다. String 같이 한번 선언 후 바뀌지 않는 객체라면, Thread에 안전하며 여러번 사용 할 수 있다라고 생각됩니다. 

> 물론 잘못 사용할 경우 불필요한 메모리를 사용하게 될 수도 있겠죠 

대표적으로 String을 예로 안티패턴을 보여줍니다.  

	String str = new String("HelloWorld");

지극히 정상적(?)인 코드이지만 이는 분명한 안티 패턴입니다.  
만약 for문에서 해당코드를 실행할 경우, for문 만큼의 객체가 생성되고 이는 GC나 Memory 입장에서도 큰 부담이 됩니다.   

	for(int i =0; i<size; ++i){
		String str = new String(variable);
		doSomeThing();
	} 

이런 문제를 피하기 위해 사용해야하는 코드는 아래와 같습니다.

	String str = "HelloWorld";    

다른 자료형들처럼 `primitive Type`을 선언하는 방식으로 작성하면 됩니다. 

> String은 primitive Type은 없습니다   

학부생때는 `new String()`의 줄임말이 `= ""` 라고 배웠지만 위 코드의 동작방식은 꽤 다릅니다.    

#### 면접 질문 - String pool  

	String str = "";
	String str = new String("");

`위 코드들의 차이점이 뭔지 아시나요 ?` 라는 질문을 받았었습니다. 

	한쪽은 일반적인 Heap 영역에 들어가며, 
	한쪽은  String Pool 영역에 들어갑니다.

> 어디서 주워들은건 있어갖고 꽤 당당히 말햇던 기억이 있습니다.   

String은 별도의 pool, 속칭 String pool을 갖고 있습니다. 2개이상의 변수가 동일한 문자열을 갖게되면, 모두 같은 메모리를 가르키게됩니다. ~~말보단 코드가 편하겠죠~~


	String name1 = 'names';
	String name2 = 'names';
	String name3 = 'names';

	sout(name1.hashcode());
	sout(name2.hashcode());
	sout(name3.hashcode());
	+
	System.out.println(name1 == name2);

> outpout 

	99162322
	99162322
	99162322

	true

즉, 3개의 String은 모두 같은 값을 의미하며, 심지어 == 연산에 대해 true를 반환합니다. 
반대로 new 를 이용하여 선언하면 모두 다른 hashCode를 갖게되며 모두 배운것처럼 == 연산에 대해 false가 나오게 됩니다.

> 물론 hashCode가 같다고해서 모두다 같은 메모리를 가르키진 않습니다.  ( hashCode를 재정의 할 수 있으므로 ..)  

String을 new 로 생성하지 않으면 String Pool 이라는 공간에 저장되어집니다. 이때 동일한 값이 선언된다면 새로운 주소를 생성하는것이 아닌, 기존에 있는 메모리 공간을 가르키게됩니다. 
new를 이용해 선언된 String 또한 .intern()이라는 메서드를 이용해 같은 동작을 할 수 있습니다. 

> Intern 의 함정    

	모든 String을 Intern()으로 생성하고 사용하면 메모리 재사용성이 올라가게되고 (동일한 String에 대해) StringBuffer/Builder를 사용할 이유가 없겠네 !   

라고 생각할 수 있습니다. 

> 장점이 있으면 단점이 있는 법이죠  ... 

intern 메서드가 호출되면, 일차적으로 String pool의 각종 문자열과 equals연산을 진행합니다. 만약 같은게 없다면 object를 추가하고 있다면 같은걸 반환하죠.   

한마디로 String pool이 많아 질 수록 추가적인 시간이 소모됩니다.  

또한 new String() 후에 intern() 함수를 사용하는건 꽤나 비효율적이며, 지양해야 합니다.

> 그냥 String str = "" 이렇게 씁시다 ! 

여담이지만, StringPool에 들어가면 CG의 대상이 될 수도, 안될 수도 있다고합니다. JVM의 버전에 따라 달라진다고하네요. :D


http://www.baeldung.com/java-string-pool

> 경량패턴의 일종이라던데 .. 

#### Immutable or static final 변수들에 대해

다시 책 내용으로 돌아와서,  String 뿐만 아니라 1장에서 봤던 Boolean.valueOf()또한 객체를 재사용한다는 점에서 비슷한 패턴이라 생각하시면 됩니다.

또한 Immutable 객체 외에 `static final`로 선언된 변수 또는 `static final` 취급을 받는 변수의 경우 전역변수로 한번만 초기화 후 사용하라는 말도 해줍니다. 

	private exampleMethod(String input){
		String defaultStr = "hello";
		return defaultStr + input;
	}

> 정말 간단한 예시를 만들다보니 .. 코드가 이상할 수 있습니다 :( 

위예시에서 메서드를 호출할 때마다, defaultStr이 선언되며 메모리를 잡아먹게됩니다. 즉, 초기 값을 갖으며 값이 바뀔 일이 없을 경우 위 방식보단 static final을 이용해 전역변수로 분리해야합니다.  

	private static final String defaultStr = "";
	
	private exampleMethod(String input){
		return defaultStr + input;
	}

이런식으로 말이죠. 

> 위 코드에 문제가 더 있지만 .. 여기서 말하진 않겠습니다.   



#### 초기화 지연은 하지 말 것 

또한 초기화 지연은 좋지만 코드가 복잡해지므로 하지 말라는 말도 ..

#### HashMap   

HashMap의 Keyset 또한 꽤 유용한 코드  

#### Object Pool은 사용하지 말자  

객체풀은 앵간하면 쓰지 말것 


[[Java/Tip] String.intern()은 메모리를 아낄 수 있다?](http://blog.ggaman.com/918)


12.29      
lusiue@gmail.com
