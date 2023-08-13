+++
title = "item09 use를 사용하여 리소스를 닫아라"
date = 2022-08-25T18:21:52+09:00
lastmod = 2022-08-25T18:21:52+09:00
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

Stream, Connection, Socket등의 Resource들은 사용하고, close 메서드를 사용하여 명시적으로 닫아야합니다.
이러한 Resource 들은 Closeable Interface 를 구현하고 있는데, 더 이상 사용하지 않을 경우 명시적으로 close 를 호출해야합니다.

> GC 가 처리하긴 하지만, 굉장히 느리며, GC에 삭제될 동안 리소스를 유지하는 비용이 많이 들어갑니다.

전통적으로 `try-catch` 를 사용하여 resource 를 닫고 있으나, 이는 좋은 방법은 아닙니다.
try 에서 Exception 발생 후, finally 에서 exception 이 또 발생한다면, 둘 중 하나만 전파되기 때문입니다.

Kotlin 에서는 Resource 에 대하여 `use` 함수를 제공합니다.

```kotlin
stream.use { reader ->
        //
}
```

만약 파일을 한줄 씩 읽어야한다면, `useLines` 를 사용할 수 있습니다.
이렇게하면 파일의 내용이 한 줄씩만 유지되므로, 대용량 파일을 적절히 처리할 수 있습니다.

다만, 파일의 줄을 한번만 처리 할 수 있으므로, 2번 이상 처리가 필요할 경우 파일을 2번 읽어야합니다.

`use` 의 내부 구현은 아래와 같습니다.

```kotlin
public inline fun <T : Closeable?, R> T.use(block: (T) -> R): R {
    contract {
        callsInPlace(block, InvocationKind.EXACTLY_ONCE)
    }
    var exception: Throwable? = null
    try {
        return block(this)
    } catch (e: Throwable) {
        exception = e
        throw e
    } finally {
        when {
            apiVersionIsAtLeast(1, 1, 0) -> this.closeFinally(exception)
            this == null -> {}
            exception == null -> close()
            else ->
                try {
                    close()
                } catch (closeException: Throwable) {
                    // cause.addSuppressed(closeException) // ignored here
                }
        }
    }
}
```


#### 결론

use 를 사용하면, closeable 을 구현한 객체를 쉽고 안전하게 처리 할 수 있습니다.


