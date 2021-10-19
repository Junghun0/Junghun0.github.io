---
layout: post
title: "[Kotlin] Kotlin DSL"
subtitle: 'Kotlin DSL'
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

Kotlin DSL 이란?

---

## Kotlin DSL ?

영역 특화 언어 (DSL - Domain Specific Language) 를 사용해 표현력이 좋고 코틀린다운 API를 설계하는 방법을 알아보자.

DSL에 대해 알아보기 전에 우리가 해결하려는 문제에 대해 더 자세히 알아보자.
궁극적으로 목표는 코드의 가독성과 유지 보수성을 가장 좋게 유지하는 것이다.

API 를 깔끔하게 작성하므로서 코드의 가독성과 유지 보수성을 좋게할 수 있다.
API가 깔끔하다는 말이란 ?
- 코드를 읽는 독자들이 어떤 일이 벌어질지 명확하게 이해할 수 있어야 한다. 이름과 개념을 잘 선택하면 이런 목적을 달성할 수 있다.
어떤 언어를 사용하건 이름을 잘 붙이고 적절한 개념을 사용하는 것은 매우 중요하다.

- 코드가 간결해야 한다. 불필요한 구문이나 번잡한 준비 코드가 가능한 한 적어야 한다.


**코틀린이 간결한 구문을 어떻게 지원하는가 ?**
|일반 구문|간결한 구문|사용한 언어 특성|
|------|---|---|
|StringUtil.capitalize(s)|s.capitalize()|확장 함수|
|1.to("one")|1 to "one"|중위 호출|
|set.add(2)|set += 2|연산자 오버로딩|
|map.get("key")|map["key"]|get 메소드에 대한 관례|
|file.use({f -> f.read()})|file.use { it.read() }|람다를 괄호 밖으로 빼내는 관례|
|sb.append("yes") sb.append("no")|with (sb) { append("yes") append("no") }|수신 객체 지정 람다|

### 수신 객체 지정 람다와 확장 함수 타입

**예제 - 람다를 인자로 받는 buildString() 정의하기**
~~~kotlin
fun buildString(
    builderAction: (StringBuilder) -> Unit // 함수 타입인 파라미터를 정의한다.
): String {
    val sb = StringBuilder()
    builderAction(sb) // 람다 인자로 StringBuilder 인스턴스를 넘긴다.
    return sb.toString()
}
~~~

~~~kotlin
>>>  val s = buildString { // "it" 은 StringBuilder 인스턴스를 가리킨다.
    it.append("Hello, ")
    it.append("World!")
}
~~~

이 코드는 이해하기 쉽다. 하지만 사용하기는 편하지는 않다. 람다 본문에서 매번 it을 사용해 StringBuilder 인스턴스를 참조해야 한다.
람다의 목적이 StringBuilder를 텍스트로 채우는 것이므로 it.append 처럼 메소드 이름 앞에 it.을 일일이 넣지 않고 append처럼 더 간단하게 호출하기를 바란다.

그러려면 람다를 수신 객체 지정 람다(lambda with a receiver)로 바꿔야 한다.
람다의 인자 중 하나에게 수신 객체라는 상태를 부여하면 이름과 마침표를 명시하지 않아도 그 인자의 멤버를 바로 사용할 수 있다.


**예제 - 수신 객체 지정 람다를 사용해 다시 정의한 buildString()**

~~~kotlin
fun buildString(
    builderAction: StringBuilder.() -> Unit // 수신 객체가 있는 함수 타입의 파라미터를 선언한다.
): String {
    val sb = StringBuilder()
    sb.buildAction() // StringBuilder 인스턴스를 람다의 수신 객체로 넘긴다.
    return sb.toString()
}
~~~

~~~kotlin
>>> val s = buildString {
    this.append("Hello, ") // "this" 키워드는 StringBuilder 인스턴스를 가리킨다.
    append("World!") // "this" 를 생략해도 묵시적으로 StringBuilder 인스턴스가 수신 객체로 취급된다.
}
~~~




