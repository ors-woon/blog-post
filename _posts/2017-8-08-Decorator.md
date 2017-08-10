---
layout: post
title: Decorator 패턴에 대하여
tags:  Java WebServer
categories:  WebServer제작
---       

#### 개요     

시간이 날때마다, 간단한 Java를 이용하여 웹서버를 구현해 보려합니다. 교육 중에 과제로 진행된 부분이기도하고, 개인적으로 웹서버를 구현한다는 것이 큰 도전(?)으로 다가와, 한 단계씩 진행하며 블로그에 글을 남겨놓으려 합니다.     

단순히 개발만 진행하기에는 조금 아쉬울 것같아서 한 단계씩 글을 작성해 보겠습니다.     

	
	BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));      

> Server를 만들면서 위같은 I/O 관련 코드를 자주 사용했습니다.    
> 때문에 I/O와 관련된 Decorator패턴을 정리해보려 합니다.    

해당 글은 Decorator의 개념을 이해하기 위한 포스팅으로, 간략하게 정리를 했습니다.   

#### Decorator 패턴       

Decorator pattern의 개념은 다음과 같습니다.     

	데코레이터 패턴(Decorator pattern)이란 
	주어진 상황 및 용도에 따라 어떤 객체에 책임을 덧붙이는 패턴으로, 
	기능 확장이 필요할 때 서브클래싱 대신 쓸 수 있는 유연한 대안이 될 수 있다.       
	[위키백과]

한마디로 요약하자면, 다음과 같을것 같습니다.    

>  " 객체에 책임을 덧붙이는 패턴 "      

#### Coffee Decorator 

다음은 Decorator 패턴을 적용한 간단한 예제입니다.       

> 지금 카페에서 커피를 마시고있으니 커피를 이용한 예제를 만들어보죠.     

구조는 다음과 같습니다.        

<img src = "/public/img/Decorator.png">      

작성하려는 코드는 생성자를 통해 객체를 넘겨받고, 기능을 확장(?)하는 동작을 하는 코드입니다.     

> Beverage가 최상위 Interface인 구조입니다.   


	public interface Beverage {
    	String getName();
	}


	public abstract class AdditionalOption implements Beverage {
	    protected Beverage beverage;
	
	    public AdditionalOption(Beverage beverage){
	        this.beverage = beverage;
	    }
	
	    @Override
	    public abstract String getName();
	}  


	// Coffee.java
	public class Coffee implements Beverage{
	    private String name;
	
	    public Coffee(String name) {
	        this.name = name;
	    }
	
	    @Override
	    public String getName() {
	        return name;
	    }
	}

	// Syrup.java
	public class Syrup extends AdditionalOption {
	    private String type;
	
	    public Syrup(Beverage beverage,String type) {
	        super(beverage);
	        this.type = type;
	    }
	
	    @Override
	    public String getName() {
	        return type+beverage.getName();
	    }
	
	}

	// Temperature.java
	public class Temperature extends AdditionalOption{
	    private String type;
	
	    public Temperature(Beverage beverage,String type) {
	        super(beverage);
	        this.type = type;
	    }
	
	
	    @Override
	    public String getName() {
	        return type + beverage.getName();
	    }
	}

> Temperature과 Syrup의 getName()에서 객체의 책임이 덧붙여집니다.   
  
1. Coffee는 Beverage를 구현한 클래스로 객체 생성시 name을 초기화 합니다. 그 후, getName이라는 Method로 초기화한 name을 호출할 수 있습니다.    
2. Temperature와 Syrup은 Beverage type의 객체와 type이라는 변수를 받아 객체를 초기화합니다.     
3. 그후, Beverage의 name과 type을 더해 String을 반환하는 getName()이라는 Method를 갖고 있습니다.  이 과정에서 **객체의 책임**이 덧붙여진다고 볼 수 있습니다.   


위 코드를 실행시키는 Main은 다음과 같습니다.    

    public static void main(String args[]){
        Beverage beverage = new Coffee("아메리카노");
        AdditionalOption additionalOption = new Syrup(beverage,"코코넛 ");
        AdditionalOption additionalOption2 = new Temperature(additionalOption, "아이스 ");
        System.out.println(additionalOption2.getName());
    }    

> 결과 >  아이스 코코넛 아메리카노     

위 코드를 한줄로 줄이면 이렇게 되죠.

	AdditionalOption additionalOption = new Temperature(new Syrup(new Coffee("아메리카노"),"코코넛 "), "아이스 ");

보신바와 같이 Decorator패턴을 사용하면 객체의 책임 및 기능을 확장할 수 있다는 장점이 있습니다. 다만 가독성이 떨어진다는 단점이 존재합니다. 

> 개인적으로 이해하기도 어렵구요 ..     

또한, 요구사항이 늘어날수록 잡다한 Class들도 증가하게 되겠죠. 여러관점에서 사용하기는 어려운 패턴같습니다.     

> 그럼에도 동적으로 책임을 확장시킬 수 있다는 점이 큰 매력으로 다가옵니다.    


[ +덧 ] 

Decorator 패턴을 공부하면서, 연관된 Class 들이 모두 공통된 Interface 또는 class를 상속 받아야 한다고 생각했습니다. 그래서 위 예제가 Beverage Interface를 모두 상속 받고 있죠.      
하지만  제가 알던 것과는 달리, Decorator 패턴에서 필수적으로 공통된 Class/Interface를 구현할 필요는 없습니다.       

> Decorator 패턴에서 공통된 Class/Interface를 구현해야할 의무는 없습니다.     
      

#### Java의 I/O 구조    

	BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));     


위 코드는 소켓의 inputStream을 가져와 InputStreamReader와 BufferedReader로 기능을 확장시키는 코드입니다.    

> 물론 Decorator 패턴입니다.   

위 코드들의 생성자 및 함수들의 정의부는 다음과 같습니다.   

	public InputStream getInputStream() throws IOException  // Socket의 함수
	public InputStreamReader(InputStream in) // 생성자
	public BufferedReader(Reader in) // 생성자     


위 코드만으론 각 객체들이 어떤 관계에 있는지 추측하기 어렵습니다. 코드를 추척하면서 관계를 알아보죠.        

> InputStream과 InputStreamReader/BufferedReader의 상속 구조는 다음과 같습니다.     
   
<br>

<img src ="/public/img/inputReaderStructure.png"/>

<br>

1. BufferedReader 와 InputStreamReader는 추상 클래스인 Reader 를 상속받습니다.    
2. Reader는 Closeable interface를 구현합니다. 
3. InputStream 또한 Closeable interface를 구현합니다.         

(+) BufferedReader 와 InputStreamReader의 생성자는 Default 생성자를 갖고 있지않고, 필수적으로 인자를 받아 진행합니다.

(추가적으로 코드를 더 작성해볼까 ? )  




#### 마치며    

Decorator 패턴은 사용하기 위해 정리했다기 보다, I/O 부분을 이해하기 위해 정리한 내용입니다. 싱글톤이나 팩토리, 전략 패턴등 다양한 패턴들은 적용해볼 기회가 있었지만, Decorator은 아직 사용해 본적은 없습니다.     

> API 설계나 규모있는 프로젝트를 진행하다보면 적용해 볼 수 있겠죠 ?    

lusiue@gmail.com     
08-08





[자바 디자인 패턴 8 - Decorator](http://egloos.zum.com/iilii/v/3850836)       