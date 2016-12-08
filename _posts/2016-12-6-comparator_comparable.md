---
layout: post
title:  "[Java] Wrapper Class 와 Boxing,UnBoxing"
date:   2016-12-8 10:32:00 +0200
tags: ['Java', 'Wrapper', 'Boxing', 'UnBoxing']
author: "Jang chulwoon"
---


Wrapper class 와 연결된 boxing과 unboxing, 나아가 Autoboxing, 자료형에 대해 정리해볼까 합니다.   


# primitive type   

primitive type, 다른말로는 원시타입이라고 불립니다.   
Java는 여러가지 원시타입을 갖고있는데, 많이 사용하는 int , double, boolean, char 등이 여기에 해당합니다.      

> 원시타입은 비객체 타입이며, 따라서 Null 값을 가질 수 없습니다.    
> (int a = null; 은 사용될 수 없습니다.)

중요한 건 원시타입은 단순히 data이기때문에 Null값을 갖을 수 없다는 것입니다.    
(Null은 '객체가 아무것도 가르키고 있지 않다'의 표현이기에 원시타입은 갖을 수 없는 값입니다. )
    
>  원시형은 object가 아닙니다       
  

# Reference Type   

Reference type, 즉 참조형은 java.lang.Object을 상속 받고,   
많이 사용하는 String,List등이 참조형에 해당됩니다.      
또한 Primitive type인 int , double 등의 wrapper class인 Integer , Double 등이 여기에 해당합니다.   

> Java 내에서 원시형이 아닌 자료형은 Reference type으로 생각하면됩니다.    

원시형과 참조형을 읽다보면 의문점이 한가지 생깁니다.    
원시형의 int 와 참조형의 Integer의 차이점은 무엇이고 , 왜 분리해 두었을까 ?  
이 내용을 알아보기전에  Boxing 에 대해 정리하겠습니다.   
 
# Boxing 과 UnBoxing   

Boxing은 간단하게 말해서 int 값을 Integer 값으로 변환하는 과정입니다.   
UnBoxing 은 Boxing과 반대로 Interger 값을 int 값으로 변환하는 과정이구요.    

즉, 원시형의 값을 wrapper class값으로 바꾸는걸 boxing,     
wrapper class의 값을 원시형으로 바꾸는걸 unboxing이라고합니다.    

 
	Integer integer = new Integer(5); // Boxing

	int a = integer.intValue(); //unBoxing

또한 위 예시처럼    
intValue() 및 doubleValue() 같이 unBoxing을 위해 method를 호출 할 필요없이,     
jdk 1.5 이상부터는 AutoBox 및 AutounBoxing을 지원하여 다음과 같이 사용해도 됩니다.    


 
	Integer integer = 5; // AutoBoxing

	int a = integer //AutounBoxing




여기서 아까의 의문을 가져오겠습니다.    

> 원시형의 int 와 참조형의 Integer의 차이점은 무엇이고 , 왜 분리해 두었을까 ?     

사실 답은 위에서 이미 언급된 내용입니다.   
바로 원시형은 Object가 아니라는 점이죠.   

Java의 API를 보다보면 다음과 같은 Method를 많이 볼 수 있습니다.    


	boolean contains(Object o);


LinkedList Method 중 하나인데, 눈에 띄는것은 인자의 형태가 Object라는 점입니다.   
Java 언어에서 Object는 참조형의 최상위 클래스입니다.   
즉, 저 함수의 인자로 모든 참조형이 들어갈 수 있다는 의미입니다.      
 
(다형성과 연결되는 부분이네요. )   
만약 API를 디자인할때, 원시형이 쓰일 경우 int, double, boolean 등 인자 마다 Method를 정의해야합니다.   
반면에,  Wrapper class를 만들어 두고 인자형을 Object로 사용한다면
다음과 같이 하나의 메소드만으로 모든 참조형을 받아 처리할 수 있습니다.    

이러한 이유때문에 Java 언어에서는 Wrapper class를 지원한다고 합니다.   

재밌네요!   





참조   
[1] [Java doc]('https://docs.oracle.com/javase/8/docs/api/')     
[2] [[JAVA] Wrapper class 란? 그리고 AutoBoxing](`http://hyeonstorage.tistory.com/168`)     
[3] [Why we need wrapper class [duplicate]]('http://stackoverflow.com/questions/20697868/why-we-need-wrapper-class')



 
lusiue@gmail.com    
2016-12-8 장철운. 


