---
layout: post
title: "[Kotlin] Kotlin Collection"
subtitle: 'Kotlin Collection'
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

Kotlin 의 컬렉션에 대해

---

## 배열
특정 원소를 담고 있는 배열을 생성하려면 `arrayOf()` 함수를 사용해야한다. 빈 배열을 생성하고 싶은 경우 `emptyArrayOf()` 함수를 대신 사용할 수 있다.
- __fun < T > arrayOf(vararg elements: T): Array< T >__ <br/>
함수의 인자로 받은 값으로 구성된 배열을 반환한다.<br/><br/>
- __fun < T > emptyArray(): Array< T >__<br/>
특정 타입을 갖는 빈 배열을 반환한다.
널 값을 포함할 수 있는 배열을 생성하고 싶은 경우, `arrayOfNulls()` 함수를 사용하여 배열을 생성한 후 이 배열에 값을 따로 채워넣을 수 있다.<br/><br/>
- __fun < T > arrayOfNulls(size: Int): Array< T? >__<br/>
배열 내 각 값들이 모두 널 값으로 초기화 되어 있고, 인자로 받은 size 만큼의 크기를 갖는 배열을 반환한다.

```kotlin
// 인자로 전달된 문자열을 포함하는 배열을 생성한다.
// 배열의 타입은 인자를 통해 추론되므로 별도로 표기하지 않아도 된다.
val cities = arrayOf("Seoul", "Tokyo", "San Francisco")

// String 타입의 빈 배열을 생성한다.
// 전달되는 인자가 없어 타입 추론이 불가하므로 함수 호출 시 타입을 지정해 주어야 한다.
val emptyStringArray = emptyArray<String>()

// 크기가 3이고 널 값을 포함할 수 있는 배열을 생성한다.
// 전달되는 인자가 없어 타입 추론이 불가하므로 함수 호출 시 타입을 지정해 주어야한다.
val nullStoreableArray = arrayOfNulls<String>(3)
```
__자바의 원시 타입을 포함하는 배열은 코틀린의 배열과 다른 타입으로 취급되므로, 위의 함수가 아닌 각 타입에 맞는 함수를 사용해야한다.__
다음은 자바 원시 타입을 포함하는 배열을 생성하는 함수들의 정의이다.
- __fun booleanArrayOf(vararg elements: Boolean): BooleanArray__<br/>
Boolean 타입을 갖는 배열을 생성한다. 이 배열은 자바의 boolean[] 배열과 호환된다.<br/><br/>
- __fun byteArrayOf(vararg elements: Byte): ByteArray__<br/>
Byte 타입을 갖는 배열을 생성한다. 이 배열은 자바의 byte[] 배열과 호환된다.<br/><br/>
- __fun charArrayOf(vararg elements: Char): CharArray__<br/>
Char 타입을 갖는 배열을 생성한다. 이 배열은 자바의 char[] 배열과 호환된다.<br/><br/>
- __fun doubleArrayOf(vararg elements: Double): DoubleArray__<br/>
Double 타입을 갖는 배열을 생성한다. 이 배열은 자바의 double[] 배열과 호환된다.<br/><br/>
- __fun floatArrayOf(vararg elements: Float): FloatArray__<br/>
Float 타입을 갖는 배열을 생성한다. 이 배열은 자바의 float[] 배열과 호환된다.<br/><br/>
- __fun intArrayOf(vararg elements: Int): IntArray__<br/>
Int 타입을 갖는 배열을 생성한다. 이 배열은 자바의 int[] 배열과 호환된다.<br/><br/>
- __fun longArrayOf(vararg elements: Long): LongArray__<br/>
Long 타입을 갖는 배열을 생성한다. 이 배열은 자바의 long[] 배열과 호환된다.<br/><br/>
- __fun shortArrayOf(vararg elements: Short): ShortArray__<br/>
Short 타입을 갖는 배열을 생성한다. 이 배열은 자바의 short[] 배열과 호환된다.<br/>

## 리스트
포함하는 요소를 읽을 수만 있고 수정할 수 없는 읽기 전용 리스트는 listOf() 함수를 사용하여 생성할 수 있다. 읽기 전용 리스트를 생성하는 함수들의 정의는 다음과 같다.<br/>

- __fun < T > listOf(vararg elements: T): List< T >__<br/>
인자로 받은 elements를 포함하는 읽기 전용 리스트를 반환한다.<br/><br/>

- __fun < T > listOf(element: T): List< T >__<br/>
인자로 받은 element 하나만을 요소로 갖는 읽기 전용 리스트를 반환한다.<br/><br/>

- __fun < T > listOf(): List< T >__<br/>
비어있는 읽기 전용 리스트를 반환한다.<br/><br/>

인자로 받는 값 중, 널 값은 무시하고 널이 아닌 값으로만 리스트를 구성하고 싶은 경우 listOfNotNull() 함수를 사용하면 편리하다
- __fun < T : Any > listOfNotNull(vararg elements: T?): List< T >__<br/>
인자로 받은 elements 중 널이 아닌 값들로만 구성된 읽기 전용 리스트를 반환한다. 만약 인자로 전달된 모든 값이 널 값이라면 빈 리스트를 반환한다.<br/><br/>
- __fun < T : Any > listOfNotNull(elements: T?): List< T >__<br/>
인자로 받은 element의 값이 널이 아닌 경우 이 요소 하나만을 갖는 리스트를 반환하며, 널 값인 경우에는 빈 리스트를 반환한다.

