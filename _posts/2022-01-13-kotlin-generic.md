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








