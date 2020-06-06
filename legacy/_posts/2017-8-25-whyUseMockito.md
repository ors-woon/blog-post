---
layout: post
title: 03-TestFrameWork사용이유
tags:  Test TDD
categories:  Test
---       


현재 진행하고 있는 프로젝트의 Test코드를 비교하면서, Mockito 도입 후, 어떤점이 개선되었는지 확인하는 포스팅입니다.      
Test FrameWork로 mockito를 사용 중에 있으며, 추가적으로 spring-test도 도입했습니다.     

> spring-test에 대해선 다음 포스팅에 정리하도록 하겠습니다.   

#### 사용 이유           

저희가 Mockito를 적용하려는 가장 큰 이유는 Layer를 독립적으로 Test하기 위함입니다. 조금더 자세히 보기위해 이전 코드를 가져오겠습니다.    
	
	@Before
	public void insert() {
		Category category = new Category();
		category.setName("영화");
		key = categoryService.insert(category);
	}

> Test 전 데이터를 insert하는 로직.     

	@Test
	public void select() {
		List<Category> categories = categoryService.selectList();
		int last = categories.size() - 1;
		long selectKey = categories.get(last).getId();
		Assert.assertThat(selectKey, is(key));
	}

> 데이터를 가져와 해당 값이 key와 일치하는지를 검증 

	@Test
	public void delete() {
		long index = categoryService.delete(key);
		Assert.assertThat(index, is((long)1));
	}
	
> delete 의 영향을 받은 key가 하나인지를 검증       

그동안 Test를 단순히 Service 검사로 사용했습니다. Select인 경우 올바른 값을 가져오는지 , Log로 확인하고,
Insert의 경우 값이 제대로 들어가는지 확인해 왔습니다.    

> 지금 생각해보면 단순히 JUnit 이라는 'Main' 함수들을 사용한 느낌입니다.      

Test에 대해 공부하면서 그동안 작성했던 Test 코드가 심히 잘못되 있음을 알 수 있었습니다.        

#### 작성한 Test 코드의 문제점         

해당 Test Case의 대표적인 문제점은 다음과 같습니다.     

	@Autowired
	CategoryService categoryService;      

1. Spring에 의존하고 있다는 점.
2. Service 계층이 독립적으로 Test가 이루어지고 있지 않다는점       

(1) - Test를 진행하는데도 Spring을 이용해야 되나 라는 의문점이 생겼습니다.      

> 해당 부분은 조금 더 고민을 해봐야합니다 ... 
     
( 추가적으로 )             
   
Test할때 최대한 spring 설정파일을 읽는 시간을 단축하고싶어서 분리하려고했지만,    
Spring을 사용하면서 Spring에 의존하지 않도록 해야하나? 라는 조언(?)을 받았습니다.    

> 생각해보니 , Spring을 사용하는데 분리할 필요가 있을까라는 .. 의문점이 생기더군요     

그래서 최대한 .. Spring을 사용하기로 했습니다..     


(2)       

	@Test
	public void select() {
		List<Category> categories = categoryService.selectList();
		int last = categories.size() - 1;
		long selectKey = categories.get(last).getId();
		Assert.assertThat(selectKey, is(key));
	}    
	
위 Test Case의 문제점은 한가지 Test에 2가지 Layer가 연관된다는 점입니다. 
만약 Test가 실패시에 DAO , Service 중 어디에 문제가 있는지 알 수 없고, 다수의 함수를 확인해봐야합니다.     
이와같은 협력객체와 실제 Test할 객체를 분리하기위해, Test Double을 제공하는 Mockito를 사용했습니다.        

#### 수정된 코드

	@Mock
	CategoryDao categoryDao;

	@Test
	public void testInsert() {
		// given
		CategoryService categoryService = new CategoryServiceImpl();
		categoryService.setCategoryDao(categoryDao);
		Category category = new Category();
		category.setName("영화");

		// when
		categoryService.insert(category);

		// then
		verify(categoryDao).insert(category);
	}

	
Mockito를 이용해 Mock 객체를 생성하고, Test하려는 Service에 주입합니다.      
그 후, 주입한 함수가 호출됬는지를 확인하는 Test Case 입니다.     

> 물론 올바른 Test라고 말할 순 없습니다.
> 검증이 너무 느슨한 점도 있고, 여러문제도 있겠죠 .. 
> Test에 대해 조금더 공부하고 수정하도록 하겠습니다. 

Test FrameWork를 이용하면, 각 계층을 독립적으로 Test 할 수 있고 원하는 함수/값이 불려졌는지도 확인할 수 있습니다.      

> Mockito를 이용하면 위와같이 의존객체를 Mock으로 대체하여 Test하고 싶은 부분만 집중 할 수 있다.      
> 엄청 편리합니다.. !


2017 -08-25      
lusiue@gamil.com          


 