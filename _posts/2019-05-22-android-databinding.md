---
layout: post
title: "[Android] DataBinding 사용해보기"
subtitle: 'Android DataBinding Sample'
author: "JungHoon-Park"
header-style: text
header-mask: 0.3
comments: true
catalog:  true
tags:
  - Android
  - Mobile
  - Study
---

Sample : Android Databinding Sample

---

# Android DataBinding 

## DataBinding 이란?
**xml 에 데이터를 바인딩하여 불필요한 코드를 줄이는 방법으로, 보통 MVVM 패턴을 구현 할 때 사용된다.**

### 사용방법
#### 1. build.gradle 파일에 dataBinding 요소를 추가
~~~java
dataBinding {
    enabled = true
}
~~~

#### 2. xml 수정하기
**레이아웃 파일의 루트태그에 'layout' 로 추가**
~~~xml
<layout xmlns:android="http://schemas.android.com/apk/res/android">

    <data>
        <variable
            name="user"
            type="com.example.databinding_sample.data.User" />
    </data>

<LinearLayout
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">

<TextView
    android:id="@+id/firstname_textView"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@{user.firstName, default=defaults}" />

<TextView
    android:id="@+id/lastname_textView"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="@{user.lastName, default=defaults}" />
    
</layout>
~~~

data 내에 있는 user 변수는 이 레이아웃 내에서 사용할 수 있는 속성에 대한 설명이다.
레이아웃 내에 있는 식은 "@{}" 구문을 사용하여 특성 속성에 기록된다. 
여기서 TextView의 텍스트는 사용자의 firstName 속성으로 설정된다.

#### 3. xml 에 binding 할 데이터 클래스 생성

~~~java
public class User {
    private final String firstName;
    private final String lastName;

    public User(String firstName, String lastName) {
        this.firstName = firstName;
        this.lastName = lastName;
    }

    public String getFirstName() {
        return firstName;
    }

    public String getLastName() {
        return lastName;
    }
}
~~~

TextView의 android:text 특성에 사용되는 식 @{user.firstName}은 전자의 클래스에 있는 firstName 필드와 후자의 클래스에 있는 getFirstName() 메서드에 액세스한다. 또는 firstName() 메서드가 존재할 경우에는 이 메서드로 해석하기도 한다.

#### 4. 데이터 바인딩 하기
~~~java
public class MainActivity extends AppCompatActivity{

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        ActivityMainBinding binding = DataBindingUtil.setContentView(this, R.layout.activity_main);
        User user = new User("JungHoon", "Park");
        binding.setUser(user);
}
~~~
위의 레이아웃 파일은 main_activity.xml이고, 따라서 생성된 클래스는 MainActivityBinding이었습니다. 이 클래스는 레이아웃 속성(예: user 변수)에서 레이아웃의 View까지 모든 바인딩을 유지하고 바인딩 식에 대해 값을 할당하는 방법을 알고 있습니다. 바인딩을 가장 쉽게 생성하는 방법은 다음과 같이 확장하는 동안 바인딩을 생성하는 것입니다.

또는 
~~~java
MainActivityBinding binding = MainActivityBinding.inflate(getLayoutInflater());
~~~ 
를 통해 뷰를 불러올 수 있다.

또는 ListView 어댑터나 RecyclerView 어댑터 내에서 데이터 바인딩 항목을 사용할때 
~~~java
ListItemBinding binding = ListItemBinding.inflate(layoutInflater, viewGroup, false);
~~~ 
or
~~~java
ListItemBinding binding = DataBindingUtil.inflate(layoutInflater, R.layout.list_item, viewGroup, false);
~~~
을 통해 뷰를 불러올 수 있다.