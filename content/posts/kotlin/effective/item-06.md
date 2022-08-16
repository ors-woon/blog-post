+++
title = "item06 사용자 정의 오류보다는 표준 오류를 사용하라."
date = 2022-08-12T16:18:13+09:00
lastmod = 2022-08-12T16:18:13+09:00
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


가능하다면 직접 오류를 정의하는 것 보다는 최대한 표준 라이브러리의 오류를 사용하는 것이 좋습니다.
많은 개발자가 표준 라이브러리의 오류를 알고 있으므로, 이를 재사용하는것이 좋습니다.

다른 사람들이 API를 더 쉽게 배우고 이해할 수 있으므로, 표준 라이브러리의 오류를 사용하세요.

일반적으로 사용되는 예외는 아래와 같습니다.

```text
- IllegalArgumentException, IllegalStateException
- IndexOutOfBoundsException
- ConcurrentModificationException
- UnsupportedOperationException
- NoSuchElementException
```

