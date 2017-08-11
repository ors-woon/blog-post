---
layout: post
title: Decorator 패턴에 대하여
tags:  Java WebServer I/O
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

작성하려는 코드는 상품의 이름, 가격을 동적으로(?) 확장하여 출력하는 예제입니다.      

>  Decorator 패턴을 사용하려다보니, 설계가 올바르진않습니다.    
>  다소 이상합니다 ..... 

	public interface Product {
	    String getName();
	    Integer getPrice();
	}     

최상위 Interface인 Product는 getName과 getPrice 이라는 함수를 갖고있습니다.    
>  subClass들은 이 함수를 재정의할 의무를 갖고 있습니다.      


	public abstract class Beverage implements Product{
    	private String name;

	    public Beverage(String name){
	        this.name = name;
	    }
	
	    @Override
	    public String getName() {
	        return name;
	    }
	}
 
Beverage는 추상클래스로 상품명(name)을 인자로 받아 객체를 생성하는 기능을 합니다.     
>  그 외의 부가적인 기능은 subClass에게 위임(?)합니다.

	public abstract class AddtionalOption implements Product {
	
	    protected Product product;
	
	    public AddtionalOption(Product product) {
	        this.product = product;
	    }
	}        


AddtionalOption은 말 그대로 상품에 Option을 추가하는 역할을 합니다. 예제에서는 간단하게 Syrup만 추가하는 구조로 설계했습니다.

> 생성자로 Product 객체를 받아 추후 name 및 price를 추가하는 역할을 합니다.     

 

	public class Americano extends Beverage {
	    private int price = 3000;
	
	    public Americano(String name){
	        super(name);
	    }
	
	    @Override
	    public String getName() {
	        return super.getName()+ "아메리카노";
	    }
	
	    @Override
	    public Integer getPrice() {
	        return price;
	    }
	}

Americano는 제품명을 받아, 제품명을 반환하는 기능을 합니다. 이 과정에서 받은 인자를 통해 name을 확장하는데, **객체의 책임**이 덧붙여진다고 볼 수 있습니다.     


	public class KakaoSyrup extends AddtionalOption{

    	public KakaoSyrup(Product product) {
	        super(product);
	    }
	
	    @Override
	    public String getName() {
	        return " 카카오 "+product.getName();
	    }
	
	    @Override
	    public Integer getPrice() {
	        return 500+product.getPrice();
	    }
	}     

다음은 KakaoSyrup의 구현부 입니다. Americano와 마찬가지로 제품명을 확장하고, 가격까지 추가하는 역할을 수행합니다.     

	public class Temperature {

	    private String status;
	    private Product product;
	
	    public Temperature(Product product, String status) {
	        this.product = product;
	        this.status = status;
	    }
	
	    public String getName(){
	        return status + product.getName();
	    }
	    public Integer getPrice(){
	        return product.getPrice();
	    }
	} 
	      
마지막으로 Temperature 또한 위 클래스들과 동일한 동작을 수행합니다.     

이제 위 예시를 통해 객체를 생성하고, 책임을 덧붙여 보겠습니다.     

    public static void main(String[] args){
        Beverage beverage = new Americano("콜드블루");
        AddtionalOption addtionalOption = new KakaoSyrup(beverage);
        Temperature temperature = new Temperature(addtionalOption,"아이스");
        System.out.println("상품명 :: " + temperature.getName());
        System.out.println("가격 :: " + temperature.getPrice());
    }    

위 코드는 beverage 객체를 생성하고, 각 생성자들에게 넘겨 책임을 덧붙입니다.     

> 콜드블루 커피에 KakaoSyrup을 추가하고, 차갑게 만드는 과정입니다.   

	상품명 :: 아이스 카카오 콜드블루아메리카노   
	가격 :: 3500     

위 코드를 한줄로 줄이면 이렇게 되죠.

	Temperature temperature = new Temperature(new KakaoSyrup(new Americano("콜드블루")),"아이스");     


#### 예제 확장      

> 위 예제는 사실 동적으로 확장한다는 느낌이 나지않습니다.    

