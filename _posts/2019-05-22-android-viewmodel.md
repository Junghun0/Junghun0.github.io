---
layout: post
title: "[Android] LiveData + ViewModel 사용해보기 "
subtitle: ' Android LiveData + ViewModel Part of Android Jetpack. '
author: "JungHoon-Park"
header-style: text
header-mask: 0.3
comments: true
catalog:  true
tags:
  - Android
  - Mobile
  - Architecture
  - Study
---

## Android Architecture Components - ViewModel

### ViewModel ?
`ViewModel`클래스는 UI 관련 데이터를 저장하고 관리하기 위해 설계되었다.
안드로이드 프레임워크는 특정 사용자 동작 또는 사용자 제어에서 완전히 벗어난 장치 이벤트에 대한 응답으로 UI 컨트롤러를 파괴하거나 re-create 하도록 한다.<br /><br />
약 시스템이 UI 컨트롤러를 destory하거나 re-creates한다면 별도로 저장되지 않은 데이터를 잃게 된다.
여러분의 앱이 사용자의 리스트를 포함하고 있다고 생각해봅시다. 만약 액티비티가 상태 변경으로 재 생성이 된다면, 새 액티비티는 사용자 리스트를 re-fetch 해야한다.<br /><br />
간단한 데이터의 경우 `onSaveInstanceState()` 메소드를 써서 `onCreate()`에서 다시 데이터를 받을 수 있지만,
이 방식은 bitmap과 리스트 형식의 많은 양의 데이터가 아닌 serialized, deserialize가 가능한 작은 데이터에 적합하다.<br />
또 다른 문제는 UI 컨트롤러가 리턴을 받기위해 상당시간 소요되는 비동기방식의 call이 빈번하게 일어날 경우 입니다.<br />
메모리 누수를 피하기 위해 시스템이 Call들을 정리해야 한다. 이런 관리는 많은 유지 관리가 필요하고 상태 변화로 인해 객체를 재 생성하는 경우, 이미 만들어진 객체를 다시 호출해야 하므로 리소스 낭비가 심하다.<br />
<br />
액티비티와 프래그먼트 같은 UI 컨트롤러는 주로 UI 데이터를 보여주고, 사용자 이벤트에 반응하고, 퍼미션 요청을 처리한다.<br />
그러므로 UI 컨트롤러가 데이터베이스 또는 네트워크에서 데이터를 로드하는 작업까지 요청되면 너무 무거운 클래스가 되어버린다. 이런식으로 UI 컨트롤러에 과한 책임을 할당하면 테스트가 더 어려워진다.<br />
그래서 `ViewModel`을 통해 UI 컨트롤러 로직에서 데이터 소유권을 분리시키는 것이 훨씬 효율적이다.<br />
<br />
Android Architecture Components에서 `ViewModel` 이라는 UI 컨트롤러를 위한 헬퍼 클래스를 제공한다. 즉, UI를 위한 데이터를 준비하는 역할을 한다.<br />
`ViewModel`객체는 자동으로 화면 회전같은 상태 변화동안 자동으로 유지되고 다음 액티비티 또는 프래그먼트 인스턴스에 즉시 사용가능하다.<br />