```kotlin
// 널 값이 아닌 인자가 아무것도 없으므로, listOfCountries에는 빈 리스트가 생성된다.
val listOfCountries = listOfNotNull(null)

// 널 값인 인자는 무시하므로, "Seoul", "San Francisco" 만을 요소로 갖는 리스트가 생성된다.
val listOfCities = listOfNotNull("Seoul", null, "San Francisco", null)
```

리스트에 포함된 요소를 수정할 수 있는 리스트는 mutableListOf() 함수를 사용하여 생성한다.

- __fun < T > mutableListOf(vararg elements: T): MutableList< T >__<br/>
인자로 받은 elements를 요소로 가지며 수정 가능한 리스트를 반환한다.<br/><br/>
- __fun < T > mutableListOf(): MutableList< T >__
비어있는 수정 가능한 리스트를 반환한다.<br/>

안드로이드 앱 개발시 자주 사용하는 자료구조 중 하나인 ArrayList 또한 표준 라이브러리에서 제공하는 함수인 arrayListOf()를 사용하여 쉽게 생성할 수 있다.
- __fun < T > arrayListOf(vararg elements: T): ArrayList< T >__<br/>
인자로 받은 elements를 요소로 하는 ArrayList를 반환한다.<br/><br/>
- __fun < T > arrayListOf(): ArrayList< T >__<br/>
비어있는 ArrayList를 반환한다.

## 맵
포함하는 요소를 읽을 수만 있고, 수정할 수 없는 읽기 전용 맵(map)은 mapOf() 함수를 사용하여 생성할 수 있다.
- __fun < K, V > mapOf(vararg pairs: Pair< K, V >): Map< K, V >__<br/>
Pair 형태로 받은 인자들을 포함하는 읽기 전용 맵을 반환한다.<br/><br/>
- __fun < K, V > mapOf(pairs: Pair< K, V >): Map< K, V >__<br/>
인자로 받은 pair 하나만을 요소로 갖는 읽기 전용 맵을 반환한다.<br/><br/>
- __fun < K, V > mapOf(): Map< K, V >__<br/>
비어있는 읽기 전용 맵을 반환한다.<br/>

리스트와 유사하게 맵이 포함하고 있는 요소를 수정할 수 있는 맵은 mutableMapOf() 함수로 생성할 수 있다.
- __fun < K, V > mutableMapOf(vararg pairs: Pair< K, V >): MutableMap< K, V >__<br/>
Pair 형태로 받은 인자들을 포함하는 수정 가능한 맵을 반환.<br/><br/>
- __fun < K, V > mutableMapOf(): MutableMap< K, V >__<br/>
비어있는 수정 가능한 맵을 반환한다.<br/>

mapOf() 함수나 mutableMapOf() 함수는 맵에 들어갈 요소를 모두 Pair 형태로 받는데, Pair를 만들 때 사용할 수 있는 표준 라이브러리 내 함수인 to()를 사용하면 이를 직관적으로 표현할 수 있다.

```kotlin
// Pair를 직접 사용하는 예
val cities1 = mapOf (
    Pair("SEO", "Seoul"), Pair("TOK", "Tokyo"), Pair("MTV", "Mountain View"))
)

//표준 라이브러리 내 함수 to를 사용하여 Pair를 직관적으로 표현한 예
val cities2 = mapOf(
    "SEO" to "Seoul", "TOK" to "Tokyo", "MTV" to "Mountain View"
)
```
보다 명시적인 타입의 맵을 생성해야 하는 경우 hashMapOf(), linkedMapOf(), sortedMapOf() 함수를 사용할 수 있다.
- __fun < K, V > hashMapOf(vararg paris: Pair< K, V >): HashMap< K, V >__<br/>
Pair 형태로 받은 인자들을 포함하는 HashMap 형태의 맵을 반환한다.<br/><br/>
- __fun < K, V > hashMapOf(): HashMap< K, V >__<br/>
비어있는 HashMap 형태의 맵을 반환한다.<br/><br/>
- __fun < K, V > linkedMapOf(vararg paris: Pair< K, V >): LinkedHashMap< K, V >__<br/>
Pair 형태로 받은 인자들을 포함하는 LinkedHashMap 형태의 맵을 반환한다.<br/><br/>
- __fun < K, V > linkedMapOf(): LinkedHashMap< K, V >__<br/>
비어있는 LinkedHashMap 형태의 맵을 반환한다.<br/><br/>
- __fun < K, V > sortedMapOf(vararg paris: Pair< K, V >): SortedMap< K, V >__<br/>
Pair 형태로 받은 인자들을 포함하는 SortedMap 형태의 맵을 반환한다.<br/><br/>

## 집합
집합(set)은 중복되지 않는 요소들로 구성된 자료구조이다. 포함하는 요소를 읽을 수만 있고, 수정할 수 없는 읽기 전용 집합은 setOf() 함수를 사용하여 생성할 수 있다.
- __fun < T > setOf(vararg elements: T): Set< T >__<br/>
인자로 받은 elements를 요소로 하는 읽기 전용 집합을 반환한다.<br/><br/>
- __fun < T > setOf(vararg elements: T): Set< T >__<br/>
인자로 받은 elements를 요소로 하는 읽기 전용 집합을 반환한다.<br/><br/>
- __fun < T > setOf(elements: T): Set< T >__<br/>
인자로 받은 element 하나만을 요소로 하는 읽기 전용 집합을 반환한다.<br/><br/>
- __fun < T > setOf(): Set< T >__<br/>
비어있는 읽기 전용 집합을 반환한다.<br/>
포함하고 있는 요소를 수정할 수 있는 집합은 mutableSetOf() 함수로 생성할 수 있다.








