+++
title = "item05 예외를 활용해 코드에 제한을 걸어라"
date = 2022-08-09T10:29:50+09:00
lastmod = 2022-08-09T10:29:50+09:00
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

확실하게 어떤 형태로 동작해야하는 코드가 있다면, 예외를 활용해 제한을 걸어주는 것이 좋습니다.
Kotlin 에서는 다음과 같은 방법을 사용 할 수 있습니다.

- `require` : argument 를 제한 할 수 있습니다.
- `check` : 상태와 관련된 동작을 제한 할 수 있습니다.
- `assert` : 어떤 것이 true 인지 확인합니다. (test 모드에서만 작동)

예시 )

```kotlin
fun pop(num: Int = 1): List<T> {
  require(num <= size) {
    "cannot remove more elements than current size"
  }
  check(isOpen) { "cannot pop from closed stack" }
  val ret = collection.take(num)
  collection = collection.drop(num)

  assert(ret.size == num)
  return ret
}
```

이렇게 제한을 걸어 주면 다양한 장점이 발생합니다.

- 문서를 읽지 않은 개발자도 문제를 확인 할 수 있습니다.
- 문제가 있을 경우, 예외를 리턴합니다. 예상하지 못한 동작을 막을 수 있습니다.
- 코드가 어느 정도 자체적으로 검사되며 관련된 단위 테스트를 줄일 수 있습니다.
- `스마트 캐스트` 기능을 활용 할 수 있게 되므로, cast를 적게 할 수 있습니다.

### argument (require)

함수를 정의할 때, argument 에 제한을 거는 코드를 많이 사용합니다.

> null / size / valid 검사등 argument 를 검사하는 경우는 많습니다.

일반적으로 이러한 제한을 걸때, require 함수를 사용합니다.
`require` 는 제한을 확인 하고, 만족하지 못할 경우 예외를 던집니다.

```kotlin
fun factorial(n: Int): Long {
  require(n >= 0)
  return if (n <= 1) else factorial(n - 1) * n
}

fun findCluster(points: List<Point>): List<Cluster> {
  require(points.isNotEmpty())
}

fun sendEmail(user: User) {
  requireNotNull(user.email)
}
```

이와 같은 형태의 입력 유효성 검사 코드는 함수의 가장 앞부분에 배치되므로, 읽는 사람도 쉽게 확인 할 수 있습니다.
require 함수는 조건을 만족하지 못할 때, 무조건 적으로 IllegalArgumentException 을 발생 시키므로, 제한을 무시할 수 없습니다.

> 만약 공개된 API라면 이러한 제한 조건들을 문서에 기록해두어야합니다.

또한 다음과 같은 방법으로 람다를 활용하여 지연 메시지를 정의할 수 도 있습니다.

```kotlin
require(n >= 0) { "cannot calculate factorial of $n ~" }
```

### 상태

어떤 구체적인 조건을 만족할 때만 함수를 사용 할 수 있게 해야 할 때가 있습니다.

- 초기화 되어 있어야만 처리를 하게 하고 싶은 경우
- 사용자가 로그인 했을때만 처리를 하고 싶은 경우
- 객체를 사용할 수 있는 시점에 사용하고 싶은 경우

상태와 관련된 제한을 걸때는 일반적으로 check 함수를 사용합니다.

```kotlin
fun speak(text: String) {
  check(isInitialized)
}

fun getUser(): User {
  checkNotNull(token)
}
```

check 함수는 require와 비슷하지만 지정된 예측을 만족하지 못할 때, IllegalStatException을 throw 합니다.

> 상태가 올바른지 확인할 때 사용하며, 함수 전체에 대한 어떤 예측이 잇을때는 일반적으로 require 블록 뒤에 배치합니다.

이러한 확인은 사용자가 규약을 어기고, 사용하면 안되는 곳에서 함수를 호출하고 있다고 의심될 때 합니다.

### Assert 계열 함수

함수가 올바르게 구현되었다면, 확실하게 참을 낼 수 있는 코드들이 있습니다.
이러한 구현 문제로 발생할 수 있는 추가적인 문제를 예방하려면, 단위 테스트를 사용하는 것이 좋습니다.

```kotlin
class StackTest {
  @Test
  fun `스택은 정확한 수의 element 를 반환한다.`() {
    val stack = Stack(20) { it }
    val ret = stack.pop(10)
    assertEquals(10, ret.size)
  }
}
```

단위 테스트만으로 확인하기 어려운 경우, 호출 위치에서 제대로 동작하는지 확인 할 수 있습니다.
다음과 같이 함수 내부에서 assert 계열의 함수를 사용하여, 내부 구현에서 검증을 할 수 있습니다.

```kotlin
fun pop() {
  assert(ret.size == num)
}
```

이러한 조건은 -ea JVM 옵션을 활성해야 확인할 수 있습니다.
application 환경에선 throw 를 발생하지 않으며, test 환경에서만 동작하는 블록입니다.

> 만약 심각한 오류일 경우엔 assert 보단 check 를 이용해 예외를 반환하는게 좋습니다.

assert block 의 장점은 실제 코드가 더 빠른 시점에 실패하게 만드는 것입니다.
따라서, 예상하지 못한 동작이 언제 어디서 실행되었는지 쉽게 찾을 수 있습니다.

> 코드를 안정적으로 만들고 싶을 때, 양념처럼 사용할 수 있다는 것을 기억하세요.

### Nullability 와 스마트 캐스팅

코틀린에서 require / check 블록으로 어떤 조건을 확인해서 true 가 나왔다면, 해당 조건은 이후로도 true 일거라 가정합니다.
따라서, 이를 활용하여 타입 비교를 했다면, `smart cast` 가 작동합니다.

```kotlin
fun changeDress(person: Person) {
  require(person.outfit is Dress)  // final 이라면 smartCast !
  val dress: Dress = person.outfit
}
```

이러한 smartCast 는 null 인지 확인할 때 굉장히 유용합니다.

```kotlin
fun valid(value: Value) {}

require(obj != null)
val value = requireNotNull(obj.value) // 변수를 Unpack 하는 용도로도 사용 할 수 있습니다.
valid(obj.value)
```

나아가 Elvis 연산을 이용하여 null 일 경우 return / throw 를 할 수도 있습니다.

```kotlin
val value = obj.value ?: return
val value = obj.value ?: throw Exception()
```

만약 null 처리와 함께 로그를 남겨야한다면, `run` 함수를 이용할 수 도 있습니다.

```kotlin
val value = obj.value ?: run {
  log("value is null")
  return
}
```

이처럼 return 과 throw 를 활용한 Elvis 연산은 nullable을 확인할 때 굉장히 많이 사용되는 관용적인 방법입니다.
적극적으로 활용하는 것이 좋으며, 함수 앞부분에 넣어 잘 보이게 만드는 것이 좋습니다.