![viewmodel](https://user-images.githubusercontent.com/30828236/58179720-b2c22880-7ce3-11e9-8db4-7fece141cf73.png)
<br />

**출처(https://developer.android.com/topic/libraries/architecture/viewmodel)**<br />
### 사용해보기
**1.간단한 버튼을 통해 숫자를 증감하는 앱으로 ViewModel 사용해보기**<br /><br />
1.build.gradle 에 추가
~~~java
def lifecycle_version = "1.1.1"
implementation "android.arch.lifecycle:extensions:$lifecycle_version"
~~~
~~~java
dataBinding {
    enabled = true
}
~~~

2.xml 에 테스트용으로 버튼 2개와 숫자를 보여줄 TextView를 만든다.
~~~xml
<android.support.design.widget.FloatingActionButton
      android:id="@+id/add_Button"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:onClick="@{activity::onAddButtonClick}"
      android:src="@drawable/add" />

<android.support.design.widget.FloatingActionButton
      android:id="@+id/sub_Button"
      android:layout_width="wrap_content"
      android:layout_height="wrap_content"
      android:onClick="@{activity::onSubButtonClick}"
      android:src="@drawable/sub" />
~~~

3.ViewModel을 상속받는 Class를 생성한다.
~~~java
public class CounterViewModel extends ViewModel {
    public int counter = 0;
}
~~~

4.MainActivity 에서 버튼 이벤트 처리 및 ViewModel 가져오기
~~~java
private ActivityViewModelBinding binding;
private CounterViewModel viewModel;
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    binding = DataBindingUtil.setContentView(this,R.layout.activity_view_model);
    binding.setActivity(this);
    //ViewModel 가져오기
    viewModel = ViewModelProviders.of(this).get(CounterViewModel.class);
    binding.countTextView.setText(viewModel.counter + " ");
}

public void onAddButtonClick(View view){
    viewModel.counter++;
    binding.countTextView.setText(viewModel.counter + " ");
}

public void onSubButtonClick(View view){
    viewModel.counter--;
    binding.countTextView.setText(viewModel.counter + " ");
}
~~~
**ViewModel을 사용함으로써 화면 회전등의 UI 컨트롤러를 destory하거나 re-creates하는 상황에서도 예제의 ViewModel의 counter 는 상태가 유지됨을 확인 할 수 있다.**

**2.1번 예제에서 LiveData 로 변경하기**<br /><br />

1.위의 3번에서 만든 ViewModel을 상속받는 Class 수정
MutableLiveData를 이용해 관찰가능한 데이터로 만들어준다.
~~~java
public class CounterViewModel extends ViewModel {
    public MutableLiveData<Integer> counter = new MutableLiveData<>();

    public CounterViewModel() {
        counter.setValue(0);
    }

    public void increase(){
        counter.setValue(counter.getValue() + 1);
    }

    public void decrease(){
        counter.setValue(counter.getValue() - 1);
    }
}
~~~

2.LiveData + ViewModel 사용하기
~~~java
private ActivityViewModelBinding binding;
private CounterViewModel viewModel;
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    binding = DataBindingUtil.setContentView(this,R.layout.activity_view_model);
    binding.setActivity(this);
    //ViewModel 가져오기
    viewModel = ViewModelProviders.of(this).get(CounterViewModel.class);

    viewModel.counter.observe(this, new Observer<Integer>() {
        @Override
        public void onChanged(@Nullable Integer integer) {
            //UI 업데이트
            binding.countTextView.setText(integer + " ");
        }
    });
}

public void onAddButtonClick(View view){
    viewModel.increase();
}

public void onSubButtonClick(View view){
    viewModel.decrease();
}
~~~

**3.2번 예제에서 DataBinding 추가하기**<br /><br />

1.xml 에서 CounterViewModel Class 를 데이터로 등록 후 TextView의 text 속성에 추가
~~~xml
<data>
    <variable
        name="viewModel"
        type="com.example.junghoon_sample_apps.sample3_viewmodel.CounterViewModel" />
</data>
~~~
~~~xml
<TextView
        android:id="@+id/count_textView"
        android:text="@{Integer.toString(viewModel.counter)}"
        android:layout_width="match_parent"
        android:layout_height="0dp"
        android:layout_weight="1"
        android:gravity="center"
        android:textSize="20sp"/>
~~~

2.MainActivity 에서 이부분 삭제
~~~java
/*viewModel.counter.observe(this, new Observer<Integer>() {
    @Override
    public void onChanged(@Nullable Integer integer) {
        //UI 업데이트
        binding.countTextView.setText(integer + " ");
    }
});*/
~~~
~~~java
private ActivityViewModelBinding binding;
private CounterViewModel viewModel;
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    binding = DataBindingUtil.setContentView(this,R.layout.activity_view_model);

    binding.setLifecycleOwner(this);//이 객체가 이 액티비티의 라이프사이클을 참조하면서 데이터가 변경되거나 하면 refresh 시키겠다.

    //ViewModel 가져오기
    viewModel = ViewModelProviders.of(this).get(CounterViewModel.class);

    binding.setViewModel(viewModel);
}

