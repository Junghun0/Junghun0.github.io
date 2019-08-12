---
layout: post
title: "[Kotlin] Kotlin 상속 및 인터페이스"
subtitle: 'Kotlin 상속 및 인터페이스'
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

Kotlin 상속과 인터페이스 구현

---

# kotlin 상속과 인터페이스 구현해보기
자바에서는 클래스의 상속과 인터페이스의 구현을 `extends` 와 `implements`로 구분하지만, 코틀린에서는 이를 구분하지 않고 콜론(:) 뒤에 상속한 클래스나 구현한 인터페이스를 표기합니다.
#### java
```java
public class MainActivity extends AppCompatActivity implements View.OnClickListener {
    ...
}
```
#### kotlin
```kotlin
class MainActivity: AppCompatActivity(), View.OnClickListener {
    ...
}
```
`클래스를 상속하는 경우 반드시 부모 클래스의 생성자를 호출해야 한다. 위에서는 AppCompatActivity의 기본 생성자를 호출하는 것을 확인할 수 있다.`<br/>
부모 클래스의 생성자가 여러 형태일 경우, 클래스 선언부에서 부모 클래스의 생성자를 호출하는 대신 별도의 생성자 선언에서 부모 클래스의 생성자를 호출하도록 구현할 수 있다.<br/>
다음은 안드로이드 커스텀 뷰를 만들기 위해 View 클래스를 상속하는 예이다.
```kotlin
class MyView : View {
    constructor(context: Context) : super(context){
        //뷰 초기화
    }
    
    constructor(context: Context, attrs: AttributeSet?) : super(context, attrs){
        //뷰 초기화
    }
}
```
`생성자가 여럿인 경우 this 키워드를 사용하여 자기 자신의 생성자를 호출할 수 있다.`
```kotlin
class MyView : View {
    constructor(context: Context) : this(context,null)

    constructor(context: Context, attrs: AttributeSet?) : super(context, attrs){
        //뷰초기화
    }
}
```
자바에서는 부모 클래스의 메서드를 재정의하거나 인터페이스를 구현한 메서드를 @Override 어노테이션으로 구분한다. 하지만 @Override 어노테이션 추가는 선택 사항이기 때문에 해당 메서드의 유형을 코드만으로 구분하기는 어렵다.<br/>
이러한 모호함을 없애기 위해, `코틀린에서는 상속받거나 구현한 함수의 앞에 무조건 override 키워드를 붙이도록 강제한다.`
```kotlin
class MyActivity: AppCompatActivity(), View.OnClickListener {
    //AppCompatActivity의 onCreate() 메서드 상속
    override fun onCreate(saveInstanceState: Bundle?) {
        super.oncreate(savedInstanceState)
    }

    //View.OnClickListener 인터페이스 구현
    override fun onClick(v: View){

    }
}
```
<br/>
자바에서는 클래스나 메서드에 final 키워드를 붙여 클래스를 더 이상 상속받지 못하게 하거나, 메서드를 재정의하지 못하게 할 수 있다. <br/>
하지만 코틀린에서는 그와 반대로 `open`키워드를 붙인 클래스나 함수가 아니라면 클래스를 상속하거나 함수를 재정의할 수 없다.
```kotlin
//open 키워드를 사용하여 클래스를 상속받을 수 있도록 한다.
open class OpenClass {
    //프로퍼티의 값을 상속한 클래스에서 재정의할 수 있도록 한다.
    open val openProperty = "foo"

    //open 키워드가 없다면, 상속한 클래스에서 프로퍼티 값을 재정의할 수 없다.
    val finalProperty() { }

    //open 키워드가 없다면, 상속한 클래스에서 함수를 재정의 할 수 없다.
    fun finalFunc() { }
}
```
```kotlin
//성공 : openClasss는 상속 가능한 클래스이다.
class FinalClass : OpenClass() {
    //성공 : openProperty는 재정의 가능한 프로퍼티이다.
    override val openProperty = "FOO"

    //오류 : finalProperty는 재정의 가능하도록 설정되지 않았다.
    override val finalProperty = "BAR"

    //성공 : openFunc 함수는 재정의 가능한 함수이다.
    override fun openFunc(){
        Log.d("Log","openFunc()")
    }

    //오류 : finalFunc는 재정의 가능하도록 설정되지 않았다.
    override fun finalFunc() {
        Log.d("Log","finalFunc()")
    }
}
```
```kotlin
//실패 : FinalClass 는 상속 가능하도록 설정되지 않았습니다.
class Foo : FinalClass()
```

