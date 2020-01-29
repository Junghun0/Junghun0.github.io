---
layout: post
title: "[Android] Notification + Kotlin"
subtitle: ' About Android Notification '
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

# Notification 이란
- Notification 은 애플리케이션과 별도로 관리되는 메시지이다.
- Notification 메시지를 OS 에게 요청하면 OS는 알림 창 영역에 알림 메시지를 표시한다.
- 화면을 가지지 않는 실행단위에서 메시지를 표시할 때 주로 사용한다.

## 특징
- 사용자가 메시지를 확인하거나 제거하기 전까지 메시지를 유지되어 있다.
- 메시지를 터치하면 지정된 Activity가 실행되어 애플리케이션 실행을 유도할 수 있다.

## 소스코드
~~~java
make_notification_button1.setOnClickListener {
    val builder = NotificationCompat.Builder(this)           
    builder.setTicker("Ticker")
    builder.setSmallIcon(android.R.drawable.ic_menu_search)
    val bitmap = BitmapFactory.decodeResource(resources, R.mipmap.ic_launcher)
    builder.setLargeIcon(bitmap)
    builder.setNumber(100)
    builder.setAutoCancel(true)
    builder.setContentTitle("Content Title")
    builder.setContentText("Content Text")

    var notification = builder.build()

    var mng = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
    mng.notify(10, notification)
}
~~~
위의 코드는 임의로 생성한 버튼을 통해 Notification을 생성하는 코드이다. 위의 코드는 안드로이드 8.0 이하 까지의 버전에서만 동작이 가능하다.<br/>
또한 안드로이드 7.1 버전부터 Ticker 와 Number 메시지 또한 표시되지 않는다.<br/><br/>

Notification을 버전별로 대응해 사용해야한다면 아래처럼 Builder를 리턴하는 함수를 만들어 사용하는 것이 용이하다.<br/>
~~~java
fun getNotificationBuilder(id: String, name: String) : NotificationCompat.Builder {
    val builder: NotificationCompat.Builder?

    if (Build.VERSION.SDK_INT >= Build.VERSION_CODES.O) { // 8.0 오레오 버전
        val manager = getSystemService(Context.NOTIFICATION_SERVICE) as NotificationManager
        val channel = NotificationChannel(id, name, NotificationManager.IMPORTANCE_DEFAULT)// 중요도에 따라 메시지가 보이는 순서가 달라진다.
        channel.enableLights(true) //
        channel.lightColor = Color.RED
        channel.enableVibration(true)
        manager.createNotificationChannel(channel)
        builder = NotificationCompat.Builder(this, id)
    } else { // 그 외의 버전s
        builder = NotificationCompat.Builder(this)
    }

    return builder
}
~~~
안드로이드 8.0 부터 모든 Notification 은 채널에 할당되어야 한다. 그렇지 않으면 Notification은 동작하지 않는다. Notification을 채널로 분류하면 사용자가 (모든 Notification을 비활성하지 않고도) 앱의 특정 알림 채널을 비활성화할 수 있고, 각 채널의 시청각적 옵션을 제어할 수 있게된다. 이 작업은 모두 안드로이드 시스템 설정에서 수행할 수 있다.<br/>
안드로이드 7.1 이하를 실행하는 기기에서 사용자는 각각의 앱 단위로만 알림을 관리할 수 있다. (**실질적으로 안드로이드 7.1 이하에서는 각 앱이 하나의 채널만 가진다.**)<br/><br/>
안드로이드 8.0 이상에서는 채널에서 알림의 **중요도**를 지정한다. 그러므로 동일한 알림 채널에 게시된 모든 알림은 동일하게 동작한다.
<br/>
### Notification Channel
- 안드로이드 8.0 부터 새롭게 추가된 기능으로 사용자가 애플리케이션의 알림 메시지를 출력하지 않도록 설정하면 모든 메시지가 출력되지 않는다.
- Notification Channel을 이용하면 알림 메시지를 채널이라는 그룹으로 묶을 수 있으며 같은 채널 별로 메시지에 대한 설정을 따로 할 수 있게 된다.<br/>
### Notification Importance
- 안드로이드는 알림의 중요도에 따라 알림이 사용자에게 간섭하는 정도를 결정한다. 알림의 중요도가 높을수록 알림의 간섭 수준이 커진다.
- 안드로이드 8.0 (API 26) 이상에서 알림의 중요도는 알림이 게시된 채널의 **Importance**에 따라 결정된다.<br/><br/>

