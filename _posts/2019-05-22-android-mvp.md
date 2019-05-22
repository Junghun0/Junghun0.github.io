---
layout: post
title: "[Android] MVP Architecture 정리"
subtitle: ' Android MVP Architecture '
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

### MVP Pattern
- - -

![MVP1](https://user-images.githubusercontent.com/30828236/56715159-d5f3c980-6771-11e9-9962-36916bbe9924.png)

![MVP2](https://user-images.githubusercontent.com/30828236/58152417-7a045e00-7ca7-11e9-9704-c30d0a47c7fe.png)

[구글에서 제공하는 샘플링크](https://github.com/googlesamples/android-architecture/tree/master)

#### MVP 패턴의 동작
 - **1. View에서 사용자의 이벤트 수신**
 - **2. View에서 Presenter 이벤트 호출**
 - **3. Presenter에서 Model에 데이터 요청**
 - **4. Model에서 Presenter로 데이터 전달**
 - **5. Presenter에서 전달받은 데이터를 기반으로 View 업데이트**
 - **6. View에서 화면 업데이트**

#### - Model

내부적으로 쓰이는 데이터를 저장하고, 처리하는 역할을 한다. 흔히 'Business Logic' 이라고 부르는 부분이다. View, Presenter 등 다른 어떤 요소에도 의존적이지 않은 독립적인 영역이다.
 
~~~java
public class ResultUrl {
    private String message;
    private String code;
    private ResultFormat result;

    public ResultUrl(String message, String code, ResultFormat result) {
        this.message = message;
        this.code = code;
        this.result = result;
    }
 }
~~~
  
~~~java
public class GetServerResponseImpl implements MainContractor.GetServerResponse {
    @Override
    public void getNoticeURL(final OnFinishedListener onFinishedListener, String url) {

        RetrofitService retrofit = RetrofitInstance.getRetrofitInstance().create(RetrofitService.class);

        retrofit.sendShortURL(url).enqueue(new Callback<ResultUrl>() {
            @Override
            public void onResponse(Call<ResultUrl> call, Response<ResultUrl> response) {
                if (response.isSuccessful()){
                    if (response.body() != null){
                        onFinishedListener.onFinished(response.body().getResult().getUrl());
                        Log.e("result url",""+response.body().getResult().getUrl());
                    }
                }
            }
            @Override
            public void onFailure(Call<ResultUrl> call, Throwable t) {
                onFinishedListener.onFailure(t);
                Log.e("shotURL Failure",""+t.toString());
            }
        });
    }
}
 ~~~

#### - View

실제 View 에 대한 직접적인 접근을 담당한다. 안드로이드에서 Activity/Fragment 는 View의 일부로 정의한다. View의 일부로 정의한다. View 에서 발생하는 이벤트는 직접 핸들링 할 수 있으나 Presenter 에 위임하도록 한다. 위임하는 방법은 Activity 가 View Interface 를 구현해서 Presenter 에서 코드를 만들 인터페이스를 갖도록 하면 된다. 이를 통해 특정 뷰와 결합되지 않고 가상 뷰를 구현해서 간단한 유닛 테스트를 실행할 수 있다.
 
~~~java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);

    ButterKnife.bind(this);
    mainPresenter = new MainPresenter(this, new GetServerResponseImpl());
    mainPresenter.attachView(this);
}
~~~

#### - Presenter

본질적으로는 MVC의 컨트롤러와 같지만, 뷰에 연결되는 것이 아니라 Interface로 연결된다는 점이 다르다. 이에 따라 MVC가 가진 테스트 가능성 문제와 함께 모듈화/유연성 문제 역시 해결한다. Presenter의 역할을 정의한다면 View 와 Model 사이에서 자료 전달 역할을 합니다.
 
~~~java
public interface MainContractor {
    interface View {
        void showResult();

        void setResultURL(String url);

    }

    interface Presenter {

        void attachView(View view);

        void detachView();

        void loadURL(Context context, String url);
    }

    interface GetServerResponse {

        interface OnFinishedListener {
            void onFinished(String resultURL);
            void onFailure(Throwable t);
        }

        void getNoticeURL(OnFinishedListener onFinishedListener, String url);
    }
}
~~~
 
~~~java 
public class MainPresenter implements MainContractor.Presenter, MainContractor.GetServerResponse.OnFinishedListener{

    private MainContractor.View view;
    private MainContractor.GetServerResponse getNoticeIntractor;

    public MainPresenter(MainContractor.View view, MainContractor.GetServerResponse getNoticeIntractor) {
        this.view = view;
        this.getNoticeIntractor = getNoticeIntractor;
    }

    @Override
    public void attachView(MainContractor.View view) {
        this.view = view;
    }

    @Override
    public void detachView() {view = null;}

    @Override
    public void loadURL(Context context, String url) {
        getNoticeIntractor.getNoticeURL(this,url);
    }

    @Override
    public void onFinished(String resultURL) {
        view.setResultURL(resultURL);
    }

    @Override
    public void onFailure(Throwable t) {
        Log.e("network onFailure",""+t.toString());
    }
}
~~~

#### MVC와 MVP의 차이
MVC 는 Model-View-Controller 로 코드를 구성하는 디자인 패턴이다.<br />
Model과 View는 MVC,MVP가 역할이 같지만 Controller와 Presenter는 약간 다르다.<br />
MVC에서는 사용자의 이벤트에 Controller가 먼저 반응한 후 View를 가져와 적절한 작업 후 반환한다.<br />
MVP에서는 사용자의 이벤트에 View가 먼저 반응한 후 Presenter에 알리고 작업을 처리한 후 View한테 다시 알려준다.<br /><br />
**왜 사용할까?**<br />
MVP와 같은 디자인 패턴은 앱을 구성하는 코드를 모듈화시킬 수 있다. 협업 시 각각 전문분야에서만 작업을 해도 된다. 소스파일을 관리하기 더 편해지고 유닛 테스트에 용이해진다는 장점이 있다. 즉, 전체적인 유지보수가 쉬워진다.

[github 링크](https://github.com/Junghun0/Rxandroid_Architecture.git)
