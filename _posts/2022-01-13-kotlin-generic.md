---
layout: post
title: "[Kotlin] Kotlin 타입 안전성에 대해"
subtitle: 'Kotlin Study'
author: "JungHoon-Park"
header-style: text
header-mask: 0.3
comments: true
catalog:  true
tags:
  - Android
  - Kotlin
  - Study
---

Kotlin 함수형 프로그래밍 이란?

---

## 제네릭

제네릭 (`Generics`) 은 타입에 타입 매개변수를 추가할 수 있는 언어 기능 집합을 표현하는 데 사용되는 용어다.

예를 들어 Int 타입 객체로 요소를 추가하는 함수가 있는 단순한 클래스가 있다.

~~~java
class AdderInt {
    fun add(i: Int) {
        ...
    }
}
~~~

그리고 String 객체에 대한 또 다른 클래스가 있다고 가정해보자.

~~~java
class AdderString {
    fun add(s: String) {
        ...
    }
}
~~~

`add()` 함수 내부에서 일어나는 일을 제외하면 이러한 클래스는 매우 유사하다.
따라서 추가할 요소의 `타입을 추상화`하는 언어 기능을 생각해 볼 수 있다.
코틀린에는 이러한 언어 기능이 존재하는데 이를 `제네릭` 이라고 한다. 제네릭에 해당하는 구조는 다음과 같은 형태로 사용할 수 있다.

~~~java
class Adder<T> {
    fun add(toAdd: T) {
        ...
    }
}
~~~

여기서 `T`는 타입 매개변수다. `T` 대신 다른 이름을 타입 매개변수로 사용할 수 있지만 대부분 타입 매개변수 이름으로 T, R, S, U, A 또는 `B` 를 자주 사용한다.

이러한 `클래스를 인스턴스화하는 경우` **컴파일러는 타입 매개변수에 해당하는 타입을 알아야 한다**.

~~~java
class Adder<T> {
    fun add(toAdd: T) {
        ...
    }
}
val intAdder = Adder<Int>()
val stringAdder = Adder<String>()
~~~


## 단순 제네릭

클래스나 인터페이스를 타입 매개변수화 하려면 타입 이름 이후의 꺽쇠 `<>` 내부에 콤마로 구분된 일정한 양식의 타입 매개변수 목록을 추가해야 한다.

~~~java
class TheClass<[타입목록]> {
    [클래스 바디]
}

interface TheInterFace<[타입목록]> {
    [인터페이스 바디]
}
~~~

다른 타입처럼 생성자와 `init{}` 블록을 포함해 클래스나 인터페이스 내부에서 타입 매개변수를 사용할 수 있다.

~~~java
class TheClass<A, B>(val p1: A, val p2: B?) {
    constructor(p1: A) : this(p1, null)
    init {
        var x: A = p1
        ...
    }

    fun function(p: A): B? = p2
}
~~~

## 선언 지점 가변성

제네릭을 말할 때 `가변성(Variance)` 이라는 용어는 할당에서 더 구체적이거나 덜 구체적인 타입을 사용할 수 있는 능력을 나타낸다.
`Any` 가 `String`과 비교해 덜 구체적이라고 알고 있을 때 가변성은 다음 중 어느 것이 가능하느냐가 문제로 나타난다.

~~~java
class A<T> {...}

var a = A<String>()
var b = A<Any>()

a = b // 가능한가 ?
...
b = a // 가능한가 ?
~~~

이것이 왜 중요할까 ? 답은 타입 안전성을 보면 명확하다.

~~~java
class A<T> {
    fun add(p: T) {...}
}

var a = A<String>()
var b = A<Any>()

b = a // 가능한가 ?
b.add(37)
~~~

`37` 을 `A<Any>` 에 추가하는 것은 임의의 타입이 `Any`의 서브클래스이기 때문에 문제가 되지 않는다.
하지만 `b = a` 로 인해 b는 `A<String>`의 인스턴스를 가리키고 `37`은 문자열이 아니므로 런타임 오류가 발생한다.