Main을 조금 바꿔보겠습니다.       


	public class Main {
	
	    public static void main(String[] args) {
	        Main main = new Main();
	        int choice = main.showMenu();
	        Product product = main.choiceProduct(choice);
	        choice = main.showSyrup();
	        Product option = main.choiceSyrup(product, choice);
	        choice = main.showTemperature();
	        Temperature temperature = main.choiceTemperature(option, choice);
	        System.out.println(temperature.getName());
	        System.out.println(temperature.getPrice());
	    }
	
	    public int showMenu() {
	        Scanner scanner = new Scanner(System.in);
	        System.out.println("메뉴를 선택해주세요.");
	        System.out.println("1. 콜드브루");
	        System.out.println("2. 더치커피");
	        return scanner.nextInt();
	    }
	
	    public int showSyrup() {
	        Scanner scanner = new Scanner(System.in);
	        System.out.println("시럽 선택해주세요.");
	        System.out.println("1. kakao");
	        System.out.println("2. another syrup");
	        return scanner.nextInt();
	    }
	
	    public int showTemperature() {
	        Scanner scanner = new Scanner(System.in);
	        System.out.println("온도 선택해주세요.");
	        System.out.println("1. ice");
	        System.out.println("2. hot");
	        return scanner.nextInt();
	    }
	
	    public Product choiceProduct(int choice) {
	        Product product = null;
	        switch (choice) {
	            case 1:
	                product = new Americano("콜드브루");
	                break;
	            case 2:
	                product = new Americano("더치커피");
	                break;
	            default:
	                break;
	        }
	        return product;
	    }
	
	    public Product choiceSyrup(Product product, int choice) {
	        AddtionalOption option = null;
	        switch (choice) {
	            case 1:
	                option = new KakaoSyrup(product);
	                break;
	            case 2:
	                // 다른 시럽 추가
	                break;
	            default:
	                break;
	        }
	        return option;
	    }
	
	    public Temperature choiceTemperature(Product product, int choice) {
	        Temperature temperature = null;
	        switch (choice) {
	            case 1:
	                temperature = new Temperature(product, "아이스");
	                break;
	            case 2:
	                temperature = new Temperature(product, "따뜻한 ");
	                break;
	            default:
	                break;
	        }
	        return temperature;
	    }
	}
	 

위 예시는 사용자가 메뉴를 선택하면, 그에 맞춰 가격과 상품명이 정해집니다.  

	메뉴를 선택해주세요.
	1. 콜드브루
	2. 더치커피
	> 2
	시럽 선택해주세요.
	1. kakao
	2. another syrup
	> 1
	온도 선택해주세요.
	1. ice
	2. hot
	> 1
	아이스 카카오 더치커피아메리카노
	3500        

> 이렇게말이죠.   

 

#### Java의 I/O 구조    

	BufferedReader in = new BufferedReader(new InputStreamReader(clientSocket.getInputStream()));     


위 코드는 소켓의 inputStream을 가져와 InputStreamReader와 BufferedReader로 기능을 확장시키는 코드입니다.    

> 물론 Decorator 패턴입니다.   

위 코드들의 생성자 및 함수들의 정의부는 다음과 같습니다.   

	public InputStream getInputStream() throws IOException  // Socket의 함수
	public InputStreamReader(InputStream in) // 생성자
	public BufferedReader(Reader in) // 생성자     


위 코드만으론 각 객체들이 어떤 관계에 있는지 추측하기 어렵습니다.    
> 코드를 추척하면서 간단하게 관계를 알아보죠.        
> InputStream과 InputStreamReader/BufferedReader의 상속 구조는 다음과 같습니다.     
   
<br>

<img src ="/public/img/inputReaderStructure.png"/>

<br>

1. BufferedReader 와 InputStreamReader는 추상 클래스인 Reader 를 상속받습니다.    
2. Reader는 Closeable interface를 구현합니다. 
3. InputStream 또한 Closeable interface를 구현합니다.         

(+) BufferedReader 와 InputStreamReader의 생성자는 Default 생성자를 갖고 있지않고, 필수적으로 인자를 받아 진행합니다.

> 즉, 강제적으로 정해진 Type의 객체를 받아야만 사용이 가능하게 설계되어 있습니다.      




#### 정리    

보신바와 같이 Decorator패턴을 사용하면 객체의 책임 및 기능을 확장할 수 있다는 장점이 있습니다. 다만 가독성이 떨어진다는 단점이 존재합니다. 

> 개인적으로 이해하기도 어렵구요 ..     

또한, 요구사항이 늘어날수록 잡다한 Class들도 증가하게 되겠죠. 여러관점에서 사용하기는 어려운 패턴같습니다.     

> 그럼에도 동적으로 책임을 확장시킬 수 있다는 점이 큰 매력으로 다가오고, Java의 경우 File I/O가 Decorator 패턴으로 구현되어 있기에, 알아두면 좋은 패턴이라고 생각합니다.    


 
#### 마치며    

Decorator 패턴은 사용하기 위해 정리했다기 보다, I/O 부분을 이해하기 위해 정리한 내용입니다. 싱글톤이나 팩토리, 전략 패턴등 다양한 패턴들은 적용해볼 기회가 있었지만, Decorator은 아직 사용해 본적은 없습니다.     

> API 설계나 규모있는 프로젝트를 진행하다보면 적용해 볼 수 있겠죠 ?    

lusiue@gmail.com     
08-08~11



[자바 디자인 패턴 8 - Decorator](http://egloos.zum.com/iilii/v/3850836)         