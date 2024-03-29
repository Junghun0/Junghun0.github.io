---
layout: post
title: "[Kotlin] Kotlin 함수형 프로그래밍"
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

## 함수형 프로그래밍 ?

~~~java
[구문 1] // 뭔가를 수행
[구문 2] // 뭔가를 수행
[구문 3] // 뭔가를 수행
...
~~~


~~~java
object.
    doSomething1().
    doSomething2().
    doSomething3()
    ...
~~~

첫 번째 스타일은 프로그램이 해야 할 일을 순차적으로 명령하는 반면, 두 번째는 함수 호출 체인으로 함수를 객체에 순차적으로 적용한다.
이러한 점 때문에 첫 번째 스타일을 명령형 프로그래밍이라고 하며 두 번째를 함수형 프로그래밍이라고 한다.

함수형 프로그래밍은 종종 함수를 다른 함수의 인수로 사용하는 것을 말하며 이러한 함수를 고차함수라고 한다. 그 외에 함수형 프로그래밍은 변경 불가능한 객체를 처리하는데 유리하다.


명령형 프로그래밍 스타일을 사용하면 다음과 같은 내용이 명확해진다.
- if/else, when 및 루프를 포함한 순차적인 구문이 존재한다. 구문 순서가 중요하다.
- 각 구문은 식별 가능한 프로그램 기능을 수행하며 명령형 프로그램은 얼핏 보면 이해하기 쉬워 보인다.
- 여러 구문은 여러 가지 서로 다른 객체를 다룰 수 있다.
- 각 구문은 포함된 객체의 상태 및 관련된 객체의 상태를 변경하거나 그렇지 않을 수 있다. 루프 및 조건 분기와 같은 여러 구조적 요소 때문에 관련된 모든 객체의 복잡한 상태와 상태 전환에 대한 실제 제한이 존재하지 않는다.
- 구문은 자신의 주요 역할과 달리 예상되지 않은 것을 수행하는 함수 호출을 포함한다. 이러한 부수적 동작을 흔하게 사이드 이펙트라고 한다. 따라서 이러한 사이드 이펙트는 예상되거나 그렇지 않을 수 있으며 프로그램 기능이 잘못될 가능성이 있다.


함수형 프로그래밍은 다음과 같은 특성이 있다.
- 함수형 구조는 주로 단일 객체 또는 객체로 이뤄진 단일 컬렉션을 참조한다. 하지만 함수 매개변수를 사용하면 다른 객체나 컬렉션이 이러한 함수 호출 체인에 들어갈 수 있다.
- 함수형 프로그래밍은 함수를 함수 매개변수로 처리하는 것을 포함한다. 또한 명령형 프로그래밍과 비교하면 더 높은 추상화가 가능하다.
- 함수 호출 결과를 다른 함수의 매개변수나 입력으로 넘길 수 있어 함수형 프로그래밍은 상태 비저장 스타일로 프로그래밍할 수 있으며 복잡한 상태 전이를 방지한다.


## 코틀린과 함수형 프로그래밍

코틀린은 완전한 명령형 언어이지만 다음과 같은 내용 덕분에 함수형 프로그래밍도 가능하다.

- 코틀린은 `([매개변수]) -> [반환타입]` 같은 함수 타입 선언을 제공한다. `[매개변수]`는 콤마로 구분된 함수 매개변수 타입 목록이다.
예를 들어 `val f : (Int, String) -> String = ....` 같은 식이다. `-> [반환 타입]`은 생략할 수 없으며 아무 것도 반환하지 않는다면 `-> Unit` 같이 작성하면 된다.

- 모든 변수는 내장된 타입을 가질 수 있으며 어떠한 클래스의 인스턴스 또는 함수가 될 수 있다. 함수는 매개변수로 함수를 허용해 고차함수가 될 수 있다.
~~~java
val f1 = { -> Log.d("LOG", "Hello Kotlin")}
val f2 = { i:Int, s:String -> "${i}: ${s}" }
...
fun ff(fun1: (Int, String) -> String): String {
    return fun1(7, "Hello")
}
ff(f2)
ff({ i: Int, s: String -> "${i}- ${s}" })
~~~

- 코틀린은 익명 람다 함수를 제공한다. 람다 함수는 함수 호출 매개변수로 사용할 수 있는 함수 리터럴이다.

~~~java
val f = { i:Int, s:String -> i.toString() + ":" + s }
// 코틀린은 f 의 타입을 (Int, String) -> String 으로 추론한다.

fun fun1(p: (Int, String) -> String) {
    p(42, "Hello")
}

fun1 { i:Int, s:String -> i.toString() + ":" + s}
~~~

- 코틀린의 표준 함수는 객체, 배열, 컬렉션을 위한 여러 고차함수를 제공한다.

- 함수 호출 `fun( { [람다함수] } )`는 `function{ [람다함수] }` 같이 축약할 수 있다.

- 함수 호출 `fun(par1, par2, ... {[람다함수]})` 는 `fun(par1, par2, ...){ [람다 함수] }` 같이 축약할 수 있다.

- 코틀린 변수는 `val s = ...` 같이 선언해 `불변`이 될 수 있다. 불변 변수는 상태 처리를 방지하고 예상하지 못한 사이드 이펙트를 줄이는 데 도움이 된다.

