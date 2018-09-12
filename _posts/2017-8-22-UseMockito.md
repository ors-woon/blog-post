---
layout: post
title: 02-Mockito사용방법
tags:  Test TDD
categories:  Test
---       


Mockito의 간단한 사용방법에 대해 기술합니다.    

> Mockito의 이론적인 부분은 내일(08.22) 작성하도록 하겠습니다.      
> 시간이 허락한다면(?) PowerMock도 정리를 ...          



## Mockito 초기화          



Mockito를 초기화하는 방법으로는 크게 2가지 방법이 존재합니다.    

> Annotation을 이용한 초기화       

	@Mock     
	private Object mockedObject;        

위와같이 사용할 경우, Test 함수 내부에 다음과 같이 코드를 작성해야 합니다.    

	@Test      
	public void mockTest(){
		MockitoAnnotations.initMocks(this);        
	}    

> 함수를 이용한 초기화      

위 같이 Annotation을 사용하지 않고도 Mockito에서 제공하는 함수로 초기화 할 수 있습니다.     

	Object obj = mock(Object.class);     


이렇게 생성된 mock들은 자신의 모든행동을 기억합니다. 

	mockObj.verify()    

위와같이 사용하면, 원하는 Method가 특정 조건으로 실행되었는지를 알 수 있습니다.  

**차이점이 있다면 기술해 둘것.**      

## Mockito 사용      

#### mockObj.verify()       

	@Test
    public void mockitoMethodTest() {
        // given
        ArrayList mockedList = mock(ArrayList.class);

        // when
        mockedList.add("JCW");
        mockedList.get(0);

        // then
        verify(mockedList).add("JCW");
        verify(mockedList).get(0);
    }    

> Unit Test는 given -> when -> then 형식으로 진행 됩니다. [00 TDD 와 Unit Test](wiki/00-TDD-와-Unit-Test)를 참조해주세요.        

위 방식에서 verify(mockedList).method()를 실행 함으로 해당 함수가 호출 됬는지를 알 수 있습니다. when / then 에서 사용되는 add 및 get의 인자가 서로 다를 경우, test는 통과되지 못하며, 함수가 2번 이상 호출되도 통과하지 못합니다.      

> verify는 기본적으로 한번만 호출되었는지를 판단합니다.         

즉, add("JCW") 가 2번 호출되면 위 Test는 완료되지 못합니다.      

#### verify()의 추가기능       

모든 함수가 1번만 실행되도록 Test를 작성하는건 말이 안되죠. 때문에 Mockito는 함수 호출 횟수를 정할 수 있는 기능을 제공합니다.     

	verify(obj , times(num)).method();  // num 번 호출
	verify(obj , never()).method(); //0 번호출
	verify(obj , atLeastOnce()).method(); // 최소한번
	verify(obj , atLeast(num)).method(); // 최소 num 번
	verify(obj , atMost(num)).method();  // 최대 num 번

> num이 들어간 부분에 숫자를 입력하여 사용할 수 있습니다.    


    @Test
    public void mockitoMethodTest() {
        // given
        ArrayList mockedList = mock(ArrayList.class);

        // when
        mockedList.add("JCW");
        mockedList.get(0);
        mockedList.get(0);

        // then
        verify(mockedList,times(2)).get(0);
        verify(mockedList,never()).get(1);
        verify(mockedList,atLeast(2)).get(0);
        verify(mockedList,atLeastOnce()).add("JCW");
        verify(mockedList,atMost(2)).get(0);
    }

위와 같이 사용 할 수 있습니다.    
위 Test는 모든 조건이 성립하기에, Test가 성공적으로 통과됩니다.   

>   verify()를 이용하여 Test의 성공여부를 알 수 있습니다.   


#### When() , thenReturn() , thenThrow()     

보통 Mock 객체의 함수가 실행되도 아무런 값도 Return 하지 못합니다. 만약 Mock 객체의 함수가 특정 값을 리턴해야될 경우, When()을 사용하면 됩니다.     
  
	// 원본
	when(obj.method()).thenReturn(value);
	when(obj.method()).thenThrow(new Exception());

	// 예제        	
	1. when(mockedList.get(0)).thenReturn("hello?");
    2. when(mockedList.get(0)).thenThrow(new RuntimeException());

(1) 처럼 선언하고 사용할 경우, get(0)의 return 값으로 hello를 반환합니다.      
(2) 처럼 선언하고 사용할 경우, get(0)를 실행하면 exception이 발생하게 됩니다.     

> when()을 사용하면 return 값을 설정 해 줄 수 있고, 나아가 Throw 또한 던질 수 있습니다. 
> 다만 반환 값이 void 인 함수의 경우 thenReturn()은 사용하지 못합니다.      

