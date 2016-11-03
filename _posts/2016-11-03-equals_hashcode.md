---
layout: post
title:  "equals 와 Hashcode [1] "
date:   2016-11-03 10:32:00 +0200
tags: ['Java', 'equals']
author: "Jang chulwoon"
---


## hashCode 와 equals [1]

hashCode()와 equals() method 는 Object에 속해있는 method 입니다.    
Java에서 Object class 는 모든 클레스의 superclass 입니다.   
때문에 Java의 모든 class 는 Object의 method를 구현하고 있습니다.   
자세한 내용은 [Java doc]('https://docs.oracle.com/javase/8/docs/api/')을 참고해주세요.   

## equals()   

equals() method 는 동등성을 비교 할 때 사용됩니다.   
간략하게 정리하면 equals()는 다른 객체가 특정 객체와 동일한가를 구현한 method 입니다.     Java doc에서는 equals()의 설명을 다음과 같이 정리하였습니다.   
   
`The equals method implements an equivalence relation on non-null object references:`   

equals method는 null값이 아닌 object 간의 **동치성**을 구현하는 method 입니다.    
또한 equals method를 재정의 하려 한다면, 동치성을 만족시켜야 합니다.   

동치성을 만족하기 위한 몇가지 특징들은 다음과 같습니다.   
(어찌보면 당연한 내용일 수 있습니다 ..)

1. 재귀    
`It is reflexive: for any non-null reference value x, x.equals(x) should return true.`      
null 이외의 참조값 x 에 대해 x.equals(x)는 항상 true 여야만 합니다.  

2. 대칭 
`It is symmetric: for any non-null reference values x and y, x.equals(y) should return true if and only if y.equals(x) returns true.`   
null 이외의 참조값 x 와 y 에 대해 y.equals(x)가 true 이면 x.equals(y) 또한 true가 되야 합니다. 
 
3. 이적   
`It is transitive: for any non-null reference values x, y, and z, if x.equals(y) returns true and y.equals(z) returns true, then x.equals(z) should return true. `    
null 이외의 참조값 x , y ,z 에 대해, x.equals(y)가 ture를 반환하고 y.equals(z)가 ture 반환하면 x.equals(z) 또한 ture를 반환해야 합니다. 
   
4. 일관   
`It is consistent: for any non-null reference values x and y, multiple invocations of x.equals(y) consistently return true or consistently return false, provided no information used in equals comparisons on the objects is modified.`       
null 이외의 참조값 x , y 에 대해, equals를 통해 비교되는 정보에 수정이 없다면, x.equals(y)의 호출의 결과는 계속해서 true나 false여야합니다.       
(정보의 변화가 없다면 반환 값은 항상 일관되어져야 합니다.)    

5. null    
`For any non-null reference value x, x.equals(null) should return false.`  
null이 아닌 참조값 x에 대해, x.equals(null)은 항상 false 입니다.  

코드 없이 언어로만 정리하면 당연한거 아닌가? 라는 생각이 듭니다. (저만 그런가요 ?)    
코드와 함께 정리해보겠습니다 ...   

### Object 의 equals()    

들어가기 앞서 , effective Java 에서는 equals method 를 재정의 하지 않아도 되는 경우를 다음과 같이 설명했습니다.  

1. 각 객체가 고유한 경우. (ex Singleton )
2. 상위 클래스에서 재정의한 equals method가 하위클래스에서 사용하기 적절한 경우.
3. class가 private 또는 package-private 로 선언되있고, equals 를 호출 할 일이 없는 경우.
4. **논리적 동일성** 검사 방법이 있건 없건 상관없는 경우.

> 논리적 동일성을 지원하는 클래스일 경우엔, equals를 재정의하는게 바람직하다고 말합니다.   
> ( 대표적으로 Integer / Date 클래스가 이에 해당한다고 합니다. )       
> 논리적 동일성은 객체가 동일한가?가 아닌 객체 안에 있는 값이 동일한가를 의미합니다.

위 조건을 만족하지 않는다면 , 재정의하는 것을 생각해 봐야합니다.   

그렇다면 , 어떻게 재정의를 해야할까요 ? 


object의 equals method   

```
	 public boolean equals(Object obj) {
        return (this == obj);
    }
```

Integer의 equals method     

```
    public boolean equals(Object obj) {
        if (obj instanceof Integer) {
            return value == ((Integer)obj).intValue();
        }
        return false;
    }
```


Integer의 equals 를 보면 우선 인자의 obj가 Integer type 인지 확인을 한 후,   
맞다면, 기존 객체에 저장된 값과 넘어온 인자의 값이 같으면 true를 반환하는 방식입니다.

~ ing ..    
( equals를 잘못구현한 예제를 찾아보고 있습니다. )





참조   
[1] [Java doc]('https://docs.oracle.com/javase/8/docs/api/')     
[2] [건실성실착실 3실 청년!](`http://egloos.zum.com/iilii/v/3999066`)   
[3] effective Java 



 