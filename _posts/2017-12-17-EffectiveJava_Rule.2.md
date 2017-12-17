---
layout: post
title:  Effective Java Rule.2
tags: Effective Study 
categories: Effective
---   



#### Rule. 2 생성자 인자가 많을 땐, Builder Pattern을 고려해봐라    

Rule. 1 에서 언급한 바와 같이, 아마 2주차까지는 객체의 생성/소멸등의 생명주기에 대한 Rule들을 학습합니다.    

Rule. 2는 생성자 인자가 많을 경우 Builder Pattern을 고려하라는 말을 합니다.   

**첫번째 이유** 

이전 Rule과 유사한 이유에서 Builder Pattern을 사용하라고 하는데, 만약 생성자에 인자가 다양할 경우 아래와 같은 코드가 완성됩니다.  

	public constructor(String name , String email , 
					String age , String address, String sex , ... ) { }


생성할 당시에는 바로 사용하는데 무리가 없을 수 있지만, 당장 내일만 되더라도 생성자 인자들의 변수명을 기억하지 못할 겁니다.  


**두번째 이유**    

만약 필요에 따라서 복수개의 생성자를 만든다고 하면, 위 상황보다 조금 더 복잡한 그림이 그려집니다.   

	public constructor(String name , String email , 
				String age , String address, ... ) { }

	public constructor(String name , String email , 
				String age , String address, String sex , ... ) { }	
 

위 처럼 다양한 인자를 받은 생성자가 2개만 되더라도 사용자는 자신이 어떤 생성자를 불렀는지 알기 어렵습니다.   

> Intelli J 에서는 알려주긴 합니다만 ..   

또한 Static Factory Method 에서 언급한 바와같이, 동일한 인자에 대해서 2개 이상의 생성자를 만들 수 없다는 한계점 또한 존재합니다.     

**해결 방법**     

	
	1. 점층적 생성자 패턴
	2. Setter/Getter 사용     
	3. Builder Pattern    
	

( 위 2 방식은 코드로 설명해줄게요 )

위 두방식은 분명 단점이 존재하고, 때문에 Builder Pattern 이라는걸 사용을 권장합니다.   

점층적 생성자 패턴의 경우, 가독성 부분과 불필요한 Default 값, 또한 코드의 양의 측면에서 좋지 못한 코드이며, Setter 의 사용은 객체의 일관성이 일시적으로 깨질 수 있다는 점에서 사용을 피하게 됩니다.    

> 여기서 객체의 일관성 부분은 정확히 이해하지 못했는데, 한줄로 생성이 불가능하다 보니 Multi-Thread 환경에서 일관성이 깨질 수 있다고 생각하고 넘어갔습니다.   

( 간단하게 함께 언급해보면 좋을 것 같습니다. )    

다시 돌아와서, 이러한 문제점들을 해결하기 위해 Builder Pattern을 사용하라하는데, Builder Pattern의 정의는 다음과 같습니다.    


	모든 매개 변수를 받은 뒤에 이 변수들을 통합해서 한번에 사용한다.    



[여기부턴 코드로 설명]     


#### Builder Pattern의 장점   

	1. 가독성
	2. 값들의 유효성 검사
	3. 불변식 사용가능
	4. Chaining 가능 

> 예시 GraphQL 객체 생성 코드  





2017 .12 .17     
lusiue@gmail.com     


	 




