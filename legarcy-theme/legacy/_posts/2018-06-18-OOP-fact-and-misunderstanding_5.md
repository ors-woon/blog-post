---
layout: post
title:  fact-and-misunderstanding-5
tags: Study project
categories: fact-and-misunderstanding  
---   

> 06.18 
> 3장과 4장을 정리하기도 전에.. 5장을 먼저 정리합니다 ..  

#### 시작하며  

이번 장에서는 책임과 메시지라는 주제로 저를(?) 혼란스럽게 합니다.

책에서는 아래와같은 문장으로 시작을 합니다. 

    "모듈 내부의 행동/속성보다 커뮤니케이션에 초점을 맞춰라"

이전 챕터에서 `객체의 행위보다 협력관계를 먼저 생각하고 설계하라`라고 말한 것과 유사한 의미로 해석됩니다. 나아가 객체의 역할/책임을 명확히 하라고 언급합니다. 

#### 책임   

객체가 행동하는 유일한 이유는 다른 객체로부터의 `요청`입니다. 

> 즉, 요청을 받은 객체는 그 요청을 수행할 `책임`이 생깁니다. 

이전에도 말했듯, 객체는 자율적인 존재입니다. 스스로 정한 원칙에 따라 판단하고 스스로의 의지로 행동해야합니다. 자신의 책임에서 벗어난 부분에 대해서는 다른 객체에게 `요청`을 보내 협력관계를 이루고 문제를 해결하는게 객체지향이 추구하는 목표입니다. 

만약 `협력관계`를 이루지않고, 다른 객체가 해야할 책임을 모두 수행할 경우 유연하지 못한 구조가 생성됩니다. 

> OCP를 깨드릴 수 있고, LSP도 깨지겠네요 ..

다른 객체가 해야할 책임을 모두 수행한다 라는 말은 
    
    객체의 자율성을 침해한다.
    
라고 해석할 수 도 있습니다. 

이는 캡슐화가 깨졋다라는 의미와 동일하며 결국 객체지향의 장점을 포기하는 형태가 됩니다.

#### 객체의 자율성   

이전 햄버거 상황을 다시 가져와 봅시다.

    1. 손님은 직원에게 햄버거를 주문한다.
    2. 직원은 뒤에있는 주방에게 햄버거를 요청한다.
    3. 주방은 햄버거를 직원에게 넘긴다.
    4. 직원은 손님에게 햄버거를 준다.

위 관계에서 `손님`, `직원`, `주방`은 협력관계를 이루며 하나의 시스템을 구성합니다.
이때 각 객체들은 `요청`을 통해 서로에게 책임을 위임(?)합니다.  

여기서 중요한 부분은 손님이 직원(다른 객체)에게 자세한 요청은 하지 않습니다. 

    빨간옷 입은 청년이 만들어주시구요.
    패티를 먼저굽고, 야채를 올려주세요. 케찹은 패티 위에 뿌려주시구요.

(저렇게 요청하면 진상입니다.)

> 물론 필요에 따라서 야채 많이 ~ 라고 말할 순 있겟지만 ..


위에서 말한 현실세계의 상황과 객체의 상황을 동일하게 봐도 무방합니다.
객체 세계(?)에서도 다른 객체에게 요청을 보낼때, 그 객체의 자율성을 침해해서는 안됩니다.

객체의 자율성을 침해한다는 의미는, `그 객체가 어떤 상태를 갖는지를 알고있다.` 라고 생각할 수 도 있습니다. 

> 결국 유연하지 못한 관계가 형성됩니다.

또한 지나치게 추상화된 요청은 수신받는 객체로 하여금 혼란을 일으킬 수 있기에 적정수준을 유지해야한다고 말합니다.(문맥에 따라 달라지므로.. 상황에 맞춰 잘 설계해야한다고 하네요..)

#### how 보단 what  

객체의 `자율성`을 지키기 위한 핵심은 `what`에 초점을 맞추는 것입니다.  
위에서 말했듯 `(어떻게) 해줘!` 보단,  `(무엇을) 해줘 !`라는 것이 객체의 자율성을 지키는데 효과적(?)입니다.

> Don't tell, Ask 도 같은 맥락이라고 보면 좋을 것 같네요. 


#### 메시지 


    객체가 행동하는 유일한 이유는 다른 객체로부터의 `요청`입니다. 

라고 말했었는데요.
달리말해, 요청은 객체가 다른 객체에게 접근할 수 있는 유일한 방법입니다. 

> 책에서는 이걸 메시지라 부릅니다. 

메시지는 아래와 같은 형태를 띄게됩니다.  

    수신자.행동(추가정보)

이걸 조금더 개발자스럽게(?) 보자면 아래와 같겠네요.

    receiver.behavior(param);


메시지는 객체의 외부와 내부를 분리하는 기준점이 됩니다. 

#### 오해 

개인적으로 이해하기는 외부에 공개된 영역, 즉 Interface 라고 이해했습니다.
여기서 중요한 점은 `메시지 != 메서드` 라는 것 입니다.

> 사실 어떻게 말하냐에 따라 조금 달라질 수 도 있을 것 같긴합니다 .. 

메시지는 외부에 공개된 영역으로, 객체는 메시지를 받으면 `자율적`으로 처리 방법을 정할 수 있습니다.
여기서 `자율적`이라는 말을 상속/다형성의 관점에서 봐야합니다. 

OOP에서는 `동적바인딩` 즉, 런타임에서 어떤 함수가 호출되는지 결정됩니다. 다시말해 하나의 메시지를 누가 받느냐에 따라 수행결과가 달라집니다. 

> (개인적으로 이해하기를) 이걸 `객체의 자율성` 이라고 생각합니다.

지금까지 `객체의 자율성`과 `협력`을 반복적으로 언급한 이유는 이제부터 나옵니다 .. 

#### 협력관계의 재사용 

OOP의 특징 중, `재사용성` 이라는 항목이 있던걸로 기억합니다.

> 캡슐화, 상속 , 재사용성 ...

이런게 있던걸로 기억하는데 .. 

책에서는 재사용성을 협력과 묶어서 설명합니다. 

    "협력관계를 재사용한다."

위에서 메시지와 메서드가 다르다고 말하면서 `객체의 자율성`, 즉 객체는 받은 메시지에 대한 처리 방법(메서드)를 자유롭게 선택한다고 말했습니다. 

> 다형성으로 생각해도 무방할것 같네요.

다시 정리하자면 같은 메시지를 받을 수 있는 객체들 즉, 책임이 같은 객체들은 만들어진 협력관계에서 `재사용`할 수 있습니다.

예를들어 Cashier 라는 `interface`를 상속받은 객체라면, Cashier로 맺어진 협력관계에서 교체되어 사용할 수 있습니다.

> 책에서는 이를 `대체가능성`이라고 언급합니다. 

SOLID 중, DI(Dependency Inversion)를 충족시켜야 하는 이유가 여기에 있습니다. 

(정확히는 SOLID를 충족시켜야하는 이유라고 봐야겠지만..)

객체가 바라보는 대상이 (적절히) 추상화되어있지 않으면, 맺어진 협력관계는 1회용으로 사용될 뿐 재사용하기 어려워집니다.

결과적으로는 변경에 열려있는 .. 코드가 되겠네요 :D 


#### 마치며  

책의 내용 모두를 정리한 것은 아닙니다.

> 부족한 점도 많구요 ..

(시간 나면 나머지 더 정리하도록 할게요)

피드백 주시면 감사합니다 

2018.06.18
lusiue@gmail.com