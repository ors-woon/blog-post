---
layout: post
title: Decorator 패턴에 대하여
tags:  Java WebServer
categories:  WebServer제작
---       

#### 개요     

시간이 날때마다, 간단한 Java를 이용하여 웹서버를 구현해 보려합니다. 교육 중에 과제로 진행된 부분이기도하고, 개인적으로 웹서버를 구현한다는 것이 큰 도전(?)으로 다가와, 한 단계씩 진행하며 블로그에 글을 남겨놓으려 합니다.     

단순히 개발만 진행하기에는 조금 아쉬울 것같아서 한 단계씩 글을 작성해 보겠습니다.     


> 오늘은 Java I/O와 연관깊은 Decorator 패턴을 정리하겠습니다.   



#### Decorator 패턴       

Decorator pattern의 개념은 다음과 같습니다.     

	데코레이터 패턴(Decorator pattern)이란 
	주어진 상황 및 용도에 따라 어떤 객체에 책임을 덧붙이는 패턴으로, 
	기능 확장이 필요할 때 서브클래싱 대신 쓸 수 있는 유연한 대안이 될 수 있다.       
	[위키백과]

한마디로 요약하자면, 다음과 같을것 같습니다.    

>  " 객체에 책임을 덧붙이는 패턴 "    

Decorator 패턴을 적용한 간단한 예제를 만들겠습니다. 지금 카페에서 커피를 마시고있으니 커피를 이용한 예제를 만들어보죠. 구조는 다음과 같습니다.    

<img src = "./Decorator.png">      

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

> 다음과 같이 코드를 작성한 이유는 Beverage 객체에 동적으로 String을 추가하기 위함입니다.   

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


  
간단히 설명하자면, Coffee는 Beverage를 구현한 클래스로 객체 생성시 name을 초기화 합니다. 그 후, getName이라는 Method로 초기화한 name을 호출할 수 있습니다.    

Temperature와 Syrup은 Beverage type의 객체와 type이라는 변수를 받아 객체를 초기화하고, Beverage의 name과 type을 더해 String을 반환하는 getName()이라는 Method를 갖고 있습니다.      

위같이 코드를 작성한 이유는 Beverage 객체를 생성 후, 인자로 넘겨주면서 String을 추가하기 위함입니다.    

위 코드를 실행시키는 Main은 다음과 같습니다.    

    public static void main(String args[]){
        Beverage beverage = new Coffee("아메리카노");
        AdditionalOption additionalOption = new Syrup(beverage,"코코넛 ");
        AdditionalOption additionalOption2 = new Temperature(additionalOption, "아이스 ");
        System.out.println(additionalOption2.getName());
    }    

> 결과 >  아이스 코코넛 아메리카노     

초기에 beverage 객체를 생성하고, 그 객체를 AdditionalOption에 계속 넘기면서 문자열이 추가되는 동작을 하게됩니다. 위 코드를 한줄로 줄이면 이렇게 되죠.

	AdditionalOption additionalOption = new Temperature(new Syrup(new Coffee("아메리카노"),"코코넛 "), "아이스 ");

Decorator패턴은 객체의 책임/기능을 확장시킬수 있다는 장점이 있지만, 위같이 사용될 경우 가독성이 떨어진다는 단점이 존재합니다. 코드를 설계한 저도 내일 당장 위 코드를 바로 이해할 순 없을겁니다. 또 요구사항이 늘어날 수록 잡다한 클래스들 또한 함께 증가한다는 단점이 존재합니다.    

> 그럼에도 동적으로 책임을 확장시킬 수 있다는 점이 큰 장점으로 다가옵니다.    

또하나의 대표적인 예로 Java I/O를 예시로 들 수 있습니다.  



	 BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));      

위 코드는 소켓의 inputStream을 가져와 InputStreamReader와 BufferedReader로 기능을 확장시키는 코드입니다. 간단하게 코드를 까(?)보겠습니다.      

	// clientSocket.getInputStream()
	public InputStream getInputStream() throws IOException; 
    
	// InputStreamReader의 생성자
	public InputStreamReader(InputStream in);      

	// BufferedReader의 생성자.    
	public BufferedReader(Reader in);     

간략하게 보자면, getInputStream()은 InputStream type을 반환하고, InputStreamReader()함수가 그걸 받아 객체를 생성,  또 다시 BufferedReader가 그걸 받아 최종적으로 객체를 생성하는 구조입니다.    

추가적으로 BufferedReader 와 InputStreamReader는 추상 클래스인 Reader 를 상속받고, Reader는 Closeable interface를 구현합니다. InputStream 또한 Closeable interface를 구현합니다.

	new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));        

결국 위 3개의 객체 모두 Closeable interface를 구현한 객체들로, 이를 이용하여, decorator 패턴을 사용했습니다.  

    

#### 마치며    

Decorator 패턴은 사용하기 위해 정리했다기 보다, I/O 부분을 이해하기 위해 정리한 내용입니다. 싱글톤이나 팩토리, 전략 패턴등 다양한 패턴들은 적용해볼 기회가 있었지만, Decorator은 아직 사용해 본적은 없습니다.     

> API 설계나 규모있는 프로젝트를 진행하다보면 적용해 볼 수 있겠죠 ?    

lusiue@gmail.com     
08-08~ 작성중     



[자바 디자인 패턴 8 - Decorator](http://egloos.zum.com/iilii/v/3850836)       
  