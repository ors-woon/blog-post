+++
title = "item01 가변성을 제한하라"
date = 2022-08-02T13:39:03+09:00
lastmod = 2022-08-02T13:39:03+09:00
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

kotlin property 중 일부는 상태(state)를 가질 수 있습니다. 
대표적으로 `var` 을 사용하거나, `mutable` 객체를 사용하면 상태를 갖을 수 있습니다.

```kotlin
var a = 0
var list : MutableList<Int> = mutableListOf()
```

이처럼 property 가 상태를 갖는 경우, 해당 요소의 동작은 사용 방법 뿐아니라, history 에도 의존하게 됩니다.

```kotlin

var property = 0

fun add(count:Int) {
    property += count
}
```

이처럼 상태를 갖게 하는 것은 양날의검입니다. 시간의 변화에 따라 요소를 표현할 수 있다는 것은 유용하나, 이를 적절히 관리하는 것은 생각보다 꽤 어렵습니다.

- 1. 프로그램을 이해하고 디버그하기가 어려워집니다. 
  - 상태 변경을 추적하는 것이 어려우며, 이해 및 유지보수하기가 어려워집니다.
- 2. 가변성이 있으면 코드의 실행을 추록하기 어려워집니다. 
- 3. 멀티 스레드 환경일때, 적절한 동기화가 필요합니다.
- 4. 테스트를 하기가 어려워집니다.
- 5. 정렬등을 사용하는 경우, 상태 추가 시 정렬 로직을 다시 수행해야합니다.

> 변할 수 있는 지점이 줄어들 수록 사람이 코드를 이해하기 쉬워집니다.

가변성은 생각보다 단점이 많아 이를 제한하는 언어들도 있습니다. (haskell)   
다만 이러한 언어는 너무 많은 제한이 있어, program을 작성하기 어려우며, 주류로 사용되지는 않습니다.   

가변성은 시스템의 상태를 나타내기 위한 중요한 방법이나, 유지보수를 위해 변경이 일어나야 하는 부분을 신중하고 확실하게 결정하고 사용해야합니다.


## 코틀린에서 가변성 제한하기

코틀린은 가변성을 제한 할 수 있게 설계되어 있습니다. immutable 객체를 만들거나, property를 변경할 수 없게 막는 것이 굉장히 쉽습니다.
이를 위해 많은 방법을 사용할 수 있으나, 많이 사용되는 것은 아래와 같습니다.

- 읽기 전용 property (val)
- 가변 컬렉션과 읽기 전용 컬렉션 구분하기
- 데이터 클래스의 copy 


#### 1. 읽기 전용 프로퍼티 (val)

코틀린은 val 을 사용해 읽기 전용 프로퍼티를 만들 수 있습니다.   
이렇게 선언된 프로퍼티는 value 처럼 동작하며, 일반적인 방법으로는 값이 바뀌지 않습니다.   
다만, 완전히 변경 불가능한 것은 아니라는 것을 주의하기바랍니다. 

> mutable 객체를 담고 있다면, 내부적으로 변할 수 있습니다. 

객체 내부 값은 바뀔 수 있습니다.   

또한 읽기 전용 프로퍼티는 다른 프로퍼티를 활용하는 사용자 정의 게터로도 사용할 수 있습니다.

```kotlin

var name: String = "Marcin
var surname: String = "Moskala"

val fullName 
    get() = "$name $surname"

fun main() {
    println(fullName) // Marcin Moskala
    name = "Maja"
    println(fullName) // Maja Moskala
}
```

값을 호출할때마다, 사용자 정의 게터가 호출되므로, 위처럼 함수를 작성할 수 있습니다.
코틀린의 프로퍼티는 기본적으로 캡슐화되어 있고, 추가적으로 사용자 정의 접근자를 가질 수 있습니다. 이러한 특성으로 API 를 변경하거나, 정의할때 굉장히 유연합니다. 

> 관련 내용은 Item16 에서 다릅니다.

추가적으로 var 은 게터와 세터 모두를 제공하지만, val 은 게터만 재공합니다.
그래서 val 을 var 로 override 할 수 있습니다.

```kotlin

interface Element {
    val active: Boolean
}

class ActualElemnet : Element {
    override var active: Boolean = false
}
```

> java 

```java
public final class ActualElemnet implements Element {
   private boolean active;

   public boolean getActive() {
      return this.active;
   }

   public void setActive(boolean var1) {
      this.active = var1;
   }
}
// Element.java
package test;

import kotlin.Metadata;

@Metadata(
   mv = {1, 6, 0},
   k = 1,
   d1 = {"\u0000\u0012\n\u0002\u0018\u0002\n\u0002\u0010\u0000\n\u0000\n\u0002\u0010\u000b\n\u0002\b\u0003\bf\u0018\u00002\u00020\u0001R\u0012\u0010\u0002\u001a\u00020\u0003X¦\u0004¢\u0006\u0006\u001a\u0004\b\u0004\u0010\u0005¨\u0006\u0006"},
   d2 = {"Ltest/Element;", "", "active", "", "getActive", "()Z", "dashBoard"}
)
public interface Element {
   boolean getActive();
}
```

