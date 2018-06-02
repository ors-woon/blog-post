---
layout: post
title:  Inline JavaScript In Html
tags: Study Js
categories: Js  
---   

이전에 팀원분과 대화하면서 이런 대화를 한적이 있습니다.

    Library의 함수가 inline이라서 잘 안써요.

특정 library의 함수가 (내부적으로) inline 처리를 한다는 말을 듣고,
`왜 안좋지?`라는 의문점이 생겼습니다. 

> Library가 뭔지 기억이 안나서.. 저렇게 표현했습니다. 

제가 알고있는 inline은 HTML의 inline 과 block 속성이 였기에...      
`inline Js`를 왜 피해야하는가에 대해 배운 내용을 정리하려 합니다. 

#### Inline   

Js의 Inline은 아래와 같은 형태를 의미합니다.

    <span onclick="someThing();">

> HTML안에 js소스를 삽입하는 것을 의미합니다. 

여기까지 알았을 때, `아! 유지보수때문에 힘들구나` 라는 생각을 했습니다. 

View 역할인 HTML에 처리 로직이 삽입되기에, 결과적으로 함수에 의존되고 변경이 일어났을 때 View 영역 함수까지 바꿔줘야하기 때문입니다.

하지만 문득 생각해보면, Vue.js 나 Angular.js 같은 경우 고유의 `attribute`(?)를 사용하여 이벤트 바인딩을 합니다.

    <a ng-click = "ctr.functions()">

결과적으로 View와 Js가 의존성이 있는건 팀원분이 말씀하신 inline의 문제점과는 거리가 있습니다. 

> 저희 팀은 angular.js 를 사용하고 있습니다.  

결국 조금 더 검색을 하기 시작했고, 해당 [링크](https://dev.to/chiefoleka/how-to-use-inline-javascript-with-html-you-definitely-like-really-bad-code-1a1o)에서 이유를 알 수있었습니다.

간단하게 정리하면 다음과 같습니다.

    1. Caching을 하지 못함.
    2. minify에 포함 안됨
    3. 분리를 못함 (위에서 설명한 것과 같은 이슈)
    4. 유지보수가 어려움    

#### cache  

기본적으로 browser에서는 js파일을 캐싱할 수 있습니다. `expires header` 값을 이용하여 캐싱을 진행합니다. 만약, html에 inline을 사용 할 경우 해당 함수는 캐싱되지 않은 채 변경이 없더라도 매 호출 때마다 파일을 가져오게됩니다.

#### minify  

많은 서비스들은 network 비용을 줄이기위해, HTTP의 `gzip` 이나 webpack 등에서 제공하는 minify를 기능을 사용합니다.  
다만, inline을 사용하면 minify 기능을 활용 할 수 없고 함수가 그대로 노출되는 단점이 존재합니다. 

개인적인 의견이지만, html에 js 파일을 선언할 경우 난독화에도 어려움이 생깁니다. 

> 똑똑한 애들은 해줄거 같긴합니다만 .. 

#### 파일의 분리 

결국 위에서 언급한 두개의 원인은 여기에 있습니다. 

    파일을 분리할 수 없다.

함수들을 js 파일로 분리하지 못해, minify를 진행 할 수 없고 cache또한 어려워집니다. 

이글을 쓴 작성자는 html에 js코드를 노출시키지 말고, `eventListener`를 사용하라고 합니다.

> 어느정도 동의하지만, angular / vue.js에서도 볼 수 있듯 이건 어쩔수 없는 문제같습니다 .. 

#### 또다른 문제 

다른 의견은 없을까 해서 구글을 헤집던(?) 중, 재밋는 [링크](http://jindo.dev.naver.com/blog/2014/02/556#comment-72)를 찾을 수 있었습니다. 


여기서는 scope를 기준으로 inline을 설명합니다.  

    <span onclick ="removeChild()">Hello</span>
    <script>
        function removeChild(){
            alert("remove Me!");
        }
    </script>


위 HTML에서 `Hello`를 클릭하면 어떻게 될까요 ? 

> 대형참사가 일어납니다 ..

예상되는 실행결과는 remove Me 라는 alert이 띄워지는 겁니다.
하지만 에러로그가 뜨면서 remove Me 는 보여지지 않습니다 ... 

여기서 문제는 바로 scope에 있습니다. inline에서 직접 호출 될 경우 아래와 같은 흐름으로 함수를 탐색합니다.   

    1. 자기 자신 탐색 
    2. form이 있다면 form 탐색  
    (이 부분은 브라우저마다 다름)
    3. document 탐색   

즉, 위 HTML은 자기자신의 scope에서 removeChild를 탐색 후, `없을 경우` document의 함수를 호출합니다. 

> 여기서는removeChild가 element에 내장되어 있는 함수이기에 에러를 뱉게 됩니다.   

다만 위 코드를 이렇게 바꾸면 문제가 없습니다 .


    <span id = "target" onclick ="removeChild()">Hello</span>
    <script>
        function removeChild(){
            alert("remove Me!");
        }

        document.getElementById("target").onclick = function(){
            removeChild();
            // this.removeChild();  
            // 이전 예제와 같은 결과를 출력
        }
    </script>

> 함수가 선언된 scope 에 removeChild를 찾기때문이죠.

만약 inline에서 처럼 동작하게 하려면, this를 붙이면 됩니다 :D 

#### 마치며   

scope가 다르다는 말은 들었지만, 어떻게 전파되고 어떤 문제가 있는지 알 수 있는 좋은 시간이였습니다.

> 모르는건 부끄러운게 아니지만 , 부끄럽네요 .. 

읽어주셔서 감사합니다.  

lusiue@gmail.com   
2018.06.02      
