---
layout: post
title:  self encapsulate field
tags: refactoring
categories: Book
---

Refactoring 책의 8장에 나오는 내용으로 `필드의 자체 캡슐화` 로 소개된다.

책의 내용을 한줄로 요약하자면..

> 클래스 내부에서 사용하는 필드를 `private`으로 바꾸고 `setter/getter`로 접근해라 !

여기까진 "당연한 내용이지!" 싶지만, private 클래스 내부에서도 `setter/getter`를 쓰라고 말한다.


> 주의 

아래 나오는 코드들은 즉흥적으로 작성한 것으로 억지가 있을 수 있습니다.   
코드를 너무 깊게 보시진 마시고, `상황`을 생각해주세요 :)


#### 예제

```
private int price;
private int count;
private int discount;

public int getAmountPrice() {
    return (price * count) - (price * discount);
}


// 이하 setter/getter
```

> 예제를 생각하다가 간단하고 보편적인 예제가 Product 라서 ..

대부분의 코드는 위같이 작성될것이다. 외부 객체는 내부 값에 직접 접근하지 못해야하니까!
하지만, 이번 장에서는 한걸음 더 나아가 아래처럼 코드를 작성할 수도 있다고 말한다.

> 여기서 중요한점은, 작성을 할 수도 있다는 점이다. (해야만 하는 건 아니다.)


```
public int getAmountPrice() {
    return (getPrice() * getCount()) - (getPrice() * getDiscount());
}
```


이 챕터를 처음 봤을 때, 반감을 갖고있었다.

`"저렇게 사용하는 경우가 어딧어 약팔고 있네 .."`

> 지금 난 약을 팔거다.

#### 약을 팔자


위 방법을 사용하는 경우가 언젤까 `?` 곰곰히 생각해보자.   

가장 먼저 떠오른 상황은 변수에 제약을 가하고 싶을 때이다.

> 가정

오늘부터 할인 률은 최대 50% 까지 적용된다.

위 같은 가정이 주어졌을 때, 직접접근을 사용할 경  `discount`가 사용되는 모든 곳을 찾아, 유효성 검사를 하는 로직을 추가해야한다.

하지만, class 내부에서도 setter/ getter가 사용되고 있다면, getter 로직만 수정하면 된다.

```
public double getAmountPrice() {
    return (getPrice() * getCount()) - (getPrice() * getDiscount());
}

public double getDiscount() {
    if(discount > LIMIT_RATE){
        return LIMIT_RATE;
    }
    return discount;
}
```

> 위 이유만으론 약을 사갈것 같진 않다.

#### 약을 조금 더 팔자

이번에 팔 약은 `상속`이다.

> 가정

Product 내부에 할인 최대 비율이 제각각 다르다고 가정하자.
신발 은 30%, 의류는 40% , 모자는 50% 으로로 가정하자.

그럴 경우, 상위 클래스를 수정할 필요없이, 아래처럼 getter를 재정의하면 된다.

```
// 전략 패턴 느낌
public class Cloth extends Product{

    public static final double LIMIT_RATE = 0.4;

    @Override
    public double getDiscount(){
        if (super.getDiscount() > LIMIT_RATE) {
            return LIMIT_RATE;
        }
        return super.getDiscount();
    }
}
```

getDiscount를 재정의 함으로, 상위 로직에서 사용되는 discount를 직접 변경할 수 있다는 장점이 있다.



#### 여담

Effective Java 의 내용중 이런 내용이있다.

```
재정의 가능한 함수를 객체 초기화에 사용하지 마라
```

class 가 final이 아니고, 함수의 접근 제한자가 public / protected 라면 객체 초기화시에는 사용해선 안된다는 말이다.

> 하위 함수에서 재정의 할 경우, 예상치 못한 동작을 할 수 있기 때문이다.

이번 챕터 마지막에 짤막하게 나오는데, 문득 생각이 나서 적어봤다. :)
lusiue@gmail.com    
