---
layout: post
title: "[Kotlin] kotlin 에서 자바 코드 접근하기"
subtitle: 'Kotlin & Java'
author: "JungHoon-Park"
header-style: text
header-mask: 0.3
comments: true
catalog:  true
tags:
  - Java
  - Kotlin
  - Study
---

Kotlin 에서 Java 코드 접근하기

---

## 1. Null 안전성
자바의 참조 타입을 코틀린으로 불러오면 타입 이름 뒤에 느낌표(!)가 붙는다.`(ex, String!)`
타입 이름 뒤의 `!`는 이 타입이 `Nullable`인지, `Not-Null`인지 알 수 없다는 뜻이다.
자바의 참조 타입은 무조건 `null을 허용`하기 때문에 이런 일이 벌어진다.
반면 int와 같은 원시 타입의 경우, null 값이 들어갈 수 없으므로 느낌표 없이 Int 타입으로 인식된다.
~~~java
public interface JavaInterface {
    String trim(String str);
}
~~~
String 타입의 인수를 받아 String 타입의 값을 반환하는 trim 함수를 갖는 자바 인터페이스 이다.
~~~java
class KotlinClass: JavaInterface {
    override fun trim(str: String?): String = str?.trim() ?: ""
}

fun main() {
    val javaInterface: JavaInterface = KotlinClass()
    println(javaInterface.trim(" hi "))
}
~~~
`String!` 타입은 `String?` 이나 `String` 으로 적을 수 있다. 일반적으로 `String? 으로 적어주는 것이 안전`하며,
절대로 null 이 들어오지 않는 다는 확신이 있다면 String으로 써도 무방하다.
단, `String으로 적었는데 이 곳에 null 값`이 들어온다면 `KotlinNullPointerException` 이 발생한다.
<br/>
만일 자바에서 이 매개변수로 절대 null 값이 들어오지 않는다는 것을 보장하고 싶으면 어떻게 해야할까? 그럴 때는 @NotNull 을 사용하면된다.

~~~java
public interface JavaInterface {
    String trim(@Notnull String str);
}
~~~

`@NotNull 이 붙은 String 타입은 코틀린에서 느낌표 없이 String 그대로 인식`된다.
@NotNull의 반대로 @Nullable 타입도 있다. String 타입 앞에 `@Nullable을 붙이면 코틀린에서 String? 타입` 으로 인식된다.


