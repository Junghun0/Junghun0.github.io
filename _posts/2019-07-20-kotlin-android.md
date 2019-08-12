---
layout: post
title: "[Kotlin] Kotlin & Java"
subtitle: 'Kotlin & Java 비교 및 기본문법'
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

Kotlin & Java 비교해보기

---

# Kotlin 이란?
Kotlin은 JetBrains 에서 만든 언어로, 2011년 최초로 공개된 후 오랜 시간 개발을 거쳐 2016년 2월 1.0 정식 버전이 출시되었습니다. Kotlin은 간결한 문법과 풍부한 기능, 높은 안정성을 토대로 높은 생산성을 보장하는 것을 목표로 개발되었다. 코틀린은 구글의 연례 개발자 행사인 Google I/O 2017에서 안드로이드의 공식 지원 언어로 채택되었다.

## 주요특징
- 문장 끝에 세미콜론(;)을 쓰지 않아도 된다.
- new 키워드를 쓰지 않고 객체를 생성한다.
- 타입 추론을 지원하므로 일반적인 경우 타입을 적지 않아도 된다.

### 1. 간결한 문법
#### java
```java
//타입을 항상 명시해 주어야 한다
String name = "Junghoon";
//객체 생성시 new 키워드를 사용한다.
Person person = new Person(name)
```
#### kotlin
```kotlin
//타입을 적지 않아도 타입추론을 통해 String 타입으로 지정한다.
val name = "Junghoon"
//객체 생성 시 new 키워드를 사용하지 않는다.
val person = Person(name)
```
- - -
### 2. Null 안전성
객체 타입의 변수에서 널(null) 값의 허용 여부를 구분하지 않던 자바와 달리, 코틀린은 이를 명확히 구분한다. 또한, 널 값의 허용 여부를 컴파일 단계에서 검사하므로 런타임에서 발생하는 오류를 대폭 줄일 수 있다. swift에서 옵셔널 타입을 생각하면 이해하기 쉽다.
```kotlin
//널 값을 허용하는 문자열 타입(String?)
val foo: String? = null
//널 값을 허용하지 않는 문자열 타입(String)
val bar: String = "bar"
```
- - -
### 3. 가변/불변 구분
널 값의 허용 여부를 구분하는 것과 유사하게, 코틀린에서는 변수 및 변수 내 할당된 값의 불변 여부를 구분한다. 변수의 불변 여부, 즉 최초 생성 시 할당된 값을 이후에도 변경할 수 있는지 여부는 변수 선언 시 사용하는 키워드인 `val`과 `var`로 구분한다. 값을 한번 할당하고 나면 그 후에 변경할 수 없는 변수는 `val`을 사용하여 선언하며, 이는 자바에서 `final` 키워드를 붙인 변수와 동일하다.
이와 달리 할당된 값을 자유자재로 바꿀 수 있는 변수는 `var`로 선언하며, 이는 자바에서 `final`키워드 없이 선언하는 변수와 동일하다.
```kotlin
//자바의 final String name = "Junghoon"; 과 동일
val name: String = "Junghoon"
//컴파일 에러: 값이 한번 할당되면 다른 값을 할당할 수 없다.
name = "ParkJungHoon"

//자바의 String name = "Junghoon"; 과 동일
var name: String = "Junghoon"
//성공: var 로 선언되었기 때문에 다른 값을 할당할 수 있다.
name = "ParkJungHoon"
```
변수의 가변/불변과 유사하게, 컬렉션 자료형에 대해서도 가변/불변 여부를 구분한다. 객체에 할당된 값이 아닌 컬렉션 내 포함된 자료들을 추가하거나 삭제할 수 있는지 여부를 구분한다. 자료의 가변/불변 여부는 인터페이스로 구분하며, 불변 인터페이스의 경우 삽입/삭제/수정을 위한 함수가 없다.
```kotlin
//자료를 변경할 수 없는 리스트 생성
val immutable: List<String> = listOf("foo","test","sample")
//컴파일 에러 : add() 함수가 정의되어 있지 않다.
immutable.add("fail")

//자료를 변경할 수 있는 리스트 생성
val mutable: MutableList<String> = mutableListOf("foo","test","sample")
//성공 : MutableList에는 자료를 수정할 수 있는 함수가 정의되어 있다.
mutable.add("success")
```
- - -
### 4. 람다 표현식 지원
람다 표현식은 대부분의 언어에서 지원하는 기능이지만, 안드로이드에서는 개발 환경의 제약으로 람다 표현식을 사용하기 위해서는 별도의 설정이 필요했다. 코틀린에서는 람다 표현식을 기본으로 지원하므로 특별한 제약 없이 코드를 더 간소화 할 수 있습니다. 또한, 자바로 작성된 인터페이스에 한해 SAM(Single Abstract Method) 변환을 지원하여 함수의 인자로 전달되는 인터페이스의 인스턴스를 람다식으로 표현할 수 있다.
#### java
```java
view.setOnClickListener(new
 View.OnClickListener(){
     @Override
     public void onClick(View view){
         ...
     }
 });
```
#### kotlin
```kotlin
//SAM 변환을 통해 OnClickListener 인터페이스의 인스턴스를 람다식으로 표현한다.
view.setOnClickListener{
    ...
}
```
- - -
### 5. 스트림 API 지원
자바8은 컬렉션 내 자료를 다루는 데 유용한 스트림(stream) API를 지원한다. 하지만 안드로이드 버전 6.0이상을 사용하는 플랫폼에서만 이 API를 사용할 수 있어 제약이 컸다.
코틀린에서는 이와 유사한 역할을 하는 함수들을 코틀린 표준 라이브러리를 통해 제공하며, 안드로이드 플랫폼 버전과 상관없이 사용할 수 있어 매우 유용하다.
```kotlin
val items = listOf(10, 2, 3, 5, 6)
//리스트 내 항목 중 짝수의 합을 구한다
val sumOfEvens = items.filter{ it % 2 == 0 }.sum()
```
filter 외의 여러가지 함수가 존재하지만 여기서는 filter 예제만 적었다.
- - -
### 6. 값 및 변수 선언
타입을 먼저 적고 그 다음 이름을 적는 자바와 달리, 코틀린에서는 이름을 먼저 적고 타입을 적는다.
```kotlin
val a: String = "foo"
val b = "bar"

//선언 시 자료를 할당하지 않으면 타입을 꼭 붙여야한다.
val c: String
//자료 할당
c = "abc"

var d: Int = 0
d += 1
```
- - - 
### 7. 함수 선언
```kotlin
fun greet(name: String) : Unit {
    println("Hello, $name!")
}

fun sum(a: Int, b: Int) : Int {
    return a + b
}
```
함수의 반환값에 사용하는 Unit은 자바의 void 와 유사하게 사용하며 Unit을 반환하는 함수는 다음과 같이 반환형을 생략할 수 있다.
```kotlin
fun greet(name: String) {
    println("Hello, $name!")
}
```
- - - 
### 8. 클래스 및 인터페이스 선언
```kotlin
class Foo {
    val foo: String = "foo"

    fun foo() {
        ...
    }
}

interface Bar{
    fun bar()
}
```
- - -
### 9. 조건문
```kotlin
fun max(a: Int, b: Int) : Int {
    if (a > b){
        return a
    }else{
        return b
    }
}
```
when 문은 자바의 swith와 동일한 역할을 한다.
```kotlin
fun countItems(count: Int){
    when(count){
        1 -> println("There is $count item.")
        else -> println("There are $count item.")
    }
}
```
- - -
### 10. 반복문
인덱스 기반 for 문과 for-each 문을 모두 지원하는 자바와 달리 코틀린은 for-each문만 지원한다.
```kotlin
val items = listOf("foo", "bar", "baz")
for (item in items){
    println("item: $item")
}
```
while 문 사용방법은 자바와 동일하다.
```kotlin
val items = listOf("foo", "bar", "baz")
val i = 0
while (i < items.size) {
    println("item: ${items[i]}")
    i++
}
```






