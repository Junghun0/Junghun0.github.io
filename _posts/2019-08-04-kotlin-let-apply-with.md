---
layout: post
title: "[Kotlin] Kotlin 범위지정함수"
subtitle: 'Kotlin let(), apply(), with(), run()'
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

Kotlin 의 범위지정함수

---

## 범위 지정 함수
개발을 하다 보면 특정 객체에 있는 함수를 연속해서 사용하거나, 다른 함수의 인자로 전달하기 위해 변수를 선언하고 이를 다른 곳에서는 사용하지 않는 경우가 있다.<br/>
이런 경우 유용하게 사용할 수 있는 함수를 코틀린에서는 표준 라이브러리를 통해 제공해준다.

### let()
`let()` 함수는 이 함수를 호출한 객체를 이어지는 함수 블록의 인자로 전달한다.
- __fun < T, R > T.let(block: (T) -> R ) : R__<br/>
이 함수를 호출하는 객체를 이어지는 함수형 인자 block 의 인자로 전달하며 block 함수의 결과를 반환한다.

`let()`함수를 사용하면 불필요한 변수 선언을 방지할 수 있다.<br/>
커스텀 뷰를 작성하다 보면 길이를 계산한 값을 변수에 저장해 두고, 이를 함수 호출 시 인자로 전달하는 경우가 흔하다.
```kotlin
//단말기 환경에 맞게 패딩 값을 계산한다.
val padding = TypedValue.applyDimension(
    TypedValue.COMPLEX_UNIT_DIP, 16f, resources.displayMetrics).toInt()

//패딩 값을 설정한다.
setPadding(padding, 0, padding, 0)
```
위의 경우에는 `let()` 함수를 사용하면 값 padding 의 선언 없이 계산된 패딩 값을 함수의 각 인자로 전달할 수 있다.
```kotlin
TypedValue.applyDimension(TypedValue.COMPLEX_UNIT_DIP, 16f, resources.displayMetrics).toInt().let{
    //계산된 값을 인자로 받으므로, 함수에 바로 대입할 수 있다.
    setPadding(it, 0, it, 0)
}
```
이 외에도 널 값이 아닌 경우를 체크한 후 특정 작업을 수행하는 코드에도 `let()` 함수를 사용할 수 있다.
```kotlin
fun doSomething(message: String?) {
    //message가 널이 아닌 경우에만 let 함수를 호출한다.
    message?.let {
        Toast.makeText(this, it, Toast.LENGTH_SHORT).show()
    }
}
```
- - -
### apply() 함수
`apply()` 함수는 이 함수를 호출한 객체를, 이어지는 함수 블록의 리시버로 전달한다.
- __fun < T > T.apply(block: T.() -> Unit): T__<br/>
이 함수를 호출하는 객체를 이어지는 함수형 인자 block의 리시버로 전달하며, 함수를 호출한 객체를 반환한다.

함수를 호출한 객체를 함수형 인자 block의 리시버로 전달하므로, 이 블록 내에서는 해당 객체 내의 프로퍼티나 함수를 직접 호출할 수 있다.<br/>
따라서 객체 이름을 일일이 명시하지 않아도 되므로 코드를 간략하게 만드는 데 도움이 된다.
```kotlin
val param = LinearLayout.LayoutParams(0, LinearLayout.LayoutParams.WRAP_CONTENT)
param.gravity = Gravity.CENTER_VERTICAL
param.weight = 1f
param.topMargin = 100
param.bottomMargin = 100
```
`apply()` 함수에 이어지는 블록에 param 객체를 리시버로 전달하므로, 객체 이름 없이 직접 해당 객체 내부의 속성에 접근할 수 있다.
```kotlin
val param = LinearLayout.LayoutParams(
    LinearLayout.LayoutParams.WRAP_CONTENT,
    LinearLayout.LayoutParams.WRAP_CONTENT).apply{
        gravity = Gravity.CENTER_VERTICAL
        weight = 1f
        topMargin = 100
        bottomMargin = 100
    }
```
- - -
### with() 함수
`with()` 함수는 인자로 받은 객체를 이어지는 함수 블록의 리시버로 전달한다.
- __fun < T, R > with(receiver: T, block: T.() -> R): R__<br/>
인자로 받은 객체 receiver를 이어지는 함수형 인자 block의 리시버로 전달하며, block 함수의 결과를 반환한다.

`with()` 함수는 let(), apply() 함수와 달리 이 함수에서 사용할 객체를 매개변수를 통해 받는다. 따라서 안전한 호출을 사용하여 인자로 전달되는 객체가 널 값이 아닌 경우 함수의 호출 자체를 막는 방법을 사용할 수 없으므로 널 값이 아닌 것으로 확인된 객체에 이 함수를 사용하는 것을 권장한다.
```kotlin
fun manipulateView(messageView: TextView){
    // 인자로 받은 messageView의 여러 속성을 변경한다.
    with(messageView) {
        text = "Hello, World"
        gravity = Gravity.CENTER_VERTICAL
    }
}
```
- - - 
### run() 함수
`run()` 함수는 인자가 없는 익명 함수처럼 사용하는 형태와 객체에서 호출하는 형태를 제공한다.
- __fun < R > run(block: () -> R): R__<br/>
함수형 인자 block을 호출하고 그 결과를 리턴한다.<br/><br/>
- __fun < T, R > T.run(block: T.() -> R): R__<br/>
이 함수를 호출한 객체를 함수형 인자 block의 리시버로 전달하고 그 결과를 리턴한다.<br/>

`run()` 함수를 인자가 없는 익명 함수처럼 사용하는 경우, 복잡한 계산을 위해 여러 임시 변수가 필요할 때 유용하게 사용할 수 있다.
```kotlin
val padding = run {
    // 이 블록 내부에서 선언하는 값들은 외부에 노출되지 않는다.
    val defaultPadding = TypedValue.applyDimension(...)
    val extraPadding = TypedValue.applyDimension(...)

    //계산된 값을 반환한다.
    defaultPadding + extraPadding
}
```
객체에서 `run()` 함수를 호출하는 경우 with() 함수와 유사한 목적으로 사용할 수 있다.<br/>
`run()` 함수는 안전한 호출을 사용할 수 있으므로 널 값일 수 있는 객체의 속성이나 함수에 연속적으로 접근해야 할 때 유용하다.
```kotlin
override fun onCreate(saveInstanceState: Bundle?) {
    super.onCreate(saveInstanceState)

    //액티비티 생성 시, 기존에 저장된 값이 있는 경우 UI 복원 수행
    saveInstanceState?.run {

        //Bundle 내에 저장된 값 추출
        val selection = getInt("last_selection")
        val text = getString("last_text")

        //UI 복원 수행
        ...
    }
}
```