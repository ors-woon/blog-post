+++
title = "item08 적절하게 null을 처리하라"
date = 2022-08-16T23:48:02+09:00
lastmod = 2022-08-16T23:48:02+09:00
tags = []
categories = []
readingTime = true # show reading time after article date
comments = false
justify = false # text-align: justify;
single = false # display as a single page, hide navigation on bottom, like as about page.
license = "" # CC License
summary = " "
draft = false
+++

null 은 `값이 부족하다(lack of value)` 는 것을 나타냅니다.

- property 가 null이라는 것은 값이 설정되지 않았거나, 제거되었다는 것을 나타냅니다.
- 함수가 null을 리턴한다는 것은 함수에 따라서 여러 의미를 가질 수 있습니다.
  - `String.toIntOfNull()`은 String 을 Int로 적절하게 변환할 수 없을 경우 null을 리턴합니다.
  - `Iterable<T>.firstOrNull(()-> Boolean)` 은 주어진 조건에 맞는 요소가 없을 경우 null을 리턴합니다.

이처럼 null은 최대한 명확한 의미를 갖는 것이 좋습니다.

기본적으로 nullable 타입은 세 가지 방법으로 처리합니다.

- `?.`, `smartCasting`, `Elvis` 연산자 등을 활용해서 안전하게 처리한다.
- 오류를 throw 한다.
- 리팩터링하여, nullable Type 이 나오지 않게한다.

#### null 을 안전하게 처리하기

null 을 안전하게 처리하는 방법으로는 `safe call` 과 `smart casting` 이 있습니다.

```kotlin
printer?.print() // safe call
if (printer != null) printer.print() // smart casting
```

위 방법 외에도 Elvis 연산을 사용 할 수 있습니다.

```kotlin
val printerName1 = printer?.name ?: "Unnamed"
val printerName2 = printer?.name ?: return
val printerName3 = printer?.name ?: throw Error("printer must be named")
```

또한, 많은 객체가 Nullable 과 관련된 처리를 지원합니다.

```kotlin
Collection<T>?.orEmpty() // ?. 확장함수로 정의되어, null 일때도 호출 할 수 있습니다.

// 구현
inline fun <T> List<T>?.orEmpty(): List<T> = this ?: emptyList()
```

`smart casting`은 코틀린의 규약 기능(contracts feature)을 지원합니다.
이 기능을 사용하면, 다음 코드처럼 smart casting 을 사용할 수 있습니다.

> contract block 을 이용하여, compiler 에게 정보를 전달하는 방식으로 구현되어 있습니다.

```kotlin
val name = readLine() // nullable
if (!name.isNullOrBlank()) {
  print("Hello ${name.uppercase()}")
}

val news: List<News>? = getNews()
if (!news.isNullOrEmpty()) {
  news.forEach { }
}
```

#### 오류 throw 하기

위처럼 null 을 처리할 경우, printer 가 null 일때 개발자에게 알리지 않고 코드가 그대로 진행됩니다.
만약 printer 가 null 이 되리라 예상하지 못했다면, 이후 로직이 실행되지 않아 이상할 것입니다.

이는 개발자가 오류를 찾기 어렵게 만드며, 전파가 필요한 경우 오류를 강제로 발생시켜주는 것이 좋습니다.

> throw, !! , requireNotNull , checkNotNull 등을 활용 할 수 있습니다.

`item07`에서 설명했듯, `exception` 혹은 `null or Failure` 중 어느 방식을 사용할지는 고민이 필요합니다.

#### not-null assertion 과 관련된 문제 `!!`

null 처리를 하는 가장 간단한 방법은 `not-null assertion(!!)` 을 사용하는 것입니다.
이는 Java 에서 nullable 을 처리할때 발생할 수 있는 문제가 똑같이 발생합니다.

> NPE 가 발생하며, 에러 로그도 친절하지 않습니다.

미래에 코드가 어떻게 변화할지 아무도 알 수 없으며, 때문에 !! 연산자는 최대한 사용을 피해야합니다.

가능하다면 lateinit / Delegates.notNull 과 같은 방법을 통해 `!!` 을 제거해야합니다.

> !! 연산자를 보면 반드시 조심하고, 무언가가 잘못되어 있을 가능성을 생각합시다.

#### 의미없는 nullability 피하기

null 처리는 어떻게는 적절하게 처리되어야하므로, 추가 비용이 발생합니다.
따라서, 필요한 경우가 아니라면, Nullability 자체를 피하는 것이 좋습니다.

null은 개발자에게 중요한 메시지를 전달하는데 사용할 수 있으므로, 의미없는 null은 피하는 것이 좋습니다.

nullability 를 피할때 사용 할 수 있는 몇가지 방법은 아래와 같습니다.

- class 에서 nullability 에 따라 여러 함수를 제공할 수 있습니다. `getOrNull`
- 어떤 값이 클래스 생성 이후 확실하게 설정된다면, lateinit 같은 방식을 사용하세요.
- 빈 컬렉션 대신 Null 을 리턴하지 마세요.

#### lateinit 과 notNull delegate

클래스가 생성 중 초기화 할 수 없는 프로퍼티를 가지는 것은 종종 발생합니다.

> spring 혹은 junit 등 DI 관련 framework 에서 자주 발생합니다.

이때마다 프로퍼티를 null 로 설정하는 것보단, lateinit 한정자를 사용하는 것이 더 바람직합니다.
물론 lateinit 도 사용 시점에 초기화가 되지 않을 경우, 예외를 발생시킵니다. 다만 정상적인 상황이 아니므로, 개발자에게 전파가 필요하며 이는 올바른 에러처리로 볼 수 있습니다.

lateinit 은 null 과 비교해서 다음과 같은 차이가 있습니다.

- !! 연산자로 Unpack 하지 않아도 됩니다.
- 프로퍼티가 초기화된 이후, null 로 주입될 수 없습니다.

다만, primitive type 에는 lateinit 을 사용할 수 없으므로 lateinit 보단 약간 느린 Delegates.notNull 을 사용 할 수 있습니다.

```kotlin
 private var doctorId: Int by Delegates.notNull()
```

#### 여담) 왜 primitive Type 은 lateinit 을 지원하지 않는가 ?

kotlin 에서 primitive type 정의 시, non-null type 은 java 의 primitive 로 변환됩니다.

> nullable type 은 Boxing Type 로 정의됩니다.

lateinit 의 구현은, null을 받을 수 있는 타입 (object)에 Null 이 들어오면 exception을 터뜨립니다.
다만, java primitive 는 null 주입이 안되므로, kotlin 의 primitive type은 lateinit을 지원하지 않습니다.
