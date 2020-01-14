----
layout: post
title:  self encapsulate field
tags: refactoring
categories: Book
---


```
필드에 직접 접근하던 중 그 필드로의 결합에 문제가 생길 땐,
    읽기 / 쓰기 메서드를 작성하여, 메서드를 통해서만 필드에 접근하게 만들자.
```

위 내용은 Refactoring 책의 8장에 나오는 내용으로 `필드의 자체 캡슐화` 로 소개됩니다.

#### 예제

> 변경 전 코드

```
private int price;
private int count;
private int discount;

public int getAmountPrice() {
    return (price * count) - (price * discount);
}

// getter
// setter
```


클래스 내부, 메서드 안에서 사용되는 변수들은 대부분 위와같이 `직접` 접근하도록 작성됩니다.
이번 chapter 에서는 Refactoring 의 방법 중 하나로, 직접 접근하던 변수를 쓰기 / 읽기 메서드로 `간접` 접근하도록 변경하는 `필드의 자체 캡슐화`에 대하여 설명합니다.

> 변경 후 코드

```
public int getAmountPrice() {
    return (getPrice() * getCount()) - (getPrice() * getDiscount());
}
```

#### 장점


> 1. 공통 로직을 분리할 수 있음

클래스 내부에 선언되어있는 변수에 대하여, 읽기 / 쓰기 메서드를 통해 변수에 접근하게 되면, 공통된 로직들을 따로 분리할 수 있다는 장점이 있습니다.
만약 `price` 를 계산하기 위한 비즈니스 로직이나, 유효성 검증들을 읽기 메서드에서 함으로 코드의 중복을 막을 수 있게 됩니다.

다만, 다른 개발자가 보기에는, 일반 getter / setter 로 인지할 수 있기때문에, 비즈니스 로직/ 유효성 검증이 필요한 메서드 명을 get / set 으로 표기하는건 혼란을 가져올 수 있습니다.

> 2. 하위 클래스들에 대해 재정의 가능

상위 클래스에서 `간접 접근`을 통해 비즈니스 로직이 구현될 경우, 하위 클래스에서 읽기 / 쓰기 메서드를 재정의하여 로직을 완성시킬 수 있습니다.

> 다형성을 이용하여 로직을 구현 할 수 있습니다.


#### 여담

개인적으로, 상속을 이용한 구조가 아니라면 나서서 사용할것 같진 않은 refactoring 방식입니다.
아직은, `직접 변수 접근`이 더 괜찮아보이네요 :)


lusiue@gmail.com
