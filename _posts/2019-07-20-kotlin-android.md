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
- - -
### 11. 접근 제한자
자바에서 클래스 및 메서드, 필드의 가시성(visibility)을 제한하기 위해 접근 제한자를 사용하는 것처럼, 코틀린에서도 접근 제한자를 사용하여 클래스와 함수, 프로퍼티의 가시성을 제어한다.
코틀린에서 접근 제한자를 사용하는 방법은 자바와 매우 유사하나, 일부 차이가 있다.
#### java
```java
public class Foo {
    public int a = 1;
    protected int b = 2;
    private int c = 3;

    //패키지 단위 제한자(별도 표기 없음)
    int d = 4;
}
```
#### kotlin
```kotlin
class Foo {
    //접근 제한자가 없으면 public 으로 간주
    val a = 1
    protected val b = 2
    private val c = 3

    //internal을 대신 사용한다.
    internal val d = 4
}
```
public 제한자는 코틀린에서도 동일하게 사용할 수 있으나, 제한자가 없으면 public 으로 간주하므로 이를 생략하는 것을 권장한다.
자바에서는 접근 제한자를 생략하면 이에 대한 접근 범위를 패키지 단위로 제한한다. 즉, 동일한 패키지 내에 있는 클래스에서만 접근이 가능하다.
하지만 해당 클래스가 포함된 모듈이 아닐지라도 패키지를 동일하게 맞추면 패키지 단위로 제한된 클래스, 필드 및 메서드에 접근할 수 있다. 이러한 단점을 보완하기 위해 코틀린에서는 `internal` 접근 제한자를 제공한다.<br/>
단순히 같은 패키지에 있으면 접근이 가능했던 자바의 패키지 단위 제한과 달리, `internal` 접근 제한자는 동일한 모듈 내에 있는 클래스들로의 접근을 제한한다. 따라서 외부 모듈에서는 이 접근 제한자로 선언된 요소에 접근할 수 없다.<br/>
__모듈의 범위__
- IntelliJ IDEA 모듈
- Maven/Gradle 프로젝트
- 하나의 Ant 태스크 내에서 함께 컴파일되는 파일들

- - -
### 12. 생성자
#### java
```java
public class Foo {
    public Foo(){
        //생성자에서 수행할 작업들
        ...
    }
}
```
#### kotlin
```kotlin
class Foo {
    init {
        //생성자에서 수행할 작업들
        ...
    }
}
```
코틀린에서는 init 블록을 사용하여 기본 생성자를 대체한다. 생성자에 인자가 필요한 경우는 다음과 같이 인자를 받을 수 있다. 코틀린에서는 이를 `주 생성자(primary constructor)`라 부르며, 여기에서 받은 인자는 init 블록에서도 사용할 수 있다.
#### java
```java
public class Foo {
    public Foo(int a){
        //생성자에서 수행할 작업들
        ...
    }
}
```
#### kotlin
```kotlin
class Foo(a: Int) {
    init {
        //생성자에서 수행할 작업들
        ...
    }
}
```
코틀린에서도 생성자의 인자를 통해 바로 클래스 내부의 프로퍼티에 값을 할당할 수 있습니다. 이 경우 생성자의 인자를 통해 프로퍼티 선언을 대신하므로 추가로 프로퍼티를 선언하지 않아도 된다.
다음은 인자로 받은 값을 사용하여 내부의 필드 및 프로퍼티에 값을 할당하는 생성자의 예이다.<br/>
생성자의 인자에서 프로퍼티 선언이 함께 이루어지고, 값 할당 또한 생성자 호출과 동시에 수행되므로 자바에 비해 비약적으로 코드가 짧다.
#### java
```java
public class Foo {
    int a;
    char b;
    public Foo(int a, char b){
        this.a = a;
        this.b = b;
    }
}
```
#### kotlin
```kotlin
class Foo(val a: Int, var b: Char)
```
주 생성자 외에 다른 형태의 생성자가 필요한 경우 constructor 키워드를 사용하여 추가 생성자를 선언할 수 있다.
#### java
```java
public class Foo {
    int a;
    char b;
    public Foo(int a, char b){
        this.a = a;
        this.b = b;
    }
    //a의 값만 인자로 받는 추가 생성자
    public Foo(int a){
        this(a, 0);
    }
    //두 인자의 값을 모두 0으로 지정하는 생성자
    public Foo(){
        this(0, 0);
    }
}
```
#### kotlin
```kotlin
class Foo(val a: Int, var b: Char){
    //a의 값만 인자로 받는 추가 생성자
    //기본 생성자를 반드시 호출해야 한다.
    constructor(a: Int) : this(a, 0)
    //두 인자의 값을 모두 0으로 지정하는 생성자
    constructor(): this(0, 0)
}
```
주 생성자 개념이 없는 자바에서는, 새로운 생성자를 정의할 때 다른 생성자를 필요에 따라 선택적으로 호출할 수 있다. 하지만 코틀린에서는 추가 생성자를 정의 하는 경우 주 생성자를 반드시 호출해야 한다. 또한, 추가 생성자에서는 인자와 프로퍼티를 함께 선언할 수 없다. 따라서 프로퍼티 선언이 필요한 인자인 경우 반드시 주 생성자에서 이를 처리해야 한다.<br/>
생성자의 가시성을 변경하려면 constructor 키워드 앞에 접근 제한자를 추가하면 된다. 주 생성자는 생략하였던 constructor 키워드를 추가하고 접근 제한자를 추가해야 한다.
```kotlin
//주 생성자의 가시성을 internal로 지정, constructor 키워드 표기 필요
class Foo internal constructor(val a: Int, var b: Char) {
    //추가 생성자의 가시성 지정
    private constructor(a: Int) : this(a, 0)

    //접근 제한자를 지정하지 않았으므로 public
    constructor(): this(0, 0)
}
```













