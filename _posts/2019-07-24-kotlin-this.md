---
layout: post
title: "[Kotlin] Kotlin this 사용"
subtitle: 'Kotlin this'
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

Kotlin this

---

# this
자바에서의 this 키워드는 해당 키워드를 사용한 클래스 자신을 지칭할 때 사용하며, 코틀린에서도 동일한 용도로 사용된다.

__java__
```java
//액티비티에 버튼 클릭 리스너를 구현한다.
public class MyActivity extends AppCompatActivity implements View.OnClickListener {
    Button btnHello;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState)

        btnHello = (Button) findViewById(R.id.btn_hello);
        //버튼 클릭 리스너로 MyActivity를 설정한다.
        btnHello.setOnClickListener(this);
    }

    @Override
    public void onClick(View v) {
        //버튼 클릭 리스너 구현
    }
}
```
__kotlin__
```kotlin
//액티비티에 버튼 클릭 리스너를 구현한다.
class MyActivity : AppCompatActivity(), View.OnClickListener {
    lateinit var btnHello: Button

    override fun onCreate(saveInstanceState: Bundle?) {
        super.onCreate(saveInstanceState)

        btnHello = findViewById(R.id.btn_hello) as Button
        //버튼 클릭 리스너로 MyActivity를 설정한다.
        btnHello.setOnClickListener(this)
    }

    override fun OnClick(view: View){
        //버튼 클릭 리스너 구현
    }
}
```

this 키워드를 단독으로 사용한 것은, `해당 위치에서 가장 가까운 범위의 클래스를 의미`한다. 따라서 `클래스 내에서 다른 클래스나 인터페이스의 인스턴스를 동적으로 생성하여 사용하는 경우 키워드를 사용하는 경우 키워드를 사용하는 위치에 따라 this가 의미하는 클래스가 달라질 수 있다`.<br/>
이러한 문제를 해결하기 위해 자바에서는 {클래스 이름}.this 형태로 가리키는 클래스를 명시하며, 코틀린에서는 이를 `this@{클래스 이름}` 형태로 표기한다.

```kotlin
class MyActivity: AppCompatActivity() {
    lateinit var btnHello: Button

    override fun onCreate(saveInstanceState: Bundle?) {
        super.onCreate(saveInstanceState)

        btnHello = findViewById(R.id.btn_hello) as Button
        //버튼 클릭 리스너를 동적으로 생성한다.
        btnHello.setOnClickListener(object: View.OnClickListener{
            
            override fun onClick(view: View){
                //this = View.OnClickListener
                //액티비티의 인스턴스를 참고하기 위해 this@MyActivity를 사용한다.
                Toast.makeText(this@MyActivity, "Hello", Toast.LENGTH_SHORT).show()
            }
        })
    }
}
```