public void onAddButtonClick(View view){
    viewModel.increase();
}

public void onSubButtonClick(View view){
    viewModel.decrease();
}
~~~

3.
~~~java
public void onAddButtonClick(View view){
    viewModel.increase();
}

public void onSubButtonClick(View view){
    viewModel.decrease();
}
~~~
이 부분도 Databinding 을 통해 코드를 없앨 수 있다.

-xml
~~~xml
<android.support.design.widget.FloatingActionButton
    android:id="@+id/add_Button"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:onClick="@{() -> viewModel.increase()}"
    android:layout_marginEnd="300dp"
    android:src="@drawable/add" />

<android.support.design.widget.FloatingActionButton
    android:id="@+id/sub_Button"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:onClick="@{() -> viewModel.decrease()}"
    android:src="@drawable/sub" />
~~~

4.수정 완료된 Activity
~~~java
private ActivityViewModelBinding binding;
private CounterViewModel viewModel;
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    binding = DataBindingUtil.setContentView(this,R.layout.activity_view_model);

    binding.setLifecycleOwner(this);//이 객체가 이 액티비티의 라이프사이클을 참조하면서 데이터가 변경되거나 하면
    //ViewModel 가져오기
    viewModel = ViewModelProviders.of(this).get(CounterViewModel.class);

    binding.setViewModel(viewModel);
}
~~~
**코드가 굉장히 깔끔해졌다!!**

### 사용해보기 2

1.SeekBar를 가지고 있는 Fragment 만들기 , Databinding 하기
~~~xml
<layout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools">
  <data>
      <variable
          name="viewModel"
          type="com.example.junghoon_sample_apps.sample4_viewmodel2.VMShareViewModel" />
  </data>

  <FrameLayout
      android:layout_width="match_parent"
      android:layout_height="match_parent"
      tools:context=".sample4_viewmodel2.SeekBarFragment">

      <SeekBar
          android:id="@+id/seekbar"
          android:layout_width="match_parent"
          android:layout_height="wrap_content"
          android:max="100"
          android:progress="@{viewModel.progress}" />

  </FrameLayout>
</layout>
~~~

2.ViewModel Class 만들기
~~~java
public class VMShareViewModel extends ViewModel {
    public MutableLiveData<Integer> progress = new MutableLiveData<>();
}
~~~

3.Activity의 xml 구성
~~~xml
<fragment
    android:id="@+id/fragment1"
    android:name="com.example.junghoon_sample_apps.sample4_viewmodel2.SeekBarFragment"
    android:layout_width="match_parent"
    android:layout_height="0dp"
    android:layout_weight="1"/>

<fragment
    android:id="@+id/fragment2"
    android:name="com.example.junghoon_sample_apps.sample4_viewmodel2.SeekBarFragment"
    android:layout_width="match_parent"
    android:layout_height="0dp"
    android:layout_weight="1"/>
~~~

4.SeekBar를 가지고 있는 fragment의 onViewCreated
~~~java
@Override
public void onViewCreated(@NonNull View view, @Nullable Bundle savedInstanceState) {
    super.onViewCreated(view, savedInstanceState);
    //viewmodel 가져오기
    final VMShareViewModel viewModel = ViewModelProviders.of(requireActivity()).get(VMShareViewModel.class);

    FragmentSeekBarBinding binding = DataBindingUtil.bind(view);
    binding.setLifecycleOwner(requireActivity());
    binding.setViewModel(viewModel);

    binding.seekbar.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
        @Override
        public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
            viewModel.progress.setValue(progress);
        }

        @Override
        public void onStartTrackingTouch(SeekBar seekBar) {

        }

        @Override
        public void onStopTrackingTouch(SeekBar seekBar) {

        }
    });
}
~~~

5.결과화면
![livedata](https://user-images.githubusercontent.com/30828236/58221440-7f1be880-7d4d-11e9-9bc2-c7bb01838586.png)