## 버전별 Notification
#### Android 4.1, API 레벨 16
- 확장형 알림 템플릿(알림 스타일이라고 함)을 도입하여 더 큰 알림 콘텐츠 영역에서 정보를 표시할 수 있게 되었습니다. 사용자는 한 손가락으로 위/아래 스와이프 동작을 사용하여 알림을 펼칠 수 있습니다.
- 버튼 형태로 더 많은 액션을 알림에 추가할 수 있는 기능도 도입되었습니다.
- 사용자가 설정에서 각 앱의 알림을 끌 수 있는 기능이 추가되었습니다.

#### Android 4.4, API 레벨 19 및 20
- 알림 리스너 서비스가 API에 추가되었습니다.
- Android Wear(현재는 Wear OS라고 함)에 대한 지원이 API 레벨 20에 추가되었습니다.

#### Android 5.0, API 레벨 21
- 잠금 화면과 헤즈업 알림이 도입되었습니다.
- 사용자가 전화를 방해 금지 모드로 설정하고 기기가 우선순위만 허용 모드일 때 어떤 알림을 표시하도록 허용할지 구성할 수 있습니다.
- API에 추가된 메서드는 알림을 잠금 화면에 표시할지 여부(setVisibility())를 설정하고 알림 텍스트의 "공개" 버전을 지정합니다.
- 시스템에 알림이 사용자를 "방해하는 수준"을 알려주는 setPriority() 메서드가 추가되었습니다(예: 높게 설정하면 알림 영역이 헤즈업 알림으로 변경됨).
- 알림 스택 지원이 Android Wear(현재는 Wear OS라고 함) 기기에 추가되었습니다. setGroup()을 사용하여 알림을 스택에 넣습니다. 알림 스택은 아직 태블릿이나 전화기에서 지원되지 않습니다. 

#### Android 7.0, API 레벨 24
- 히어로 이미지와 아바타를 강조하도록 알림 템플릿의 스타일이 변경되었습니다.
- 세 가지 알림 템플릿이 추가되었습니다. 하나는 메시지 앱에 사용하고 나머지 두 개는 확장형 어포던스 및 기타 시스템 장식으로 맞춤 콘텐츠 뷰를 장식하는 데 사용합니다.
- 핸드헬드 기기(전화기 및 태블릿)에 알림 그룹에 대한 지원이 추가됩니다. Android 5.0(API 레벨 21)에서 도입된 Android Wear(현재는 Wear OS라고 함) 알림 스택과 동일한 API를 사용합니다.
- 사용자는 인라인 답장을 사용하여 알림 내부에서 직접 답장할 수 있습니다(텍스트를 입력하면 알림의 상위 앱으로 라우팅됨).

#### Android 8.0, API 레벨 26
- 이제 각 알림을 각 채널에 할당해야 합니다.
- 사용자가 앱에서 모든 알림을 끄지 않고도 채널별로 알림을 끌 수 있습니다.
- 활성 상태의 알림이 있는 앱은 홈/런처 화면에서 앱 아이콘 위에 알림 "배지"를 표시합니다.
- 이제 사용자는 창에서 알림을 일시 중지할 수 있습니다. 알림의 자동 시간 초과를 설정할 수 있습니다.
- 알림의 배경색도 설정할 수 있습니다.
- 알림 동작과 관련된 일부 API가 Notification에서 NotificationChannel로 이동되었습니다. 예를 들어 Android 8.0 이상에는 NotificationCompat.Builder.setPriority() 대신 NotificationChannel.setImportance()를 사용합니다.