마찬가지로 `a = b` 로 할당해도 문제가 발생한다.
이는 더 명백하다. a 가 String 요소를 위한 인스턴스이고 b처럼 Int 타입 값을 처리 할 수 없기 때문이다.


~~~java
class A<T> {
    fun extract(): T = ...
}

var a = A<String>()
var b = A<Any>()

a = b // 가능한가 ?
val extracted: String = a.extract()
~~~

마지막 구문에서 `a.extract()` 는 예를 들어 b 와 현재의 a 는 Int 객체를 포함할 수도 있기 때문에 `Any` 및 `String` 타입 모두로 평가될 수 있다.
하지만 a는 String 요소만 처리할 수 있어 Int 객체를 포함하는 것이 허용되지 않는다. 따라서 `a = b`를 허용하지 않는다.


~~~java
class A<T> {
    fun add(p: T) {...}
}
var a = A<String>()
var b = A<Any>()

a = b // 가능한가 ?
a.add("World")
~~~

이러한 종류의 변형이 가능하도록 코틀린은 제네릭 매개변수에 가변성 어노테이션을 추가할 수 있다.
`b = a`로 할당하는 첫 번째 예제는 타입 매개변수에 `out` 어노테이션을 추가하면 컴파일 할 수 있다.

~~~java
class A<out T> {
    fun extract(): T = ...
}
var a = A<String>()
var b = A<Any>()

b = a // 가능하다!
val extracted: String = b.extract()
~~~

`a = b` 로 할당하는 두 번째 예제는 타입 매개변수에 `in` 어노테이션을 추가하면 컴파일 할 수 있다.

~~~java
class A<in T> {
    fun add(p: T) {...}
}
var a = A<String>()
var b = A<Any>()

a = b // 가능하다!
b.add("World")
~~~


따라서 `in` 이나 `out` 가변성 어노테이션을 타입 매개변수에 추가하고 클래스 변형 작업을 제네릭 타입의 입력만 허용할 것인지 제네릭 타입의 출력만 허용할 것인지를 제한하면 코틀린에서 변형이 가능하다


## *

in 또는 out 가변성 어노테이션을 갖는 클래스나 인터페이스가 있다면 특수한 와일드카드 `*` 을 사용할 수 있으며 이는 다음과 같은 의미가 있다.

- `out` 가변성 어노테이션의 경우 `*` 은 `out Any?` 의 의미를 갖는다.
- `in` 가변성 어노테이션의 경우 `*` 은 `in Nothing` 의 의미를 갖는다.

`Any` 는 모든 클래스의 수퍼클래스이고 `Nothing` 은 모든 클래스의 서브클래스다.

~~~java
interface Interf<in A, out B> {
    ...
}

val x: Interf<*, In> = ...
    //...Interf<in Nothing, Int> 와 동일하다.

val y: Interf<Int, *> = ...
    //...Interf<Int, out Any?> 와 동일하다.
~~~

타입에 대해 아무 것도 모르지만 클래스 또는 인터페이스 선언에 의해 규정된 가변성 시맨틱을 만족시키려면 별표 와일드카드를 사용하길 바란다.


## 제네릭 함수

코틀린의 함수도 제네릭이 될 수 있다. -> **함수의 매개변수 또는 매개변수의 일부가 제네릭 타입을 가질 수 있다.**

이러한 경우 제네릭 타입 지정자는 `function` 키워드 다음의 꺽쇠 괄호 안에 콤마로 구분된 목록으로 추가해야 한다.

~~~java
fun <A> fun1(par1: A, par2: Int) {
    ...
}

fun <A, B> fun2(par1: A, par2: B) {
    ...
}

fun <A> fun3(par1: String): A {
    ...
}

fun <A> fun4(par1: String): List<A> {
    ...
}
~~~

호출

~~~java
fun1<String>("Hello", 38)

fun2<Int, String>(38, "World")

val s: String = fun3<String>("A")
~~~

타입을 추론할 수 있다면 타입 매개변수는 생략할 수 있다.







