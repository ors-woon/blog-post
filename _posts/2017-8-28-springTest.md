---
layout: post
title: 04-Spring Test에 대하여
tags:  Test 
categories:  Test
---       

   
처음 Spring-test까지 도입하라는 글을 봤을 때     

> Mockito[Test-FrameWork]를 썻으면 됬지! 무슨 Test를 또하냐 !    

라는 생각을 했습니다. 하지만 spring camp에서 진행됬던 test 관련 영상을 보고 사용하기 시작했고 , 정말 좋은 도구라는걸 깨달았습니다.   

Youtube 영상으로는 [Spring-camp Test](https://www.youtube.com/watch?v=k_88ADbuJqQ&t=1045s)을 보시면 좋을 것 같습니다.    


#### Spring Test를 쓰는 이유       

Spring Test를 도입하기 전, 개발흐름(?)은 다음과 같이 진행했습니다.       

<br> 

<center>
	<img src = "/public/img/beforespring.jpg">    
</center>
   
코드를 작성하고, Server를 킵니다. 만약 url/dispatcher에 문제가 생기면 서버를 끄고 다시 코드를 수정합니다. 그후 다시 서버를 키는 사이클로 개발을 진행했습니다.    

위 과정에서 서버 재가동하는 시간은 평균적 30~60초 정도 소모됩니다.    

>  그때마다 저는 밀린 카톡을 읽었습니다.....    

그러다보니 집중이 깨지기 시작했고, 언제부턴가 코드를 작성하는 시간보다 서버를 재가동하는 시간이 더 오래 소모되더군요.     

하지만 Spring Test를 사용한다면 다음과 같은 과정으로 개발흐름이 바뀔 수 있습니다!!    

 <br>           
<center>
	<img src = "/public/img/AfterSpringtest.jpg">    
</center>     



> Tomcat을 가동하지 않고도 url/dispatcher를 확인 할 수 있습니다 !!      


#### Spring-Test 도입하기       


> Maven 에 의존성 추가하기    

spring-test를 사용하기 위해 의존성을 추가해야합니다.      

		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-test</artifactId>
			<version>${org.springframework-version}</version>
			<scope>test</scope>
		</dependency>



> 사용하기 - 설정        

	@RunWith(SpringJUnit4ClassRunner.class)
	@ContextConfiguration(classes = RootApplicationContextConfig.class)
	@WebAppConfiguration
	public class RestProductControllerTest {  
		// Test 내용 
	}      

기존 Test Class에 @WebAppConfiguration Annotion이 추가된것을 볼 수 있습니다.  ApplicationContext을 가져오기 위해 사용하는 Annotation입니다.      

	// 지역변수    	

	private MockMvc mockMvc;

	@Autowired
	WebApplicationContext context;    

MockMvc의 변수를 선언하고,  @WebAppConfiguration을 통해 가져온 ApplicationContext을  변수에 주입합니다.       

그 후, setUp Method에 mockMvc를 초기화하여 진행 합니다.     

> Case 1 

	@Before
	public void setUp() {
		MockitoAnnotations.initMocks(this);
		mockMvc = MockMvcBuilders.webAppContextSetup(context).build();
	}

모든 Spring의  설정 정보를 가져와서 진행하는 테스트입니다.   
단위테스트가 아닌,  통합테스트의 느낌이 강합니다.

> Case 2      

	@Before
	public void setUp() {
		MockitoAnnotations.initMocks(this);
		controller = new RestProductController(service);
		mockMvc = MockMvcBuilders.standaloneSetup(controller).build();
	}     

위 테스트는 인자로받는 Controller에 대한 Test로 단위테스트의 느낌이 강한 Case 입니다.     

> 물론 두가지 Case 모두 통합테스트로 취급한다고합니다.       

(+)    
사실 통합테스트와 단위테스트의 경계가 명확하지 않기때문에, 딱 잘라 구분하기는 어려울것같습니다.       

> 사용하기 - 시작       


	mockMvc.perform(get("/api/product?start=1&amount=3")).andExpect(status().isOk())
				.andExpect(content().contentType("application/json;charset=UTF-8"))
				.andExpect(jsonPath("$", hasSize(1)))
				.andExpect(jsonPath("$[0].id").value((int) productId));
		verify(service, times(1)).selectList(start, amount);          

변수들을 설정 후 위와같이 Test를 진행할 수 있습니다.     

초기화한 mvcMock은 perform이라는 함수를 제공하는데, 해당 함수의 인자로 

	get() / post() / put() / delete()      
    
등의 spring-test lib에서 제공하는 함수를 사용할 수 있습니다.      

> HTTP Method와 같은것을 알 수 있습니다. controller에서 정의된 url과 해당 Method를 알맞게 사용하면 됩니다.      

그 후, chainning을통하여 andExpect() 와 andDo(), andReturn() 을 사용 할 수 있습니다.     

**andExpect()**는 넘겨받은 response 의 값들을 검증 할 수 있는 기능을 제공합니다. 예시에서는 contentType 이나 json형태의 반환값을 확인하는 용도로 사용 했습니다.    

**andDo()**는 넘겨받은 요청을 print하는 기능을 제공하는데, 다음과 같이 사용할 수 있습니다.    

	andDo(print());    

위 함수를 예시에 추가하여 실행할 경우, 다음과같은 형태로 console에 출력되는 것을 볼 수 있습니다.     

	MockHttpServletRequest:
	      HTTP Method = GET
	      Request URI = /api/product
	       Parameters = {start=[1], amount=[3]}
	          Headers = {}
	
	Handler:
	             Type = kr.or.reservation.controller.rest.RestProductController
	           Method = public java.util.List<kr.or.reservation.domain.Product> kr.or.reservation.controller.rest.RestProductController.selectList(int,int)
	
	Async:
	    Async started = false
	     Async result = null
	
	Resolved Exception:
	             Type = null
	
	ModelAndView:
	        View name = null
	             View = null
	            Model = null
	
	FlashMap:
	       Attributes = null
	
	MockHttpServletResponse:
	           Status = 200
	    Error message = null
	          Headers = {Content-Type=[application/json;charset=UTF-8]}
	     Content type = application/json;charset=UTF-8
	             Body = [{"id":1,"categoryId":0,"name":null,"description":null,"salesStart":null,"salesEnd":null,"salesFlag":0,"event":null,"createDate":null,"modifyDate":null,"firstImageSaveFileName":null,"productDetail":null,"displayInfo":null,"productImage":null,"productPrices":null}]
	    Forwarded URL = null
	   Redirected URL = null
	          Cookies = []      




**andReturn()**은 결과를 반환하는 함수로, MVCReturn Object를 반환합니다.       
해당 함수를 사용하여 Body값을 별도로 추출할 수도 있습니다.   

	String content =   this.mockMvc.perform(함수).andReturn().getResponse().getContentAsString();	   


#### 마치며      

간략하게 사용법과 왜 사용하는지에 대해 초점을 맞춰 정리했습니다. Spring - test를 이용하면 Session 값을 별도로 지정하여 Test할 수 있습니다. 나아가, 단순히 Controller Layer만을 검증하는것이 아닌, Dispatcher 까지도 Test할 수 있다는 장점이 존재합니다.     

저는 주로 Mockito와 함께 Sprint-test를 사용했습니다.        

2017-08-28       

lusiue@gmail.com 



