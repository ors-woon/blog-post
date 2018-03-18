---
layout: post
title:  Effective Java Rule.45  
tags: Effective Study 
categories: Effective
---   

어쩌다보니, 스터디를 맡은 부분에 대해서만 정리를 하고 있습니다.
추 후, 복습할 기회가 생기면 다시 처음부터 정리해보도록 하겠습니다 :D  

#### 지역 변수의 유효범위를 최소화하라   

일반적으로 다른언어(c,js)들에서는 사용되는 변수들을 상단에 먼저 선언 후 사용하고 있습니다.

> c는 중간에 선언이 불가능했던 것으로 알고있으며, js의 경우 호이스팅때문에 상단에 선언하는 경우가 잦습니다.  

하지만 책에서는 상단이 아닌, 사용하기 직전에 선언해두라고 말합니다.

가독성의 문제도 있으며, 분기문을 타게될 경우 사용되지 않는 변수도 초기화하기 때문입니다. 

> Java의 scope는 block단위 입니다.

또한 (거의) 모든 지역변수를 선언할 때는 초기화를 함께 진행하라고도 언급합니다.
초기화하기에 충분한 정보가 없다면 선언되는 위치를 고려해봐야합니다.

> 단 try-catch 문은 예외입니다.

#### for / while 문   

책에서는 while문 대신 for문을 사용하라고 말하는데, 이 또한 유효범위와 관련되어 있습니다.


    for (int i = 0; i < 5; i++) {
        // something
    }

    int i = 0;
    while(i < 5 ){
    
        i++;
    }

위 예제를 보시면 이해가 빠를것 같습니다.
for 문의 경우 변수 선언을 for 문 내에서 할 수 있지만, while문은 그렇지않습니다.
또한 만약 다른 코드를 copy-past하는 중에 i/j를 잘못 사용 할 경우, for문은 컴파일 에러를 뱉지만 while은 그렇지 않습니다.

> while에서 사용되는 변수는 한단계 높은 scope이기 때문입니다.


    for (int i = 0; i < 5; i++) {
        System.out.println(i);
    }

    for (int i = 0; i < 5; i++) {
        System.out.println(i);
    }

    int i = 0;
    while(i < 5 ){
        System.out.print(i);
        i++;
    }

    while(i < 5 ){
        System.out.print(i);
        i++;
    }

꽤 단순한 코드입니다. 위 2개의 for문은 정상적으로 작동하지만 밑의 2개의 while문은 scope의 오염(?)때문에 하나의 while문만 동작하게 됩니다.

이러한 이유로, while 보다는 for문을 사용하라고 언급합니다.

#### Tip   

    for(int i=0,len=5; i<len; ++i){}

이렇게도 사용할 수 있습니다.  


2018.03.19     
lusiue@gmail.com

피드백은 환영입니다 :D 