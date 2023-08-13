+++
title = "item07 결과 부족이 발생할 경우 null과 Failure를 사용하라"
date = 2022-08-12T16:21:51+09:00
lastmod = 2022-08-12T16:21:51+09:00
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

함수가 원하는 결과를 만들어 낼 수 없을때, 2가지 방법을 사용 할 수 있습니다.

1) null 또는 실패를 나타내는 sealed class 를 리턴한다.
2) 예외를 throw 한다.

2번째 방법인 예외는 정보를 전달하는 방법으로 사용해서는 안되며, 잘못된 특별한 상황을 나타내도록 처리해야합니다.

예외는 많은 개발자가 예외 전파되는 과정을 제대로 추적하기 어렵습니다. 또한 kotlin 의 모든 예외는 unchecked 이므로, 사용자가 예외를 처리하지 않을 수 있습니다.
마지막으로 try-catch 블록 내부에 코드를 배치하면, 컴파일러가 할 수 있는 최적화가 제한됩니다.

반면, null 과 sealed class (Failure)는 예상되는 오류를 표현할때 굉장히 좋습니다.

> method 를 사용하는 사람에, 실패 상황을 명시적으로 표현할 수 있습니다.

따라서 충분히 예측할 수 있는 범위의 오류는 Null 과 Failure 를 사용하고, 예측하기 어려운 상황에 대해서는 throw 를 처리하는 것이 좋습니다.

추가적인 정보를 전달해야 할 경우 Failure 를 사용하며 그렇지 않으면 null 을 사용하는 것이 일반적입니다.

만약 null 을 사용한다고하면, getOrNull 과 같은 무엇이 리턴되는지 예측할 수 있게 하는 것이 좋습니다.

#### 추가적으로 ..

Kotlin 은 Result 라는 class 를 제공하는데, 결과를 캡슐화하여, 다른 곳에서 처리할 수 있도록하는 class 입니다.

```kotlin
fun getApi(): Result<String> {
  return try {
  } catch (e: Exception) {
    Result.failure(Exception("text"))
  }
}

// method 호출부에서 exception 처리가 가능
val response = getApi().getOrElse { "default" }
```

Result 를 사용 시, 비즈니스 로직과 관련된 예외 처리를 infra 가 아닌 service layer 에서 (쉽게) 진행 할 수 있습니다.
