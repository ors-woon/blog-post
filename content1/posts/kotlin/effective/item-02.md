+++
title = "item02 변수의 스코프를 최소화하라"
date = 2022-08-05T16:56:57+09:00
lastmod = 2022-08-05T16:56:57+09:00
tags = ["kotlin", "effective"]
categories = ["kotlin"]
readingTime = true # show reading time after article date
comments = false
justify = false # text-align: justify;
single = false # display as a single page, hide navigation on bottom, like as about page.
license = "" # CC License
draft = false
summary = " "
+++

상태를 정의할때 변수와 프로퍼티의 스코프를 최소화하는 것이 좋습니다.

- property 보단 지역 변수를 사용하는 것이 좋습니다.
- 최대한 좁은 스코프를 갖게 변수를 사용합니다.

scope 라는 것은 요소를 볼 수 있는 컴퓨터 프로그램 영역입니다.
kotlin은 block scope 로, 내부 scope 에서 외부 scope에 있는 요소에만 접근할 수 있습니다.

> 반대로 외부에서 내부 scope 로는 접근 할 수 없습니다.

```kotlin
val a = 1
fun fizz() {
  val b = 2
  print(a + b)
}

val buzz = {
  val c = 3
  print(a + c)
}

// 여기서 a 를 사용할 수 있으나, b/c 는 사용하지 못함
```

최대한 변수는 scope를 좁게 설정하는것이 좋습니다.
가장 큰 이유는 추척 / 관리하기 쉬워지기 때문이며, immutable 을 선호하는 이유와 비슷합니다.

scope 범위가 넓을 경우, 변수가 잘못 사용될 여지가 있으며 코드가 길어질 수 록 이해하기가 어려워집니다.

> 변수는 정의할때 초기화 되는 것이 가장 좋습니다.

`if, when, try-catch, Elvis` 표현식등을 사용하면 최대한 변수를 정의할때 초기화 할 수 있습니다.

```kotlin

// worst
val user: User
if (hasValue) {
  user = getValue()
} else {
  user = User()
}

// best
val user: User = if (hasValue) {
  getValue()
} else {
  User()
}
```

여러 property 를 한꺼번에 설정해야하는 경우, destructuring declaration 을 활용하는 것이 좋습니다.

```kotlin
// worst
fun updateWeather(degrees: Int) {
  val description: String
  val color: Int
  if (degrees < 5) {
    description = "color"
    color = Color.BLUE
  } else if (degrees < 23) {
    description = "mild"
    color = Color.YELLOW
  } else {
    description = "hot"
    color = Color.RED
  }
}

// best ?
fun updateWeather(degrees: Int) {
  val (description, color) = when {
    degrees < 5 -> "cold" to Color.BLUE
    degrees < 23 -> "mild" to Color.YELLOW
    else -> "hot" to Color.RED
  }
}
```

결론적으로 변수의 스코프가 넓으면 굉장히 위험합니다.

## 캡처링

예시로 나오는 알고리즘은 에라토스테네스의 체 (소수를 구하는 알고리즘) 입니다.

- 2부터 시작하는 숫자 리스트를 만든다.
- 첫번째 요소를 선택하고, 이는 소수이다.
- 남아있는 숫자 중, 2번에서 선택한 소수로 나눌 수 있는 모든 숫자를 제거합니다.

```kotlin
var numbers = (2..100).toList()
val primes = mutableListOf<Int>()

while (numbers.isNotEmpty()) {
  val prime = numbers.first()
  primes.add(prime)
  number = numbers.filter { it % prime != 0 }
}
print(primes)
```

이를 시퀀스로 확장하면, 아래처럼 구현할 수 있습니다.

```kotlin
val primes: Sequence<Int> = sequence {
  var numbers = generateSequence(2) { it + 1 }

  while (true) {
    val prime = numbers.first()
    yield(prime) // 다음 호출시 까지 stop (lazy evaluation)
    numbers = numbers.drop(1).filter { it % prime != 0 }
  }
}

print(primes.take(10).toList()) // [2,3,5,7,11,13,17,19,23,29]
```

위 코드에서 prime 의 scope 를 한 단계 올리면, 이상한 결과가 나오게 됩니다.

```kotlin
val primes: Sequence<Int> = sequence {
  var numbers = generateSequence(2) { it + 1 }

  var prime: Int
  while (true) {
    prime = numbers.first()
    yield(prime)
    numbers = numbers.drop(1).filter { it % prime != 0 }
  }
}

print(primes.take(10).toList()) // [2,3,5,6,7,8,9,10,11,12]
```

위처럼 이상한 결과가 나온 이유는 capturing 때문입니다. 반복문 내부에서 prime 으로 숫자를 필터링하는데, 지연 연산으로 filter 가 지연됩니다.

따라서 최종적인 prime 값으로만 filtering 이 진행되고, 2로 설정되어있을때의 4 값만 제외된 결과가 나옵니다.

이러한 예상하지 못한 결과가 나올 수 있으므로, 항상 잠재적인 capture 문제를 주의해야합니다.

#### 해설

```text
start: 2
prime : 2

return: 2

numbers = numbers.drop(1)
.filter { it % prime != 0 }

prime = 3 => first()   // 종단 연산  drop - 2, first - 3
return = 3

numbers = numbers.drop(1) drop 2
.filter { it % prime != 0 } return 4/ 5 (3 % 4 / 3 % 5)
.drop(1) -> drop 4
.filter { it % prime != 0} -> 3 % 5

prime = 5 => first()
return = 5

numbers = numbers.drop(1) drop 2
.filter { it % prime != 0 } return 3/ 4 / 5 (3 % 5, 4 % 5, 6 % 5)
.drop(1) -> drop 3
.filter { it % prime != 0} -> return 4/ 6 (4 % 5, 6 % 5)
.drop(1) -> drop 4
.filter { it % prime != 0} -> 6

prime = 6 => first()
... (중략)
```

위처럼 캡처링이 사용 될 경우, 예상하지 못한 값이 반환 됩니다.


## 결론

여러 가지 이유로 변수의 scope 는 좁게 만들어서 활용하는 것이 좋습니다.

> 또한 var 보단 val 을 사용하는게 좋습니다. + 변수는 선언과 동시에 초기화되는 것이 좋습니다.

람다에서 변수를 캡처한다는 것을 꼭 기억해야합니다.
