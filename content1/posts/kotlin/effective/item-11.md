+++
title = "item11 가독성을 목표로 설계하라"
date = 2022-08-25T18:38:14+09:00
lastmod = 2022-08-25T18:38:14+09:00
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

개발자는 어떤 코드를 작성하는 것보다, 읽는 데 많은 시간을 소모합니다.
때문에 항상 가독성을 생각하면서, 코드를 작성해야합니다.

#### 인지 부하 감소

가독성은 사람에 따라 다르게 느낄 수 있습니다. 하지만, 일반적으로 많은 사람의 경험과 인식으로 만들어진 어느정도의 규칙이 있습니다.

```kotlin
// A
if (person != null && person.isAdult) {
  view.showPerson()
  return
}
view.showError()

// B
person?.takeIf { it.isAdult }
  ?.let(view::showPerson) ?: view.showError()
```

위 코드 중, 어느 구현이 더 읽기 쉽다고 생각하나요? 더 짧다는 이유로 B 를 골랏다면 좋은 대답이 아닙니다.
B는 일단 읽고 이해하기 어렵습니다.

`가독성` 이란, 코드를 읽고 얼마나 빠르게 이해할 수 있는지를 의미합니다.
이는 우리의 뇌가 얼마나 많은 관용구에 익숙해져 있는지에 따라 다릅니다. 코틀린을 많이 접하지 못한 사람에게는 A가 더 읽기 쉬우며,
숙련된 사람이라면 B를 더 쉽게 읽을 수 도 있습니다.

다만, 숙련된 개발자만을 위한 코드는 좋은 코드가 아닙니다.

또한 A 의 코드가 더 수정하기 쉬우며, 직관적입니다.

만약 위 코드에 기능이 추가되었다고 가정해봅시다.

```kotlin
// A
if (person != null && person.isAdult) {
  view.showPerson()
  view.hideProgressWithSuccess()
  return
}
view.showError()
view.hideProgress()

// B
person?.takeIf { it.isAdult }
  ?.let {
    view.showPerson()
    view.hideProgressWithSuccess()
  }
  ?: run {
    view.showError()
    view.hideProgress()
  }
```

B의 경우, 기능이 추가되면서 버그가 발생합니다.

> `let` 의 마지막 줄이 null 을 return 할 경우, 아래 run 함수를 수행하게 됩니다.

이처럼, 악숙하지 않은 구조를 사용하면 잘못된 동작을 확인하기 어렵습니다.
구성원 모두가 코틀린 전문가가 아닐 수 있으며, 가독성을 위해 조금 더 직관적인 구조로 코드를 짜야합니다.

보편적으로 사용되는 패턴을 사용하세요.

#### 극단적이 되지 않기

위 케이스로, `let 을 절대로 사용하면 안된다.` 라고 받아들여서는 안됩니다.
let 은 좋은 코드를 만들기 위해서 다양하게 사용되는 인기 있는 관용구 입니다.

> safety call 혹은 함수 chaining 등에서 param 에 이름을 지어주기 위해 자주 사용되는 함수로 자주 사용됩니다.

let 과 같은 Scope Function 들은 경험이 적은 코틀린 개발자에게 이해하기 어려울 수 있습니다.
다만, 이 비용은 지불한 가치가 있으므로 사용해도 좋습니다.

문제가 되는 경우는, 비용을 지불할 만한 가치가 없는 코드에 사용되는 경우입니다.

> 정당한 이유 없이 복잡성이 추가되는 경우에는 사용을 피하는게 좋습니다.

물론 비용에 대해 각자 생각하는 기준이 다를 수 있기때문에 팀원들끼리의 기준을 맞추는 것 또한 중요합니다.

#### 컨벤션

사람에 따라서 가독성에 대한 관점이 다르다는 것을 알아보았습니다.
관점을 최대한 맞추기 위하여, 컨벤션을 정해놓고 코드를 작성해야합니다.

필자가 생각하는 최악의 코드는 아래와 같습니다.

```kotlin
val abc = "A" { "B" } and "C"
print(adb) // ABC
```

위 처럼 함수를 작성하기 위해 invoke / infix 함수를 구현해야하는데, 이는 이후 설명하는 수많은 규칙들을 위반합니다.

> 이후 item 들에서 하나씩 설명합니다.





