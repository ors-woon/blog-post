---
layout: post
title:  Effective Java Rule_ExceptionBase 
tags: Effective Study 
categories: Effective
---   

Effective Java의 Exception을 설명하기 전에 checkedException과 UncheckedException에 대해 간단하게 정리하겠습니다.

#### Exception

Java에는 2가지 종류의 Exception이 존재합니다. 

바로 uncheckedException과 CheckedException 입니다.

간단하게 말해서, 컴파일 시점에서 처리가 강제되는 Exception을 checked, 그럴 필요가 없는 것을 unchecked라고 말합니다.   

    ExecutorService es = Executors.newFixedThreadPool(1);
    Future<String> future = es.submit(() -> {
        return "String";
    });

    try {
        future.get();
    } catch (InterruptedException e) {
        e.printStackTrace();
    } catch (ExecutionException e) {
        e.printStackTrace();
    }

Future는 일반적으로 get 사용시, 2개의 Exception을 강제합니다. 만약 이를 처리하지 않을 경우 컴파일 에러를 뱉게 되죠.
이렇게 예외처리를 강제하는 것을 checked Exception이라고 말합니다.

    try{
        int a = 1/0;
    }catch (ArithmeticException e){
        // something 
    }

위 코드는 try-catch가 없어도 문제가 되진않습니다.
다만, 개발자가 문제를 예상하고 handling할 수는 있죠 ..

> 예외처리가 있는 이유이기도 합니다. 

위와같은 예제를 uncheckException이라고 말합니다.  

한마디로, 다른 개발자(?)가 처리를 강제하도록 만든 예외가, checked / 처리를 강제하지 않고, 런타임에 발생할 수 있는 예외를 unchecked라고 말합니다.  

> unchecked에 예외 / 에러 두가지 종류가 포함되어 있습니다.

Java의 throwable 계층구조를 보면, Throwable 아래에 Error/ Exception 이 존재하며, 다시 Exception에는 RuntimeException을 비롯한 여러가지 하위 클래스가 존재합니다.

여기서 RuntimeException을 상속받아 구현된 클래스들을 uncheckedException, 그 외의 클래스들을 checkedException이라 부릅니다. 


이 정도만 알고 있으면, 책을 읽는데 큰 문제가 없을 것 같습니다 :D


lusiue@gmail.com
2018.03.23