읽기 전용 프로퍼티 val 의 값은 변경될 수 있지만, reference 자체를 변경할 수는 없으므로 동기화 문제등을 줄일 수 있습니다.
그래서 일반적으로 var 보단 val 을 더 많이 사용합니다.

> val 을 사용할 경우, 값이 바뀌지 않으므로 smartCast 를 사용할 수 있습니다.

*주의* val 은 읽기 전용 프로퍼티지만, 변경할 수 없음을 의미하지 않습니다.

#### 2. 가변 컬렉션과 읽기 전용 컬렉션 구분하기

코틀린은 읽고 쓸 수 있는 컬렉션과 읽기 전용 컬렉션으로 구분됩니다.

> mutable 이 붙은 interface 는 대응되는 읽기전용 interface 를 상속 받아서, 변경을 위한 메서드를 추가한 것입니다.
> list 에는 add 등의 함수가 없으며, mutableList 에만 add 등의 상태 변경 함수를 구현하고 있습니다.

다만, 읽기 전용 컬렉션이 내부의 값을 변경할 수 없다는 의미는 아닙니다.
대부분의 경우는 변경할 수 있으나, Interface 가 이를 지원하지 않는다는 의미로 많은 util 함수에서 up casting 으로 이를 구현하고 있습니다.


```kotlin
public inline fun <T, R> Iterable<T>.map(transform: (T) -> R): List<R> {
    return mapTo(ArrayList<R>(collectionSizeOrDefault(10)), transform)
}
```

내부로직에선 ArrayList (mutable)을 사용하고 있으나, return 시에는 List(immutable)로 반환하고 있습니다.   
이러한 컬렉션을 진짜로 immutable 하게 만들지 않고, 읽기 전용으로 설계한 것은 굉장히 중요한 부분입니다.    
이로 인해, 더 많은 자유를 얻을 수 있으며 안정성 또한 보장됩니다.   

> 다만, 다운캐스팅으로 이를 우회하려하지마세요. 이는 추상화를 무시하는 행위로, 내부 구현이 바뀔 경우 동작을 보장할 수 없습니다.


#### 3. 데이터 클래스의 copy 

지금까지 살펴본 것 처럼 mutable 객체는 예측하기 어려우며 위험하다는 단점이 있습니다. 반면, immutable은 변경할 수 없다는 단점이 있는데, 이를 해결하귀 위해 자신의 일부를 수정한 새로운 객체를 만들어내는 함수를 가져야합니다.

> with 와 같은 함수로 내부 값을 바꿔 새로운 객체를 반환하는 함수를 구현해야합니다.

모든 프로퍼티를 대상으로 이런 함수를 만들 수 없으므로, data 한정자를 사용하는걸 권장드립니다.

> data + val 을 사용 시, set / map 의 key로 활용 될 수 있습니다. (내부값이 바뀌지 않으므로 hashCode 값도 유지됩니다.)
> copy 사용 시, deep copy는 지원히지 않습니다.

#### 다른 종류의 변경 가능 지점 

변경 할 수 있는 리스트를 만들어야한다고 했을때, 다음과 같은 두가지 선택지가 있습니다.

```kotlin
val list1: MutableList
var list2: ImmutableList 
```

두가지 모두 변경할 수 있으나, 방법이 다릅니다.

```
list1 += 1 // list1.plusAssign(1) 로 변경됩니다. (add)
list2 += 2 // list2 = list2.plus(2) 로 변경됩니다.
```

두 방법 모두 변경 가능 지점이 있으나, 그 위치가 다릅니다.
첫번째 코드는 구현 내부에 변경 가능 지점이 존재하며, 두번째 코드는 프로퍼티 자체가 변경 가능 지점입니다.

> 멀티 스레드를 사용할 경우, 내부 구현에서 적절한 동기화가 이뤄지지 않는다면 첫번째 방식은 사용하기 어렵습니다.

*주의* var 과 Mutable 을 함께 사용하지 마세요. 

또한 2번 방식을 사용 할 경우, set 을 막으면 내부에서만 값을 바꿀 수 있습니다.

```kotlin
var announcements = listOf<AnnounceMent>()
    private set
```

#### 변경 가능 지점 노출하지 말기 

상태를 가지는 mutable 객체를 외부에 노출하는 것은 굉장히 위험합니다.

> 캡슐화를 어기는 행위로, 내부 구현에 어떤 영향이 갈지 추측하기 어렵습니다.

이를 막기 위해, mutable 객체를 복제하거나, upcasting 을 통해 가변성을 제한할 수 있습니다.

#### 마무리 

가끔 효율성 때문에 immutable 객체보다 mutable 객체를 사용하는 것이 좋을 때가 있습니다.
이러한 최적화 코드는 코등에서 성능이 중요한 부분에서만 사용되어야하며, immutable 객체를 사용할때는 언제나 멀티스레드 때에 더 많은 주의를 기율여야합니다.

