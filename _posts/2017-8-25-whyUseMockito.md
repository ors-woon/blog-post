---
layout: post
title: 03-Mockito사용이유
tags:  Test TDD
categories:  Test
---       


현재 진행하고 있는 프로젝트의 Test코드를 비교하면서, 어떤점이 개선되었는지 확인하는 포스팅입니다.      

#### 사용이유          

현재 저희가 Mockito를 적용하려는 가장 큰 이유는 Layer를 독립적으로 Test하기 위함입니다. 조금더 자세히 보기위해 이전 코드를 가져오겠습니다.    
	
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


#### 작성한 Test 코드의 문제점         

해당 Test Case의 대표적인 문제점은 다음과 같습니다.     

	@Autowired
	CategoryService categoryService;      

1. Spring에 의존하고 있다는 점.
2. Service 계층이 독립적으로 Test가 이루어지고 있지 않다는점       
3. 그 외 문제점들 ... 

(1),(3)은 추후에 수정하도록하고, (2)는 Mockito를 사용함으로 독립적인 Test를 진행할 수 있습니다.      

> 이전코드는 Insert가 먼저 수행되고, select / delete를 test할 수 있었습니다.      
> Test 코드간 의존성이 발생되는 부분이기도 합니다.    

#### 수정된 코드

	@Mock
	CategoryDao categoryDao;

	@Test
	public void testInsert() {
		// given
		CategoryService categoryService = new CategoryServiceImpl();
		categoryService.setCategoryDao(categoryDao);

		// when
		Category category = new Category();
		category.setName("영화");
		categoryService.insert(category);

		// then
		verify(categoryDao).insert(category);
	}

	
위와 같이 작성하면 Service를 독립적으로 Test할 수 있을 뿐더러, Insert와 Select/Delete간의 의존성도 사라집니다.    

> Mockito를 이용하면 위와같이 의존객체를 Mock으로 대체하여 Test하고 싶은 부분만 집중 할 수 있다.      

(추가적으로)       
   
Test할때 최대한 spring 설정파일을 읽는 시간을 단축하고싶어서 분리하려고했지만,    
Spring을 사용하면서 Spring에 의존하지 않도록 해야하나? 라는 조언(?)을 받았습니다.    

> 생각해보니 , Spring을 사용하는데 분리할 필요가 있을까라는 .. 의문점이 생기더군요     

그래서 최대한 .. Spring을 사용하기로 했습니다..



2017 -08-25      
lusiue@gamil.com          


 

