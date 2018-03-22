---
layout: post
title:  Effective Java Rule.57    
tags: Effective Study 
categories: Effective
---   

들어가기전에 checkedException과 UncheckedException에 대해 간단하게 언급하고 리뷰하겠습니다.

Java에는 2가지 종류의 Exception이 존재합니다. 

> checkedException을 왜 만들었냐에 대한 논쟁이 존재하지만, 여기서는 다루지 않습니다.

(간단하게 말해서,) 컴파일 시점에서 처리가 강제되는 Exception을 checked, 그럴 필요가 없는 것을 unchecked라고 말합니다.   

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

한마디로, 다른 개발자(?)가 처리를 강제하도록 만든 예외가, checked / 처리를 강제하지 않고, 런타임에 발생할 수 있는 예외를 uncheck라고 말합니다.  

> 여기서, 예외와 에러에 대해서는 설명하지 않습니다.

이제 이것을 base로 몇가지 rule에 대해 설명하겠습니다.


~ 작성중 