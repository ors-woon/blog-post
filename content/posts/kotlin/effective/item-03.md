+++
title = "item03 최대한 플랫폼 타입을 사용하지 말라"
date = 2022-08-06T19:46:53+09:00
lastmod = 2022-08-06T19:46:53+09:00
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

코틀린의 주요 기능 중 하나는 null-safe 입니다.
자바에서 자주 볼 수 있었던 NPE 는 코틀린에서 null-safety 메커니즘으로 인해 거의 찾아보기 힘듭니다.
다만, 자바나 C 등의 프로그래밍 언어와 코틀린을 연결해서 사용할 때는 이러한 예외가 발생 할 수 있습니다.

만약 Java 에서 String 을 리턴하는 메서드가 있을 경우, 코틀린에서 이를 어떻게 처리해야할까요 ?

- String? => Nullable annotation 이 있을 경우
- String => Nonnull annotation 이 있을 경우

만약 다음과 같이 annotation 이 붙어 있지 않다면, 어떻게 처리해야할까요 ?

```java

public class JavaTest {
  public String giveName() {
  }
}
```

자바에서는 모든 것이 Nullable 이므로, 최대한 안전하게 다루기 위해서는 nullable 로 가정하고 다뤄야합니다.
하지만, null 이 아님이 확신 가능한 경우 non-null 단정을 나타내는 !! 을 붙여 회피할 수 있습니다.

> 실제 코드를 보면 !! 을 많이 볼 수 있습니다.

nullable 과 관련하여 자주 문제가 되는 부분은 자바의 제네릭타입 입니다.
자바 API 에서 `List<User>`를 리턴하고, annotation 이 따로 붙어 있지 않은 경우 코틀린이 디폴트로 모든 타입을 nullable로 다룬다면 어떤일이 발생할까요?

```java
public class UserRepo() {
  public List<User> getUser() {

  }
}
```

> kotlin

```kotlin
val users: List<User> = UserRepo().user!!.filterNotNull()
```

List 뿐 아니라, 내부 Item 들에 대해서도 null check 를 진행해야합니다. 더 나아가서 Collection들이 중첩으로 사용 될 경우, 코드는 더 복잡하게 변합니다.

```kotlin
val user: List<List<User>> = UserRepo().groupedUsers!!.map { it!!.filterNotNull() }
```

이러한 문제를 피하고자, 코틀린에서는 다른 프로그래밍 언어에서 넘어온 타입들을 `플랫폼 타입`으로 특수하게 다룹니다.

`플랫폼 타입`은 타입 으름 뒤에 `!` 기호를 붙여서 표기합니다.

> 플랫폼 타입이란 nullable 인지 아닌지 알 수 없는 타입을 의미하며, 직접적으로 코드에 나타나지 않습니다.

```kotlin
val repo = UserRepo()
val user1 = repo.user // 타입은 User!
val user2: User = repo.user // Nonnull
val user3: User? = repo.user // Nullable
```

위처럼 코드를 사용할 수 있으므로, 이전에 언급했던 문제는 사라집니다.
다만, null 이 아니라고 표현했던 타입이 null 일 가능성이 있으므로 여전히 위험한 부분이 존재합니다.

따라서 함수가 지금 당장 null을 리턴하지 않더라도, 미래에 변경될 수 있다는것을 염두해둬야하며 주석 혹은 annotation등을 이용해 이를 표시해두어야합니다.

> 자바를 코틀린과 함께 사용할 때, 자바 코드를 직접 조작할 수 있다면 annotation을 사용하여 이를 방지해야합니다.

지원하는 annotation 은 [다음](https://kotlinlang.org/docs/java-interop.html#nullability-annotations)과 같습니다.

### PlatformType 을 제거해야하는 이유

이와 같은 플랫폼 타입은 안전하지 않으므로 최대한 빨리 제거해야합니다.

```java
public class JavaClass {
  public String getValue() {
    return null;
  }
}
```

```kotlin
fun statedType() {
  // (1)
  val value: String = JavaClass().value

  print(value.length)
  // (2)
  val value = JavaClass().value

  println(value.length)
}
```

두가지 경우 모두 NPE 가 발생하지만, 오류의 발생 위치에 차이가 있습니다.

`(1)`은 값을 가져오는 부분에서 NPE 가 발생합니다. 따라서 코드를 굉장히 쉽게 수정할 수 있습니다.
다만, `(2)`는 실제 값을 사용할때 NPE 가 발생하게 됩니다. 선언 시점과 사용 시점이 차이가 날 경우 원인을 파악하는데 오랜 시간이 소요될 수 있습니다.

이러한 플랫폼 타입은 더 많은 위험 가능성을 갖고 있는데, interface + inferred 타입 (추론된 타입) 이 사용 될 경우, 더 큰 혼란을 야기할 수 있습니다.

```kotlin
interface UserRepo {
  fun getUserName() = javaClass().value // inferred (platformType)
}

class RepoImpl : UserRepo {
  override fun getUserName(): String {
  }
}

class RepoImpl2 : UserRepo {
  override fun getUserName(): String? {
  }
}
```

위처럼 상속 받은 하위 클래스에서 nullable 여부를 지정할수 있기때문에, 실제 설계와는 다른 혼란을 발생시킬 수 있습니다.

### 결론

이러한 예시들처럼 플랫폼 타입이 전파되는 일은 굉장히 위험합니다.
따라서, 안전한 코드를 원한다면 이러한 부분을 제거하는 것이 좋습니다.

