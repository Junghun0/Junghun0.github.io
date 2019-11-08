---
layout: post
title: "[Android] Handler 용도 정리"
subtitle: ' About Handler Use '
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

# Handler 용도 정리
Handler는 일반적으로 UI 갱신을 위해 사용한다.

## 백그라운드 스레드에서 UI 업데이트
백그라운드 스레드에서 네트워크나 DB 작업 등을 하는 도중에 UI를 업데이트 한다. AsyncTask에서도 내부적으로 Handler를 이용해서 onPostExecute() 메서드를 실행해서 UI를 업데이트한다.

## 메인 스레드에서 다음 작업 예약
UI 작업 중에 다음 UI 갱신 작업을 MessageQueue에 넣어 예약한다. 작업 예약이 필요한 경우가 있는데, 예를 들어 Activity의 onCreate() 메서드에서 하지 못하는 일들이 있다. 소프트 키보드를 띄우는 것이나, ListView의 setSelection() 메서드를 호출하는 작업은 onCreate() 메서드에서는 잘 동작하지 않는다. 이 때 Handler에 Message를 보내면 현재 작업이 끝난 이후의 다음 타이밍에 Message를 처리한다.

## 반복 UI 갱신
반복해서 UI를 갱신한다. DigitalClock이나 TextColor 같은 위젯도 Handler를 이용해서 현재 시간을 갱신해서 보여준다. 반복해서 UI를 갱신하는 패턴은 다음과 같다.<br/>
~~~java
private static final int DELAY_TIME = 2000;

private Runnable updateTimeTask = new Runnable() {

    @Override
    public void run() {
        systemInfo.setText(monitorService.getSystemInfo());
        handler.postDelayed(this, DELAY_TIME); //1
    }
};

public void onClickButton(View view) {
    handler.post(updateTimeTask);
}
~~~
UI 갱신이 끝나고 `1` 에서 postDelayed() 에 Runnable 자체를 전달해서 계속 반복한다.

## 시간 제한
시간을 제한할 때 사용한다. 안드로이드에서 내부적으로 ANR을 판단할 때도 사용하는 방법이다. 아래 코드는 개발자 가이드에 있는 것인데, 블루투스 LE 디바이스를 스캔하는 시간을 제한한다.<br/><br/>
**블루투스 LE 스캔 시간 제한**
~~~java
private static final long SCAN_PERIOD = 10000;

private void scanLeDevice(final boolean enable) {
    if (enable) {
        mHandler.postDelayed(new Runnable() { //1 ~
            @Override
            public void run() {
                mScanning = false;
                mBluetoothAdapter.stopLeScan(mLeScanCallback);
            }
        }, SCAN_PERIOD); // 1
        mScanning = true;
        mBluetoothAdapter.startLeScan(mLeScanCallback); //2
    } else {
        mScanning = false;
        mBluetoothAdapter.stopLeScan(mLeScanCallback);
    }
    ...
}
~~~
`2`에서 startLeScan()을 실행하는데, `1`의 postDelayed(Runnable) 메서드에서 stopLeScan()을 10초 후에 실행하도록 Runnable Message 를 전달하였다.<br/><br/>
**백 키를 두 번 이상 연속해서 누를 때만 액티비티 종료**
~~~java
private boolean isBackPressedOnce = false; //1

@Override
public void onBackPressed() {
    if (isBackPressedOnce) { // 2
        super.onBackPressed();
    } else {
        Toast.makeText(this, R.String.backpressed_message, Toast.LENGTH_SHORT).show();// 3
        isBackPressedOnce = true; // 4
        timerHandler.postDelayed(timerTask, 5000);// 5
    }
}

private final Runnable timerTask = new Runnable() { // 6

    @Override
    public void run() {
        isBackPressedOnce = false;
    }
}
~~~
`1` **isBackPressedOnce** 변수가 종료 플래그이다. 최초 값은 false 이다.<br/>
`2` 종료 플래그가 true 이면 종료한다. 최초 값이 false이기 때문에 처음에는 이 조건에 걸리지 않는다.<br/>
`3` 처음 백 키를 누르면 "정말 종료하시겠습니까?" 라는 Toast를 띄운다. 그러고서 `4`에서 종료 플래그를 true로 바꾼다.<br/>
`5` **postDelayed()** 메서드로 5초 후에 할 작업을 지정한다.<br/>
`6` **Runnable Message**는 종료 플래그를 다시 false로 되돌린다.









