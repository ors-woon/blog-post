---
layout: post
title:  Effective-Java -2 
tags: Study 
categories: Java
---   

이번 포스트에서는 메서드와 관련된 내용을 다룹니다.   

    인자의 유효성검사를해라
    필요하다면 방어적 복사본을 만들어라 
    메서드 시그니처는 신중하게 설계해라
    오버로딩할 떄는 주의하라
    varargs는 신중히 사용하라 

> 이 밑 부분은 다음주에 진행하도록 하겠습니다.

    null 대신 빈 배열이나 컬랙션을 반환하라
    모든 API 요소에 문서화 주석을 달아라  


네 끝입니다 ~~ 

:D  

#### 인자의 유효성 검사를 해라 

대부분의 메서드는 인자로 사용할 수 없는 값들을 제한합니다. 

> null 또는 유효한 값인지를 자주 확인하고 있습니다.

이같은 유효성 검사는 메서드 시작부분에서 선언해야하며, 반드시 문서로 남겨야한다고 합니다. 

유효성검사는 꼭 필요한 절차이지만, 몇가지 예외에 대해서는 진행하지 않아도 됩니다.

    1. 유효성 검사 시, Overhead가 크다.
    2. 연산과정에서 자연스럽게 진행된다.

> 다만, 2번의 경우 가독성의 문제라거나 실패원자성을 잃게되는 문제가 존재합니다.


#### 필요하다면 방어적 복사본을 만들어라

저희와는 조금 거리가 먼 규칙이라고 생각됩니다.
(api 개발자들을 위한 규칙이라고 생각됩니다.)

> api를 사용하는 client는 당신의 코드를 최선을 다해 망가뜨릴 것을 가정합니다.

즉, client가 이상한 짓을 해도, 정상적으로 돌아갈 수 있도록 코드를 작성해야 한다고 말합니다.

코드를 방어하는 기법은 여러가지가 있겠지만, 이번 rule에서는 `방어적 복사`를 초점으로 진행합니다.

#### final 사용 

기본 자료형에 대해서는 final 키워드를 붙이면 불변식을 적용할 수 있습니다.

    public int sum(final int a, final int b) {
        return a + b;
    }


하지만, 객체라면 final은 약간은 다른 느낌을 풍기게 됩니다.

    public static void populateDate(final Date date){
        date.setTime(10000);
        System.out.println(date);
    }

모두 알 수 있듯 위 코드는 컴파일에러 없이 돌아갑니다.
(불변식이 의미가 약간 퇴색(?)되네요)

이를 막기위해 방어적 복사본을 사용하라는게 이번 rule의 내용이며, 복사본을 사용하는 point는 생성자, 메서드 (setter,getter)입니다.

> 사실 서비스 로직에서는 방어적 복사가 필요할지..는 모르겠네요 

#### 메서드 시그너처는 신중하게 설계하라 

이번 rule은 자잘한 룰들을 모아놓았습니다.
때문에 그냥 한줄씩 읽고 서로 토론하는 형식으로 진행했으면 합니다 :D 

1. 메서드 이름은 신중하게 지어라

> 당연한 말입니다.

메서드 이름을 신중하게 짓는다는 것은 일반적으로 사용되는 명칭으로 작명하라는 의미입니다.
예를들어, 어떤 객체를 만드는 역할의 함수를 작성할 때, 사용 할 수 있는 단어는 꽤나 많습니다.

    make, build , populate, create, ...

이 중에 일반적으로 사용되는 단어를 사용하라고 합니다.

> 물론 단어마다 풍기는 느낌이 조금씩은 다르긴하네요  

2.편의메서드를 제공하는데 너무 열을 올리지마라.

메서드가 많아질 수록, test 및 유지보수가 어려워지므로 최대한 맡은 일에 충실하게 끔 메서드를 설계하라고 말합니다.

3.인자리스트를 길게만들지마라.

개발자도 사람이다보니, 인자리스트가 길어질 수록 기억을 못할 가능성이 높다고합니다.
때문에 4개이상의 인자를 사용하지 말라고합니다.
인자를 줄이기 위해, 메서드를 쪼개거나 빌더패턴, 또는 helperclass를 사용하라고 합니다.

4.인자의 자료형은 class보다 interface가 좋다

DIP와 연결되는 내용으로 당연한겁니다.

