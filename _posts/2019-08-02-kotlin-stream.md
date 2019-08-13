---
layout: post
title: "[Kotlin] Kotlin Stream"
subtitle: 'Kotlin Stream'
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

Kotlin 의 Stream 함수정리

---

## 스트림 함수
자바 8에서는 리스트나 맵과 같은 컬렉션에 포함된 자료들을 손쉽게 다룰 수 있도록 스트림(stream) 기능을 제공한다. 스트림에서 제공하는 여러 연산자들을 사용하면 컬렉션에 포함된 자료들을 다른 타입으로 변경하거나, 새로운 자료를 추가로 생성하는 등의 작업을 쉽게 구현할 수 있다.<br/>
코틀린에서는 스트림 대신 이와 유사한 역할을 하는 함수들을 표준 라이브러리에서 제공하며, 확장 함수 형태로 제공된다. 따라서 항상 stream() 메서드를 호출해야 했던 자바와 달리 컬렉션 객체에서 직접 이러한 함수들을 호출할 수 있어 편리하다.<br/>

### 변환 (map)
#### map()
`map()` 함수는 컬렉션 내 인자를 다른 값 혹은 타입으로 변환할 때 사용한다.
```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View")

//도시 이름을 대문자로 변환한다.
cities.map{ city -> city.toUpperCase() }
        .forEach { println(it) }

//도시 이름을 받아, 이를 각 이름의 문자열 길이로 변환한다.
cities.map{ city -> city.length }
        .forEach{ println("length=$it") }
```
__결과__
```kotlin
SEOUL
TOKYO
MOUNTAIN VIEW
length=5
length=5
length=13
```
- - -
#### mapIndexed()
`mapIndexed()` 함수를 사용하면 컬렉션 내 포함된 인자의 인덱스 값을 변환 함수 내에서 사용할 수 있다.
```kotlin
//0 부터 10까지 정수를 포함하는 범위
val numbers = 0..10

//변환 함수에서 각 인자와 인덱스를 곱한 값을 반환한다.
numbers.mapIndexed{ idx, number -> idx * number }
        .forEach { print("$it") }
```
__결과__
```kotlin
0 1 4 9 16 25 36 49 64 81 100
```
- - -
#### mapNotNull()
`mapNotNull()`은 컬렉션 내 각 인자를 변환함과 동시에, 변환한 결과가 널 값인 경우 이를 무시한다.
```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View")

//도시 이름의 길이가 5 이하일 경우에는 이를 그대로 반환하고 , 그렇지 않은 경우 널 값을 반환한다.
cities.mapNotNull{ city -> if (city.length <= 5) city else null }
        .forEach{ println(it) }
```
__결과__
```kotlin
Seoul
Tokyo
```
- - -
#### flatMap()
`flatMap()` 함수는 map() 함수와 유사하게 컬렉션 내 인자를 다른 형태로 변환해주는 역할을 한다. 하지만 map() 함수와 달리 `flatMap() 함수는 변환 함수의 반환형이 Iterable 이다.`<br/>
따라서 하나의 인자에서 여러 개의 인자로 매핑이 필요한 경우에 사용한다.
```kotlin
val numbers = 1..6

//1부터 시작하여 각 인자를 끝으로 하는 범위를 반환한다.
numbers.flatMap { number -> 1..number}
        .forEach { println("$it") }
```
__결과__
```kotlin
1 1 2 1 2 3 1 2 3 4 1 2 3 4 5 1 2 3 4 5 6
```
- - -
#### groupBy()
`groupBy()` 함수는 컬렉션 내 인자들을 지정한 기준에 따라 분류하며, 각 인자들의 리스트를 포함하는 맵 형태로 결과를 반환한다.
```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View")

//도시 이름의 길이가 5 이하면 "A" 그룹에, 그렇지 않으면 "B" 그룹에 대입한다.
//여기에서 지정하는 이름은 반환되는 맵의 키 이름으로 사용된다.
cities.groupBy { city -> if (city.length <= 5) "A" else "B" }
        .forEach{ key, cities -> println("key=$key cities=$cities")}
```
__결과__
```kotlin
key=A cities=[Seoul, Tokyo]
key=B cities=[Mountain View]
```
- - -
### 필터 (filter)
#### filter()
`filter()` 함수는 컬렉션 내 인자들 중 주어진 조건과 일치하는 인자만 걸러주는 역할을 한다.
```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View")

//도시 이름의 길이가 5 이하인 항목만 통과시킨다.
cities.filter { city -> city.length <= 5 }
        .forEach{ println(it) }
```
__결과__
```kotlin
Seoul
Tokyo
```
- - -
#### take()
`take()`함수는 컬렉션 내 인자들 중 앞에서 take() 함수의 인자로 받은 개수만큼만을 인자로 갖는 리스트를 반환한다.<br/>
이와 비슷한 함수로는 `takeLast()`, `takeWhile()` 함수가 있다.<br/>
`takeLast()` 함수는 `take()` 함수와 반대로 뒤에서부터 이 함수의 인자로 받은 개수 만큼만을 인자로 갖는 리스트를 반환하고, `takeWhile()` 함수는 첫 번째 인자부터 시작하여 주어진 조건을 만족하는 인자까지를 포함하는 리스트를 반환한다.<br/>
`takeLastWhile()` 함수는 `takeWhile()` 함수와 반대로 뒤에서부터 주어진 조건을 만족하는 인자까지를 포함하는 리스트를 반환한다.
```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View", "NYC", "Singapore")

//첫 번째 인자로부터 하나의 인자만 포함하도록 한다.
cities.take(1)
    .forEach { println(it) }

//마지막 인자로부터 두 개의 인자만 포함하도록 합니다.
cities.takeLast(2)
    .forEach { println(it) }

//문자열의 길이가 5 이하인 조건을 만족할 때까지 해당하는 항목을 반환한다.
//Mountain View 가 조건을 만족하지 않으므로 이후의 인자들은 모두 무시한다.
cities.takeWhile { city -> city.length <= 5 }
        .forEach { println(it) }

//뒤에서부터 시작하여, 문자열의 길이가 13미만인 조건을 만족할 때까지 해당하는 항목을 반환한다.
//컬렉션 내 항목의 순서는 유지된다.
cities.takeLastWhile { city -> city.length < 13 }
        .forEach { println(it) }
```
__결과__
```kotlin
// cities.take(1)
Seoul
// cities.takeLast(2)
NYC
Singapore
// cities.takeWhile { city -> city.length <= 5 }
Seoul
Tokyo
// cities.takeLastWhile { city -> city.length < 13 }
NYC
Singapore
```
- - -
#### drop()
`drop()`함수는 take() 함수의 반대 역할을 하며, 조건을 만족하는 항목을 컬렉션에서 제외한 결과를 반환한다.<br/>
take() 함수와 유사하게 `dropLast()`, `dropWhile()`, `dropLastWhile()` 함수를 지원한다.
```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View", "NYC", "Singapore")

//첫 번째 인자로부터 하나의 인자를 제외한다.
cities.drop(1)
    .forEach { println(it) }

//마지막 인자로부터 두 개의 인자를 제외한다.
cities.dropLast(2)
    .forEach { println(it) }

//문자열의 길이가 5 이하인 조건을 만족할 때까지 해당하는 항목을 제외한다.
//Mountain View 가 조건을 만족하지 않으므로 이후의 인자들은 더 이상 제외되지 않는다.
cities.dropWhile { city -> city.length <= 5 }
        .forEach { println(it) }

//뒤에서부터 시작하여, 문자열의 길이가 13미만인 조건을 만족할 때까지 해당하는 항목을 제외한다.
//컬렉션 내 항목의 순서는 유지된다.
cities.dropLastWhile { city -> city.length < 13 }
        .forEach { println(it) }
```
__결과__
```kotlin
// cities.drop(1)
Tokyo
Mountain View
NYC
Singapore
// cities.dropLast(2)
Seoul
Tokyo
Mountain View
// cities.dropWhile { city -> city.length <= 5 }
Mountain View
NYC
Singapore
// cities.dropLastWhile { city -> city.length < 13 }
Seoul
Tokyo
Mountain View
```
- - -
#### first()
`first()` 함수는 컬렉션 내 첫번째 인자를 반환한다. 단순히 리스트 내에서 첫 번째에 위치하는 인자를 반환하는 것뿐 아니라, 특정 조건을 만족하는, 특정 조건을 만족하는 첫 번째 인자를 반환하도록 구성하는 것도 가능하다.<br/>
조건을 만족하는 인자가 없는 경우 `NoSuchElementException` 예외를 발생시키며, `firstOrNull()` 함수를 사용하면 예외 대신 널 값을 반환하도록 할 수 있다.
```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View", "NYC", "Singapore")

//첫번째 인자를 반환한다.
println(cities.first())

//마지막 인자를 반환한다.
println(cities.last())

//문자열 길이가 5를 초과하는 첫 번째 인자를 반환한다.
println(cities.first { city -> city.length > 5 })

//문자열 길이가 5를 초과하는 마지막 인자를 반환한다.
println(cities.last { citiy -> city.length > 5 })

try {
    //조건을 만족하는 첫 번째 인자를 반환하며, 없을 경우 예외를 발생시킨다.
    cities.first { city -> city.isEmpty() }
} catch (e: NoSuchElementException) {
    println("Not found")
}

try {
    //조건을 만족하는 마지막 인자를 반환하며, 없을 경우 예외를 발생시킨다.
    cities.last { city -> city.isEmpty()}
} catch (e: NoSuchElementException) {
    println("Not found")
}

//조건을 만족하는 마지막 인자를 반환하며, 없을 경우 예외를 발생시킨다.
println(cities.firstOrNull { city -> city.isEmpty() })

//조건을 만족하는 마지막 인자를 반환하며, 없을 경우 널 값을 반환한다.
println(cities.lastOrNull { city -> city.isEmpty() })
```
__결과__
```kotlin
//cities.first()
Seoul

//cities.last()
Singapore

//cities.first { city -> city.length > 5 }
Mountain View

//cities.last { citiy -> city.length > 5 }
Singapore

//cities.first { city -> city.isEmpty() }
Not found

//cities.last { city -> city.isEmpty() }
Not found

//cities.firstOrNull { city -> city.isEmpty() }
null

//cities.lastOrNull { city -> city.isEmpty() }
null
```
- - -
#### distinct()
`distinct()` 함수는 컬렉션 내에 포함된 항목 중 중복된 항목을 걸러낸 결과를 반환한다. 이때 항목의 중복 여부는 `equal()`로 판단하며, `distinctBy()` 함수를 사용하면 비교에 사용할 키 값을 직접 설정할 수 있다.

