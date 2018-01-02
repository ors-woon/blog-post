---
layout: post
title:  Effective Java Rule.5  
tags: Effective Study 
categories: Effective
---   


#### Rule.5 불필요한 객체는 만들지마라     

책에서는 기능적으로 동일한 객체를 만드는 것보다 재사용하는게 더 빠르고 더 우아하다 라고 표현합니다. 특히 Immutable 객체의 경우 언제나 재사용 할 수 있다고 언급해 줍니다. 

> 해당 부분은 Rule 15에서 나온다고 하네요    

대표적인 Immutable 객체인 String을 예로 안티패턴을 보여줍니다.  

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

#### 면접 질문   

	String str = "";
	String str = new String("");

`위 코드들의 차이점이 뭔지 아시나요 ?` 라는 질문을 받았었습니다. 

	한쪽은 일반적인 Heap 영역에 들어가며, 
	한쪽은  String Pool 영역에 들어갑니다.

> 어디서 주워들은건 있어갖고 꽤 당당히 말햇던 기억이 있습니다.   

[[Java/Tip] String.intern()은 메모리를 아낄 수 있다?](http://blog.ggaman.com/918)여기서 주워들은거 같은데 추후 정리를 해도록 하겠습니다.  

https://androidexample.com/Strings_In_Java/example.php?view=description&aid=149&article=163&mcat=2

http://www.baeldung.com/java-string-pool

> 경량패턴의 일종이라던데 .. 

다시 책 내용으로 돌아와서,  String 뿐만 아니라 1장에서 봤던 Boolean.valueOf()또한 객체를 재사용한다는 점에서 비슷한 패턴이라 생각하시면 됩니다.

또한 Immutable 객체 외에 `static final`로 선언된 변수 또는 `static final` 취급을 받는 변수의 경우 전역변수로 한번만 초기화 후 사용하라는 말도 해줍니다. 

> 예시 ? 

또한 초기화 지연은 좋지만 코드가 복잡해지므로 하지 말라는 말도 ..

HashMap의 Keyset 또한 꽤 유용한 코드  

AutoBoxing 하지 말것 .

객체풀은 앵간하면 쓰지 말것 


12.29      
lusiue@gmail.com