5.인자자료형으로 boolean보다 enum을 쓰는게 좋다.

가독성 때문이라는데, 사실 이 부분은 호불호가 갈릴 수 있을거 같네요.

> 개인적으로 class가 많아지는건 좋게 보진 않아서요 .. 


여기까지가 규칙 40 이였습니다 :D

#### 오버로딩할 떄는 주의하라  

오버로딩은 런타임이 아닌, 컴파일시점에 결정됩니다. 


    public static void main(String[] args) {

        List<Object> list = Arrays.asList(new Integer(5),new String("5"),new Object());

        for(Object obj : list){
            classify(obj);
        }

    }

    public int sum(final int a, final int b) {
        return a + b;
    }

    public static void populateDate(final Date date){
        date.setTime(10000);
        System.out.println(date);
    }

    public static String classify(Integer num){
        System.out.println("num");
        return null;
    }

    public static String classify(String num){
        System.out.println("String");
        return null;
    }

    public static String classify(Object num){
        System.out.println("Object");
        return null;
    }

> classify(Object obj) 를 3번 호출한 결과가 나옵니다.

그 이유는 오버로딩은 compile 시점에서 결정되기 때문입니다.

때문에 혼란을 막기위해 같은 함수 명, 같은 개수의 인자를 같은 함수를 되도록 피하라고 말합니다.  

(생성자에도 같은 문제가 있어, 정적팩터리메서드에 대해 언급한적이 있습니다.)

또한 인자의 수가 같더라도, 인자가 확실히 다르면 혼란을 겪지 않는다고 언급하는데요.

casting이 불가능한 자료형에 대해서 `확실히 다르다`라고 표현합니다.

   public static void main(String[] args) {

        Set<Integer> set = new TreeSet<>();
        List<Integer> list = new ArrayList<>();

        for(int i = -3; i<3; i++){
            set.add(i);
            list.add(i);
        }

        System.out.println(set + " :: " + list);
        
        for(int i = 0; i<3; i++){
            set.remove(i);
            list.remove(i);
        }
        System.out.println(set + " :: " + list);

    }


이 코드의 결과값은 조금 예상하기 어렵습니다.

    -3 , -2 , -1 
    -2 , 0,  2  

이유는 remove 함수에 있습니다.

    remove(int) / remove(integer)

list에는 이 두함수가 존재하며, 각각의 동작 방식은 다르므로 예상치 못한 결과를 불러옵니다.
이는 autoboxing 때문입니다.

> 즉, 확실히 다르다. 는 autoboxing까지 고려한 사항입니다.

`계승관계가 없는 클래스끼리`를 확실히 다르다 라고 말합니다.

#### vararags 는 신중하게 사용하라 

varargs를 사용하면, 배열을 초기화/생성하기 때문에 오버해드가 들게 되고, 사용자 입장에서도 잘못된 코드를 생성할 수 있으므로 사용하지 말아라.  


    public int min(final int ...a) {
        int min = a[0];

        for(int i = 1; i< a.length; ++i){
            if(min > a[i]){
                min = a[i];
            }
        }
        return min;
    }

여기서의 문제는 a 가 1개라면 ? 
컴파일은 문제를 일으키지않고 런타임에 문제를 일으킨다.
이 때문에 varargs만 사용하지 않고, 최소 인자를 함께 받는 방법이 존재합니다.



    public int min(final int first,final int ...arrays) {
       ...
    }

이런식으로 사용하고 있습니다.

> 실제로 다른 api들에서 이렇게 사용하는걸 꽤 본적이 있습니다 :D 

#### null 대신 빈 배열이나 컬렉션을 반환하라 .

null을 반환할 경우, client는 매번 null처리를 진행해야한다.

성능의 문제를 주장할 수 있는데, 책에서는 프로파일링 결과로 나오지 않는 이상, 성능걱정을 하지 말라고 언급합니다.

> 즉, 체계화된 툴이 아닌 추측만으로 성능을 걱정하지 말라고 하는것 같네요 

또한 변경 불가능한 empty-collection을 반환하면 사용자는 조금더 편하게 사용할 수 있으므로 이를 더 지향하라고 언급합니다 .

#### 모든 api 요소에 주석을 달아라 

네 주석 다세요 

> 되도록이면 java doc 으로 ...


끝 ! 