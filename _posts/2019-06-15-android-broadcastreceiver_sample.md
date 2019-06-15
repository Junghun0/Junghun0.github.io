---
layout: post
title: "[Android] BroadCastReceiver 사용해보기"
subtitle: 'Android BroadCastReceiver Sample'
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

Android BroadCastReceiver 사용해보기

---

## BroadCastReceiver 란
**- 4대 컴포넌트 중 하나이다** <br />
**- 각종 앱에서 발생하는 방송(이벤트)을 캐치 후 리시버로 처리할 수 있도록 해준다** <br />
**- 방송하기 -> 수신하기 >> 두 개가 하나의 사이클로 동작된다.** <br />
**- 정적리시버 와 동적리시버 두가지 종류가 있다.** <br /><br />

### 정적리시버 사용
- 한번 등록되면 해제할 수 없다.
- Manifest에 리시버를 등록하는 방식으로 정적리시버를 등록한다.
- 해당 앱이 설치될 때 자동으로 등록된다.

**1.BroadcastReceiver를 상속받는 클래스 생성**
**new -> other -> Broadcast Receiver로 만들면 됨**

~~~java
public class BatteryReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        String action = intent.getAction();
        Toast.makeText(context, "받은 Action-> ", Toast.LENGTH_SHORT).show();
    }
}
~~~

**2.AndriodManifest.xml에 intent-filter 등록**

~~~xml
<receiver
    android:name=".broadcastreceiver.BatteryReceiver"
    android:enabled="true"
    android:exported="true">
        <intent-filter>  <!--오래오 이전버전에서는 이렇게 해야됨-->
                <action android:name="android.intent.action.ACTION_POWER_CONNECTED"/>
        </intent-filter>
</receiver>
~~~

**3.테스트해보기**
~~~java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_main);
    sendBroadcast(new Intent(Intent.ACTION_POWER_CONNECTED));
}
~~~
전원이 연결되면 `BatteryReceiver 가 방송을 캐치`하여 Toast 메시지가 동작한다.


### 동적리시버 사용
`동적등록`은 안드로이드의 `Context가 유효할동안 동작`한다. 즉 `Activity`의 `Context`로 `BroadcatReceiver`를 등록한다면 `Activity` 가 `Destroy`될 때까지 `Receiver`는 유효하다. 등록은 `Context`에서 제공하는 `registerReceiver()`를 사용해서 등록한다.<br />
메시지를 수신할 때는 등록된 `Receiver`의 `onReceive()` 메서드에서 수신 가능하다.
`BroadCastReceiver`가 더이상 필요하지 않을 경우 `unregisterReceiver()`메서드로 등록을 해제해야한다.<br />

~~~java
@Override
protected void onResume() {
    super.onResume();
    //BroadCastReceiver 에 Action 등록
    batteryReceiver = new BatteryReceiver();
    IntentFilter intentFilter = new IntentFilter();
    intentFilter.addAction(Intent.ACTION_POWER_CONNECTED);
    intentFilter.addAction(Intent.ACTION_POWER_DISCONNECTED);
    this.registerReceiver(batteryReceiver,intentFilter);
}

@Override
protected void onPause() {
    super.onPause();
    unregisterReceiver(batteryReceiver);
}
~~~