```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View", "Seoul", "Tokyo")

//도시 목록 중 중복된 항목을 제거한다.
cities.distinct()
    .forEach { println(it) }

//중복된 항목을 판단할 때, 도시 이름의 길이를 판단 기준으로 사용한다.
cities.distinctBy{ city -> city.length }
    .forEach { println(it) }
```
__결과__
```kotlin
//cities.distinct()
Seoul
Tokyo
Mountain View

//cities.distinctBy { city -> city.length }
Seoul
Mountain View
```
- - -
### 조합 및 합계
#### zip()
`zip()` 함수는 두 컬렉션 내의 자료들을 조합하여 새로운 자료를 만들 때 사용한다. 두 컬렉션 간 자료의 개수가 달라도 사용할 수 있으며, 이 경우에 반환되는 컬렉션의 자료 수는 조합에 사용하는 컬렉션의 자료의 수 중 더 적은 쪽은 따라간다.<br/>
기본값으로는 조합된 결과를 Pair로 만들어주며, 원하는 경우 조합 규칙을 사용자가 정의하여 사용할 수도 있다.
```kotlin
//도시 코드를 담은 리스트로, 4개의 자료를 가지고 있다.
val cityCodes = listOf("SEO", "TOK", "MTV", "NYC")

//도시 이름을 담은 리스트로, 3개의 자료를 가지고 있다.
val cityNames = listOf("Seoul", "Tokyo", "Mountain View")

//단순히 zip 함수를 호출하는경우, Pair 형태로 자료를 조합한다.
cityCodes.zip(cityNames)
        .forEach { pair -> println("${pair.first}:${pair.second}") }

//조합할 자료의 타입을 조합 함수를 통해 지정하면 해당 형태로 바꿔준다.
cityCodes.zip(cityNames) { code, name -> "$code ($name)" }
        .forEach { println(it) }
```
__결과__
```kotlin
// cityCodes.zip(cityNames)
SEO:Seoul
Tok:Tokyo
MTV:Mountain View

// cityCodes.zip(cityNames) { code, name -> "$code ($name)" }
SEO (Seoul)
TOK (Tokyo)
MTV (Mountain View)
```
- - -
#### joinToString()
`joinToString()` 함수는 컬렉션 내 자료를 문자열 형태로 변환함과 동시에, 이를 조합하여 하나의 문자열로 생성한다.<br/>
이는 컬렉션 내 자료를 간단히 직렬화할 때 매우 유용하게 사용할 수 있다.
```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View", "NYC", "Singapore")

//기본 설정값을 사용하여 문자열 형태로 조합한다.
println(cities.joinToString())

//구분자로 다른 문자를 사용하도록 한다.
//구분자 이외에도 다른 설정을 변경할 수 있다.
println(cities.joinToString(separator = "|"))
```
__결과__
```kotlin
// cities.joinToString()
Seoul, Tokyo, Mountain View, NYC, Singapore

// cities.joinToString(separator = "|")
Seoul|Tokyo|Mountain View|NYC|Singapore
```
<br/>
```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View", "NYC", "Singapore")

// 컬렉션 내 포함된 모든 자료의 개수를 반환한다.
println(cities.count())

// 컬렉션 내 포함된 자료 중, 길이가 5 이하인 자료의 개수를 반환
println(cities.count { city -> city.length <= 5 })
```
__결과__
```kotlin
// cities.count()
5
// cities.count { city -> city.length <= 5 }
3
```
- - -
#### reduce()
`reduce()` 함수는 컬렉션 내 자료들을 모두 합쳐 하나의 값으로 만들어주는 역할을 한다. joinToString() 함수는 reduce() 함수의 일종이다.<br/>
`reduce()` 함수는 컬렉션 내 첫 번째 자료부터 조합을 시작하며, `reduceRight()` 함수는 동일한 작업을 컬렉션 내 마지막 자료부터 시작한다.
```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View", "NYC", "Singapore")

//acc 에는 지금까지 조합된 결과가, s 에는 새로 조합할 자료가 만들어진다.
println( cities.reduce { acc ,s -> "$acc, $s" })

//reduceRight 함수는 마지막 인자부터 조합한다.
println( cities.reduceRight { s, acc -> "$acc, $s" })
```
__결과__
```kotlin
// cities.reduce { acc ,s -> "$acc, $s" }
Seoul, Tokyo, Mountain View, NYC, Singapore
// cities.reduceRight { s, acc -> "$acc, $s" }
Singapore, NYC, Mountain View, Tokyo, Seoul
```
- - -
#### fold()
`fold()` 함수는 reduce() 함수와 거의 동일한 역할을 하나, 초기값을 지정할 수 있다.
```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View", "NYC", "Singapore")

// fold 함수의 초기값으로 "Initial" 문자를 대입한다.
println(cities.fold("Initial") { acc ,s -> "$acc, $s" })
```
__결과__
```kotlin
// cities.fold("Initial") { acc ,s -> "$acc, $s" }
Initial, Seoul, Tokyo, Mountain View, NYC, Singapore
```
- - -
### 기타
#### any()
`any()` 함수는 컬렉션 내 단 하나의 자료라도 존재하면 true 를, 그렇지 않으면 false 를 반환한다. 
```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View", "NYC", "Singapore")

// 리스트에서 자료가 존재하는지 확인
println(cities.any())

// 문자열 길이가 5 이하인 자료가 있는지 확인
println(cities.any { city -> city.length <= 5 })
```
__결과__
```kotlin
//cities.any()
true
//cities.any { city -> city.length <= 5 }
true
```
- - -
#### none()
`none()` 함수는 any() 함수와 반대 작업을 수행하며, 컬렉션이 비어있는지 여부를 반환한다.
```kotlin
val cities = listOf("Seoul", "Tokyo", "Mountain View", "NYC", "Singapore")

//cities 리스트에 자료가 존재하지 않는지 확인
println(cities.none())

//빈 문자열을 가진 자료가 존재하지 않는지 확인
println(cities.non { city -> city.isEmpty() })
```
__결과__
```kotlin
//cities.none()
false
//cities.non { city -> city.isEmpty() }
true
```
- - -
#### max(), min(), average()
숫자 타입의 자료를 갖는 컬렉션 내에서 각각 최대값 , 최솟값, 평균을 반환하는 함수이다.














