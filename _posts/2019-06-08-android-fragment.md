---
layout: post
title: "[Android] Fragment"
subtitle: ' About Fragment '
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

# Fragment

`Fragment`는 동작 또는 `Activity` 내에서 UI의 일부를 나타낸다. 여러 개의 프래그먼트를 하나의 액티비티에 조합하여 창이 여러 개인 UI를 구축할 수 있으며, 하나의 프래그먼트를 여러 액티비티에서 재사용할 수 있다. 프래그먼트는 자체 수명 주기를 가지고, 자체 입력 이벤트를 받으며, 액티비티 실행 중에 추가 및 제거가 가능한 액티비티의 모듈식 섹션이라고 생각하면 된다.<br /><br />
프래그먼트는 항상 액티비티 내에 포함되어 있어야 하며 해당 프래그먼트의 수명 주기는 호스트 액티비티의 수명 주기에 직접적으로 영향을 받는다.<br /><br />
예를 들어 액티비티가 일시정지되는 경우, 그 안의 모든 프래그먼트도 일시정지되며 액티비티가 소멸되면 모든 프래그먼트도 마찬가지로 소멸된다.<br /><br />
프래그먼트를 액티비티 레이아웃의 일부로 추가하는 경우, 이는 액티비티의 뷰 계층 내부의 `ViewGroup`안에 있으며, 해당 프래그먼트가 자신의 뷰 레이아웃을 정의한다. 프래그먼트를 액티비티 레이아웃에 삽입하려면 해당 프래그먼트를 액티비티의 레이아웃 파일에서 `<fragment>`요소로 선언하거나, 애플리케이션 코드에서 이를 기존의 `ViewGroup`에 추가하면 된다.`그러나 프래그먼트가 액티비티 레이아웃의 일부분이어야만 하는 것은 아니다. 나름의 UI가 없는 프래그먼트도 액티비티를 위한 보이지 않는 작업자로 사용할 수 있다.`<br /><br />

<img width="300" height="300" src="https://user-images.githubusercontent.com/30828236/59144720-aa712980-8a15-11e9-8430-800b840957eb.png">


## Fragment 생명주기

### onAttach()
프래그먼트가 액티비티에 attach 될 때 호출된다. 호출되면서 인자로 Context가 있는대 이 Context를 가지고 부모 액티비티에 listener interface를 implement했다면 형변환을 통해 가져올 수 있다.

### onCreate()
시스템은 프래그먼트를 생성할때 `onCreate()`를 호출한다. 구현 내에서 프래그먼트의 기본 요소 중 프래그먼트가 일시정지되거나 중단되었다가 재개되었을 때 유지하고자 하는 것을 초기화해야 한다.

### onCreateView()
시스템은 프래그먼트가 자신의 UI를 처음으로 그릴 시간이 되면 호출한다. 프래그먼트에 맞는 UI를 그리려면 메서드에서 `View`를 리턴해야 한다. 이 메서드는 프래그먼트 레이아웃의 루트이다. 프래그먼트가 UI를 제공하지 않는 경우 null을 반환하면 된다.

### onActivityCreated()
액티비티에서 프래그먼트를 모두 생성하고 난 다음 호출된다. 액티비티의 onCreate()에서 setContentView()한 다음과 유사하다. 이후에 UI 변경 작업이 가능하다.

### onStart()
액티비티에서의 역할과 비슷하다. 유저에게 프래그먼트가 보이도록 하는 역할이다.

### onResume()
프래그먼트가 비로소 화면에 보여지는 단계, 사용자에게 포커스를 잡은 상태. 사용자와의 상호작용이 가능하다.

#### 다른 프래그먼트가 add 되는 경우

### onPause()
시스템이 이 메서드를 호출하는 것은 사용자가 프래그먼트를 떠난다는 첫 번째 신호이다.(프래그먼트가 소멸 중이라는 뜻은 아니다). 현재 사용자가 세션을 넘어서 지속되어야 하는 변경 사항을 적용하려면 이곳에서 해야 한다.

### onStop()
프래그먼트는 더이상 보여지지 않게되며, 프래그먼트는 기능을 할 수 없다.

### onDestroyView()
프래그먼트에 `View`들을 제거한다. 만약 `add 할 때 backstack 을 사용했다면, 다시 해당 프래그먼트로 돌아 올때 onCreateView() 가 호출된다.`

### onDestroy()
프래그먼트를 제거하기 직전에 호출된다.

### onDetach()
프래그먼트 `제거를 완료`하고 액티비티와의 `연결도 해제` 시킨다.


## 액티비티의 생명주기와 프래그먼트의 생명주기
액티비티의 각 상태가 성공적으로 전이되었을 때, 프래그먼트가 호출 받을 수 있는 콜백함수의 범위이다.
<img width="300" height="300" src="https://user-images.githubusercontent.com/30828236/59145088-44d36c00-8a1a-11e9-9304-2977557fe312.png">
 - activity가 onCreate() 호출을 받아서 생성되었으면, fragment는 onActivityCreated() 함수까지 진행가능
 - **onCreate()** : fragment가 처음 만들어질때 호출한다. `데이터를 초기화 시키거나 onPause(), onStop() 단계에 갔다가 다시 데이터 복원할 정보를 여기서 처리한다.`
 - **onCreateView()** : fragment UI 초기화 할 때 호출한다.
 - **onPause()** : 사용자가 fragment를 떠날 때 호출한다. 포커스를 가지고 있지는 않지만 back stack에 있을 수 있으므로 완전히 사라지는 것은 아니다. `여기서 데이터를 저장하는 것이 좋다.`
 - **onDestroy()** : activity가 destroy 될 때 같이 destory 된다.










[안드로이드 공식 문서 링크](https://developer.android.com/guide/components/fragments?hl=ko#EventCallbacks)
