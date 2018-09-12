---
layout: post
title:  Reflection에 대하여
tags: Java 
categories: Java
---    

> 스프링을 사용하면서 리플렉션이 뭔지 모른다면 문제 있는거죠     

우연히 JAVA 리플렉션 이라는 게시글을 보고 정보를 찾던 도중 본 글 입니다 ...    

저는 문제가 있었고 .. 문제를 없애기 위해 관련 개념을 정리해보려 합니다.       


#### JAVA Reflection     

Reflection의 정의는 다음과 같습니다.    

> 구체적인 Class Type을 알지 못해도, 그 클래스의 메서드, 변수들에 접근 할 수 있도록 해주는 JAVA API       

제가 지금까지 알고 있던 JAVA에서는 구체적인 클래스 타입을 알지 못하면 메서드를 실행 할 수 없었습니다. 사용할 수 있는 메서드는 Object의 기본 메서드뿐 이였죠.   

	Class Person{
		public void drink(){
			System.out.println("Drink !! ");
		}
	} 

	public Class Main{

		public static void main(String[] args){
			Object person = new Person();
			person.drink();
		}
	}


위 코드는 당연히(?) 컴파일 에러를 뱉습니다. 선언된 person의 Type은 Object이기 때문에 해당 메서드만 사용가능하기 때문입니다.    

> 이와같이 구체적인 타입을 모를때 사용하는게 Reflection이라고 합니다.       

그럼 이와같은 기술이  어떻게 가능한 걸까요 ?    

#### 원리        

원리를 이해하기위해 JAVA의 메모리 구조를 알고 있어야합니다. 

> 관련 부분은 조금 더 깊게 공부해야하기에 .. 여기서는 간단히 넘어가겠습니다.    

JAVA에는 GC의 대상이 되는 Heap 메모리와, GC의 대상이 되지 않는 Method Area가 존재합니다. 여기서 눈여겨봐야 할 부분은 **Method Area**입니다.      
Method Area에는 Static 변수들을 비롯한, 생성자 , Method, SuperClass등 의 정보가 올라가게됩니다.  Reflection은 바로 이 영역을 뒤져서 클래스에 대한 정보를 가져온다고 합니다.      

> 관련 부분을 보다가 알게된 사실은 Heap 영역에는 객체의 Method를 제외한 정보 - Field값이 올라가고, Method 정보는 Method Area에 저장된다고 합니다.      


Reflection의 예시는 DB Connection을 맺는 부분에서 쉽게 찾아 볼 수 있습니다.     

    
     Class.forName("oracle.jdbc.driver.OracleDriver");
  

위 코드 역시 리플렉션의 예시로, Static 영역에 있는 'oracle.jdbc.driver.OracleDriver'를 찾아 DriverManager에 연결 시키는 동작을 합니다.   

#### 추가     

별도의 이야기이지만, Java에서는 DB 연결을 위해 Interface를 사용합니다. DB Vendor들이 해당 인터페이스를 구현해서 기능을 정의해두면, 사용자는 해당 Driver를 명시하는 것만으로 사용 가능합니다.     

> 즉, 여러 업체들이 하나의 Interface를 이용하기에 , 사용자는 인터페이스를 명시함으로 제품을 자유롭게 사용 가능합니다.     

mysql은 이렇게 사용 가능합니다.

	Class.forName("com.mysql.jdbc.Driver");
   


Class.forName 은 단순히 메모리 영역을 가져오기에, Object를 받고 싶다면 .newInstance()를 사용해야 된다고합니다.     

> Reflection을 프로젝트에 적용해보려 했지만 마땅한 예시가 없어 적용해보진 못했습니다. 
> 추후, 사용하게 된다면 다시 정리해보도록 하겠습니다.    





(+)  
Spring의 의존성 주입시 Reflection을 사용한다고 하네요. ! 

2017-09-11      
lusiue@gmail.com        
      
     

[자바의 리플렉션](https://brunch.co.kr/@kd4/8)      