- 싱글턴 객체의 함수는 콜론 2개 `::` 를 앞에 붙여 함수 자체를 객체처럼 다룰 수 있다.
~~~java
object x {
    fun add(a:Int, b:Int): Int = a + b
}
...
val f : (Int, Int) -> Int = X::add
~~~

- 클래스의 함수는 리시버 타입에 콜론 2개 `::` 를 앞에 붙여 객체처럼 다룰 수 있다.
~~~java
class X {
    fun add(a: Int, b:Int): Int = a + b
}
...
val f : X.(Int,Int) -> Int = X::add
~~~

- 인스턴스의 함수는 콜론 `::` 을 앞에 붙여 객체처럼 다룰 수 있다.
~~~java
class X {
    fun add(a: Int, b:Int): Int = a + b
}
...
val x1 = X()
val f : (Int, Int) -> Int = x1::add
~~~


## 이름 없는 함수 : 람다함수

우리는 일반적인 함수가 다음과 같다는 것을 알고 있다.

~~~java
fun functionName([매개변수]): ReturnType {
    ...
}

fun functionName([매개변수]): ReturnType = ...
~~~

이러한 방식으로 선언된 함수는 `functionName`으로 식별된다.

오직 필요한 것은 데이터를 처리하기 위한 변수 이름뿐이다. 변수에 할당되는 함수를 보면 `{...}` 도 식별 이름이 없다는 것을 알 수 있다.

~~~java
val f = { i:Int, s:String -> i.toString() + ": " + s }
~~~

함수는 할당된 변수에 의해 사용된다. 따라서 이러한 함수는 익명이며 일반적으로 `람다 함수` 라고 한다.


다른 함수에 매개변수로 전달된 함수도 마찬가지다.

~~~java
ff( { i:Int, s:String -> "${i} - ${s}"})
~~~

여기도 마찬가지로 이름 없는 함수 또는 람다 함수가 있다. 람다 함수를 `호출`하려면 다음 중 하나를 사용하면 된다.

~~~java
[람다함수].invoke([매개변수])
[람다함수]([매개변수])
~~~

람다 함수는 결과를 가질 수 있다. return 문을 통해 반환하는 일반 함수와 달리 람다 함수의 결과는 무엇이든 마지막 줄에서 평가되는 값이다.

~~~java
val f = { i:Int, s:String -> i.toString() + ": " + s}
~~~

`단일 매개변수`를 갖는 람다 함수에서는 간결함을 위해 매개변수 선언을 생략할 수 있으며 매개변수를 참조하기 위한 특별한 식별자 `it` 을 대신 사용할 수 있다.



## 루프 다시 살펴보기

~~~java
val arr = arrayOf("Joe", "Isabel", "John")
arr.forEach { name ->
    Log.d("A name: ${name}")
}

arr.forEachIndexed { i,s ->
    Log.d("Name #${i}: ${name}")
}
~~~

`forEach` 나 `forEachIndexed`를 처음 보면 뒤에 있는 `{}`이 구문 블록 처럼 보이지만 `->` 에서 사실 이들이 `람다 함수를 매개변수`로 갖는 함수라는 것을 알 수 있다.
따라서 `arr.forEach({...})` 또는 `arr.forEachIndexed({...})` 같이 작성하는 것도 가능하다.


`forEach` 내부를 살펴보자.
~~~java
public inline fun <T> Array<out T>.forEach(action :(T) -> Unit): Unit {
    for (element in this) action(element)
}
~~~
`forEach` 이후의 `블록이 함수 매개변수로 전달되는 함수`라는 것을 알 수 있다.


`forEach` 와 `forEachIndexed`는 언어 구조가 아닌 함수이므로 이들은 반복할 수 있는 뭔가를 포함한 임의의 객체에 직관적으로 적용할 수 있다.

~~~java
originalCollection.
    filter([필터함수]).
    map([맵핑 함수]).
    take(37).
    forEach { element ->
        ...
    }
~~~

## 리시버가 있는 함수

예를 들어 `클래스 내부의 함수처럼 함수 객체로 간주` 되고 `컨텍스트에 포함된 함수`를 `리시버 타입이 있는 함수` 라고 한다.

~~~java
val f : ReceiverType.([매개변수]) = ...
~~~

이러한 함수는 ReceiverType 클래스의 멤버 함수인 것처럼 동작하며 함수 구현 내부에서 해당 클래스의 인스턴스를 가리키는 `this`를 사용할 수 있다.

~~~java
class A {
    var d: Double = 0.0
    fun plus(x: Double) = d + x
}

val f: A.(Double) -> Double = {
    x:Double -> this.d - x
}

fun A.minus(x: Double) = f
~~~

예를 들어 앞에서 `함수 f 가 리시버 타입`을 갖는 함수다. `f`를 사용해 클래스 `A`를 `minus()` 함수로 확장하고 있으며 `f`의 구현 내부에 있는 `this.d`는 리시버 타입 (이러한 경우에는 A) 내부의 프로퍼티 `d`를 가리킨다.

**클래스 내부 함수에 대한 직접 참조**는 클래스 환경 내부에서만 작동하므로 다음은 리시버 타입이 있는 함수라는 것을 자연스럽게 알 수 있다.
~~~java
class X {
    fun add(a: Int, b:Int): Int = a + b
}
...
val f: X.(Int, Int) -> Int = X::add
~~~






