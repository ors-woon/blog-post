+++
title = "item04 inferred 타입으로 리턴하지 말라"
date = 2022-08-09T10:07:17+09:00
lastmod = 2022-08-09T10:07:17+09:00
tags = ["kotlin", "effective"]
categories = ["kotlin"]
readingTime = true # show reading time after article date
comments = false
justify = false # text-align: justify;
single = false # display as a single page, hide navigation on bottom, like as about page.
license = "" # CC License
summary = " "
draft = false
+++

코틀린의 타입 추론 (type inference)은 JVM 세계에서 가장 널리 알려진 코틀린의 특징입니다.
다만, 타입 추론을 사용할 때는 몇가지 위험한 부분들이 있습니다.

위험한 부분을 피하려면, 할당 때 inferred 타입은 `정확하게 오른쪽에 있는 피연산자` 에 맞게 설정된다는 것을 기억해야합니다.

> 절대로 슈퍼 클래스 또는 interface 로는 설정되지 않습니다.

```kotlin
open class Animal

class Zebra : Animal()

fun main() {
  var animal = Zebra()
  animal = Animal() // type mismatch
}
```

일반적인 경우, 타입을 명시적으로 지정해서 이러한 문제를 해결할 수 있습니다.
다만, 라이브러리등 조작할 수 없는 경우, 문제를 간단하게 해결 할 수 없습니다.

> 또한 inferred 타입을 외부에 노출하면 위험한 일이 발생할 수 있습니다.

```kotlin
interface CarFactory {
  fun produce(): Car
}

//
val DEFAULT_CAR: Car = Fiat126P()
```

위같은 Interface 가 있고, 디폴트로 생성되는 자동차가 있다고 생각해봅시다.
대부분의 공장에서 Fiat126P 라는 자동차를 생산하므로, 이를 디폴트로 두었다고 가정합니다.

코드를 작성하다보니, Car 가 명시적으로 지정되어 있어, 함수의 리턴 타입을 제거했다고 합시다.

```kotlin
interface CarFactory {
  fun produce() = DEFAULT_CAR
}
```

이 후, 다른 사람이 코드를 보다가, DEFAULT_CAR 는 타입 추론에 의해 자동으로 지정 될 것이므로, 다음과 같이 코드를 변경했다고 해봅시다.

```kotlin
val DEFAULT_CAR = Fiat126P()
```

이제 문제가 발생했습니다.
CarFactory 에서는 이제 Fiat126P 이외의 자동차를 생산하지 못합니다. `엄청난 worst case ...`

> 만약 Interface 를 다룰 수 있다면 문제를 굉장히 쉽게 수정할 수 있습니다.

하지만 외부 API라면 문제를 쉽게 해결 할 수 없습니다.

Return Type 은 API를 잘 모르는 사람에게 전달해 줄 수 있는 중요한 정보입니다.
따라서 외부에서 확인 할 수 있게 명시적으로 지정해 주는 것이 좋습니다.

### 정리

타입을 확실하게 지정해야하는 경우, 명시적으로 타입을 지정해야한다는 원칙만 갖고 있으면 됩니다.

또한 안전을 위해 외부 API를 만들때는 반드시 타입을 지정하고, 확실한 확인 없이는 제거하지 말기 바랍니다.

> inferred type 은 프로젝트가 진전될 때 제한이 너무 많아지거나, 예측하지 못한 결과를 낼 수 있다는 것을 기억하세요.
