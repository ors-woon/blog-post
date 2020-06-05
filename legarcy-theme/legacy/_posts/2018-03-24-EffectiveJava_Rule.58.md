---
layout: post
title:  Effective Java Rule.58    
tags: Effective Study 
categories: Effective
---   

> 복구 가능 상태에는 checkedException을, 프로그래밍 오류에는 UncheckedException을 사용하라 

책에서는 클라이언트가 코드를 복구 할 수 있을 때, checkedException을 사용하고, 그 외 프로그래밍 오류에는 UncheckedException을 사용하라고 합니다. 

#### checkedException    

checkedException은 예외를 처리한다는 개념 외에도, 예외를 알리는 용도로 사용될 수 있습니다. 

API를 사용하는 사용자에게 

    이 메서드는 이런 예외가 발생될 수 있으니, 별도로 처리를 해주세요. 
    
라고 요청하는 용도 입니다.  

#### UncheckedException   

Unchecked 는 error 와 runtimeException이 존재하는데, 이는 명백한 오류이므로 처리해선 안됩니다.

>  컴파일 시점에서 체크를 강요하지도 않습니다.

UncheckedException을 만나면 적절한 오류 메시지를 뱉으며 시스템을 중단시켜야합니다. 

#### Extends Exception    

에러를 재 정의 할때는 Exception / RuntimeException / Error 의 하위 클래스로 정의해야 합니다.

추상화 단계를 올려서 Throwable로 재 정의 할 수도 있는데, 책에서는 절대로 사용하지 말라고 합니다. 

> 일반적인 예외보다 나은점도 없고, 사용자를 혼란스럽게 합니다. 

#### 그럼 언제, 어느 Exception을 써야하는가 ? 

위에서 언급했듯, API 사용자가 복구 할 수 있는 예외에 대해서는 checked / 명백한 프로그램 오류일 경우는 Unchecked를 사용하라고 합니다.

하지만 모든 상황이 그렇듯. 딱 잘라 분류 할 수 없는 문제가  존재합니다. 

> ex 메모리 문제에 대해, 실제로 메모리가 부족할 수도 있고, 프로그래머가 잘못된 메모리 주소를 참조 할 수도 있죠. 

우선 API개발자의 판단하에 결정하되, 잘 모르겠다면 unchecked를 사용하라고 합니다. (그 이유가 다음 룰 입니다.)


#### 그외     

checkedException을 사용할 경우, 사용자에게 충분한 정보를 주어야 합니다. 
언제/왜 문제가 발생했는지 알려줘야 사용자가 처리할 수 있기 때문이죠.

lusiue@gmail.com        
