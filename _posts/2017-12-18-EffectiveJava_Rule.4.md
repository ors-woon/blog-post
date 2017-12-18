---
layout: post
title:  Effective Java Rule.3  
tags: Effective Study 
categories: Effective
---   


#### Rule.4 객체의 생성을 막을땐 private 생성자를 사용하라    

개발을하다보면, 필요에 따라 Utility Class를 만들어야 할 필요가 발생합니다. 최근에 만들었던 Util은 Json 문자열을 Map으로 변환하는 Class였는데 해당 클래스의 경우 대부분 정적 메서드로 이루어져있으며 객체를 생성할 필요가 없었습니다.  
> 물론 객체를 생성해서 사용한다고 하더라도, 큰문제는 없었습니다.   

책에서는 객체 생성을 막으려 한다면, 생성자를 private로 두고 예외처리를 하라고 합니다. Java는 기본적으로 default Constructor를 제공하기에 생성자를 안만들면, public한 생성자가 만들어지고 객체 생성을 막을 수 없기 때문입니다.   


때문에 생성자를 private로 두고 설정한 뒤, Exception 처리를 하면, 객체 생성을 막을 수 있습니다. 

	// prevent to initialize 
	private PrivateConstructor() {
		System.out.println("이거 뚫고 오나?");
	}


> 정확히는 모든 생성자가 private 여야만 합니다.

또한 private로 생성자를 둘 경우, 상속또한 불가능한데 Java에서는 상속시 부모의 생성자를 overriding 해야하기 때문입니다. 하지만 하나밖에 없는 생성자는 private임으로  overriding이 불가능해지고, 이는 compile단계에서 error를 뱉게 됩니다.   

만약 private으로 생성자를 막을 경우, 해당 코드 위에는 주석을 달아 의도를 명확하게 표기하는게 좋습니다.   

해당 방법 외에도 abstract keyword를 붙여 객체 생성을 막을 수도 있는데 이는 일시적인 것 뿐, 상속을 이용해서 객체를 생성할 수 있기에 옳지않는 표현이라고 합니다.   


#### 추가적으로   

Spring을 사용하는 입장에서 private 생성자를 두고 bean 설정을 하면 어떻게 될까 라는 생각을 해서, 테스트를 해봤습니다.    

	@Component
	public class PrivateConstructor {
		
		// 생성을 막음
		private PrivateConstructor() {
			System.out.println("이거 뚫고 오나?");
		
		}
		
		public void print() {
			System.out.println("Hello?");
		}
		
	}

이 상태에서 PrivateConstructor를 주입받아 

	privateConstructor.print();

를 호출하면 ...

잘 작동합니다.   

Spring은 인자가 알맞은 constructor가 있으면 해당 생성자가 private, public이든 상관하지 않고 DI를 진행합니다.    

> 이 부분은 reflection을 이용하기 때문에 가능한 것으로 알고있습니다.   

때문에 만약 객체 생성을 반드시 막고자한다면, private 내부에 error 처리를 해주는게 좋습니다.     

[Java Spring bean with private constructor](https://stackoverflow.com/questions/7254496/java-spring-bean-with-private-constructor)

[관련 Spring doc](https://docs.spring.io/spring/docs/3.0.x/javadoc-api/org/springframework/beans/BeanUtils.html#instantiateClass(java.lang.reflect.Constructor, java.lang.Object...))

2017 .12 .18    
lusiue@gmail.com     

