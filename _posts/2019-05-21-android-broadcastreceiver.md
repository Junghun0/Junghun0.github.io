---
layout: post
title: "[Android] BroadCastReceiver 오레오 이후 변경"
subtitle: 'Android BroadCastReceiver 오레오 이후에 변경된 점'
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

Android BroadCastReceiver 오레오 이후 변경된 점

---

## BroadCastReceiver 오레오 이후 변경된 점

[안드로이드개발자홈페이지](https://developer.android.com/about/versions/oreo/background.html#broadcasts)

앱은 더 이상 명시적 브로드캐스트를 제외한 리시버를 AndroidManifest.xml에 등록할 수 없습니다.
암시적 브로드캐스트 리시버는 런타임에 Context.registerReceiver()를 통해서만 등록이 가능합니다.
단, 서명 권한이 요구되는 브로드캐스트는 암시적 브로드캐스트 제한에서 제외됩니다.
이는 동일한 인증서로 서명된 앱으로만 브로드캐스트가 전송되기 때문입니다.

**AndroidManifest.xml**
~~~xml
<receiver
    android:name=".broadcastreceiver.BatteryReceiver"
    android:enabled="true"
    android:exported="true">
    <!--<intent-filter>//오래오 이전버전에서는 이렇게 해야됨-->
            <!--<action android:name="android.intent.action.ACTION_POWER_CONNECTED"/>-->
    <!--</intent-filter>-->
</receiver>
~~~

**BroadcastReceiver class**
~~~java
public class BatteryReceiver extends BroadcastReceiver {
    @Override
    public void onReceive(Context context, Intent intent) {
        if (intent.getAction().equals(Intent.ACTION_POWER_CONNECTED)){
            Toast.makeText(context, "전원 연결", Toast.LENGTH_SHORT).show();
        } else if (intent.getAction().equals(Intent.ACTION_POWER_DISCONNECTED)){
            Toast.makeText(context, "전원 해제", Toast.LENGTH_SHORT).show();
        }
    }
}
~~~

**MainActivity**
~~~java
private BatteryReceiver batteryReceiver;

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