반환 값이 없는 함수는 다음과 같이 사용될 수 있습니다.    

	doThrow(new Exception()).when(mock).method()

	ArrayList list = mock(ArrayList.class);
    doThrow(new Exception()).when(list).add(0);

    list.add(0);

위와같이 사용하면 doThrow 부분의 인자를 통해 Exception을 설정해 줄 수 있습니다.     

	when(list.add(0)).thenThrow(new NullPointerException());    

이렇게 사용할 경우 when의 인자로 void 형식이 들어가기 때문에 문법상 맞지도 않고, 아무런 문제없이 Test가 성공하게 됩니다.  

> 사용자가 의도한건 Exception을 뱉는건데 말이죠 ...

추가적으로, when()의 인자로 같은 함수를 중복하여 사용 할 경우, 마지막으로 호출한 함수가 이전 함수를 덮어쓰게 됩니다.    

	    when(mockedList.get(0)).thenReturn("hello?");
        when(mockedList.get(0)).thenReturn("TT"); 

위와같은 상황에서 mockedList.get(0) 을 호출하면 "TT"를 return 합니다.       


#### InOrder      

InOrder라는 Class를 사용하면 함수들의 호출 순서까지도 검증할 수 있습니다.     

	// mock 생성	    
    List mockList = mock(List.class);
	// method 호출
    mockList.add("single");
    mockList.add("mock");
	// order 생성
    InOrder inOrder = inOrder(mockList);
	// 호출한 method의 순서대로 검증. 
    inOrder.verify(mockList).add("single");
    inOrder.verify(mockList).add("mock");

위 Test는 문제 없이 작동되며, 만약 single과 mock 사용 순서가 바뀐다면 Test는 실패하게됩니다.       


		List mockList = mock(List.class);
        List mockList2 = mock(List.class);

        mockList.add("multi");
        mockList2.add("mock");

        InOrder inOrder = inOrder(mockList, mockList2); 

        inOrder.verify(mockList).add("multi");
        inOrder.verify(mockList2).add("mock");     

만약 2개 이상의 객체에 대해 함수 실행순서를 검증하고 싶다면 inOrder에 여러개의 인자를 넘기고 Test를 작동시키면 됩니다.       

	public static InOrder inOrder(Object... mocks) 

> inOrder 함수가 가변인자로 정의되어 있기에 여러개를 넣을 수 있습니다.  


#### Argument Matcher       

Mockito는 함수에 인자를 줄때 사용할 수 있는 여러가지 Matcher를 제공합니다.    

    when(list.get(anyInt())).thenReturn("int");
    when(list.add(anyFloat())).thenReturn(true);
	when(list.add(anyString())).thenReturn(true);

    System.out.println(list.get(999)); // int
    System.out.println(list.add(3.3)); // true
	System.out.println(list.add("string")); // true

    verify(list).get(anyInt());
    verify(list).add(anyFloat());
	verify(list).add(eq("strings"));

anyInt(), anyFloat(), anyString(), eq()등의 Machter를 사용 할 수 있습니다.      
eq(value)을 사용하면 특정 변수의 값으로 함수가 호출됬는지를 확인할 수 있습니다.       

> 위 코드는 error을 뱉는데, anyFloat()가 호출되지 않아서 생기는 문제.    
>  조금더 생각해 볼 것.    


	개인적인 생각으론 list.add 함수를 2번 호출하기때문에 에러. 
	Type을 읽어주진 못하는거 같음 .... (?)      
      

#### Chainning       


    when(list.get(0)).thenReturn("int");
    when(list.get(0)).thenReturn("String");

    System.out.println(list.get(0));
    System.out.println(list.get(0));

    verify(list,times(2)).get(0);    

위 코드의 실행 결과는 

	String
	String 

입니다.     

> 나중에 불러진 when(list.get(0)).thenReturn("String"); 가 "int"를 덮어 쓰기때문이죠.      

만약 같은 함수가 호출 될떄마다 다른 값을 반환하고 싶은 경우 Chainning을 이용하면 됩니다.      

    when(list.get(0)).thenReturn("int").thenReturn("String");

    System.out.println(list.get(0));
    System.out.println(list.get(0));     
	// 결과 
	int
	String     

	// 이렇게도 쓸 수 있습니다.    
	when(list.get(0)).thenReturn("int","String");     

만약 list.get(0)을 3번 이상 호출 한다면, 마지막으로 등록한 값("String")이 호출됩니다.       



#### 참고         

[Mockito 사용하기 1](http://bestalign.github.io/2016/07/08/intro-mockito-1/)         



2017-08-22      

lusiue@gmail.com   