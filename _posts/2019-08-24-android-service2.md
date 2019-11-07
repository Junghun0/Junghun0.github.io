---
layout: post
title: "[Android] Service 란(2)"
subtitle: ' About Service '
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

## 백그라운드 스레드에서 작업을 오래 하는 문제
먼저 예제 코드를 보자. 일반적으로는 이처럼 코드를 작성하지 않는다.
~~~java
public class LifecycleApplication extends Application {
    private static final long SLEEP_TIME = 10000L;
    
    @Override
    public void onCreate() {
        super.onCreate();
        Log.d(TAG, "Application Create");
        Thread thread = new Thread(new Runnable(){

            @Override
            public void run() {
                Log.d(TAG, "Thread start");
                SystemClock.sleep(SLEEP_TIME);
                Log.d(TAG, "10 Seconds After");
                SystemClock.sleep(SLEEP_TIME);
                Log.d(TAG, "20 Seconds After");
                SystemClock.sleep(SLEEP_TIME);
                Log.d(TAG, "30 Seconds After");
                SystemClock.sleep(SLEEP_TIME);
            }
        });
        thread.start();
        Log.d(TAG,"Application Created");
    }
}
~~~
앱이 시작되면서 선행 작업이 필요한 경우가 있다. 선행 작업은 Application에서 할 수 있는 작업들이라 Application에서 스레드를 시작하는 코드를 가끔 봤을 것이다. 실행 시간이 30초나 걸리기 때문에 UI를 블로킹하지 않기 위해 백그라운드 스레드로 작업을 진행한다. 그런데 이렇게 하면 생각지 못한 문제가 생긴다.<br/>
이 앱이 스레드 실행을 마치기 전에 백 키로 앱을 빠져나오거나 홈 키로 나가서 다른 앱을 오랫동안 사용하면, 프로세스가 종료될 수도 있다는 문제가 있다. 메모리가 부족할 경우에 LMK(low memory killer)는 우선순위가 높지 않은 프로세스를 종료하는데, LMK가 스레드 실행 도중에 앱 프로세스를 종료할 수 있기 때문에 30초나 걸리는 작업의 안정성을 보장할 수 없다.

## 프로세스 우선순위
### 1. 포그라운드 프로세스
안드로이드 컴포넌트가 포그라운드에서 실행되는 프로세스이다. `사용자와 상호 작용하는 액티비티를 가지고 있거나`, startForeground()를 호출한 `포그라운드 서비스를 가지고 있거나`, `생명주기 메서드(onCreate, onStart, onStartCommand, onDestroy)를 실행 중인 서비스를 가지고 있거나`, `onReceive()를 실행하는 브로드캐스트 리시버를 가지고 있는 경우`이다. 메모리가 부족할 때에도 가장 마지막까지 남을 수 있는 프로세스 이다.

### 2. 가시(visible)프로세스
포그라운드 컴포넌트를 가지고 있지는 않지만 사용자가 보는 화면에 아직 영향이 있는 프로세스이다. 액티비티로 보면 onPause()까지 실행되었지만 가시 상태인 것이다.(다른 프로세스의 다이얼로그 테마나 투명한 액티비티가 가렸을 때). 가시 액티비티에 바운드된 서비스를 실행 중인 프로세스도 가시 프로세스에 해당한다.

### 3. 서비스 프로세스
startService()로 실행했지만 위의 카테고리에는 들어가지 않는 서비스가 실행 중인 프로세스이다. 이런 것들은 사용자가 지금 보고 있는 것과 직접적인 연관은 없다.

### 4. 백그라운드 프로세스
액티비티가 종료된 것은 아니지만 사용자에게 더이상 보이지 않고(예를 들어 홈 키를 누르면 onStop()까지 불리고 태스크가 백그라운드로 이동) 활성화된 컴포넌트가 없는 프로세스이다. 보통 백그라운드 프로세스가 여러 개 존재한다.

### 5. 빈(empty) 프로세스
사용자가 백 키로 액티비티를 모두 종료하고 활성화 된 컴포넌트가 없다면 빈 프로세스가 된다. 이런 프로세스를 메모리에 한동안 유지하는 이유는 다음에 컴포넌트를 다시 띄울 때 빠르게 띄울 수 있도록 캐시로 사용하기 위해서다. 우선순위가 낮아서 리소스가 부족하면 가장 먼저 강제 종료 대상이 된다.

**우선 순위상 위 단계로 올라갈 수 있다면 작업의 안정성을 보장할 수 있다. 앞의 예와 같이 스레드에서 30초간 작업한다면 백 키로 모든 화면을 종료했을 때 앱 프로세스는 빈(empty) 프로세스가 되어서 LMK에 의해 언제든 종료될 수 있는 상태가 된다.**
위의 코드는 서비스를 사용해서 아래와 같이 변경할 수 있다.
<br/>
~~~java
public class LifecycleApplication extends Application {
    
    @Override
    public void onCreate() {
        super.onCreate();
        Log.d(TAG, "Application Create");
        startService(new Intent(this, SleepService.class));
        Log.d(TAG, "Application Created");
    }
}
~~~
서비스에서 백그라운드 스레드를 시작한다.

~~~java
public class SleepService extends Service {

    private static final long SLEEP_TIME = 10000L;

    @Override
    public void onCreate() {
        Log.d(TAG, "Service onCreate");
        Thread thread = new Thread(new Runnable() {

            @Override
            public void run() {
                Log.d(TAG, "Thread start");
                SystemClock.sleep(SLEEP_TIME);
                Log.d(TAG, "10 Seconds After");
                SystemClock.sleep(SLEEP_TIME);
                Log.d(TAG, "20 Seconds After");
                SystemClock.sleep(SLEEP_TIME);
                Log.d(TAG, "30 Seconds After");
                SystemClock.sleep(SLEEP_TIME);
            }
        });
        thread.start();
    }
    @Override
    public IBinder onBind(Intent intent) {
        return null;
    }
}
~~~

이렇게 하면 서비스의 생명주기 메서드가 실행 중일 때는 우선순위가 가장 높은 포그라운드 프로세스에 있다가, onStartCommand() 메서드가 리턴되고 난 이후에 세 번째 우선순위인 서비스 프로세스에 남는다. LMK에 의해 언제든 제거되는 프로세스인 빈 프로세스가 아니므로 스레드에서 하는 작업을 무사히 종료할 수 있는 가능성이 높아진다. 사용자가 최근 앱 목록에서 제거해도 마찬가지다. 프로세스가 강제 종료되면 서비스는 onStartCommand() 리턴 값에 따라 재시작 여부를 결정하는데, 디폴트 리턴 값은 START_STICKY로 서비스를 재시작한다.

## 서비스는 UI 스레드에서 실행
서비스를 언급할 때 자주 나오는 얘기가 백그라운드상에서 실행되는 컴포넌트라는 것이다. 서비스는 액티비티처럼 눈에 보이는 가시 컴포넌트가 아니라는 의미로 백그라운드를 이야기하는 것이지, 서비스 자체가 메인 스레드가 아닌 별도 스레드에서 실행하는 것으로 착각하면 안 된다. 다시 말하면 서비스의 생명주기 메서드는 UI 스레드에서 실행되고 이 때문에 다른 UI 이벤트가 지연되는 경우가 생길 수 있다. 따라서 서비스에서 UI를 블로킹하는 작업이 있다면 백그라운드 스레드를 생성해서 작업을 진행해야 한다.

## 서비스는 단일 인스턴스로 실행
서비스는 앱에서 1개의 인스턴스밖에 생기지 않는다. 따라서 우리는 일부러 싱글톤 객체를 만들고 그 안에서 백그라운드 스레드를 실행할 필요가 없다. 훨씬 안정적으로 동작하는 컴포넌트를 활용하면 된다. 예를 들어 어떤 앱에서는 싱글톤 객체를 서비스와 다른 클래스에서 함께 사용하고 있었다. 그런데 서비스에 모든 작업을 위임하면서 이 싱글톤 객체는 더 이상 싱글톤으로 만들 필요가 없이 서비스의 멤버 변수로만 남게 되었다. 그럼으로써 싱글톤만 제거된 것이 아니라 전체적인 구조가 단순해졌다.

## 서비스 시작 방법
Context에는 서비스를 시작하는 방법으로 startService() 와 bindService() 메서드 2가지가 있다.

### startService()
Context의 startService() 메서드로 시작된다. 이것 역시 startService()를 호출하는 시점에 서비스가 바로 시작되지 않는다. 메인 Looper의 MessageQueue에 Message가 들어가서 메인 스레드를 쓸 수 있는 시점에 서비스가 시잔된다. startService() 메서드는 곧바로 ComponentName을 리턴하고 다음 라인을 진행한다. startService()는 Intent Bundle에 파라미터를 전달하고 서비스에 작업하도록 요청하는 역할을 할 뿐이다.

#### onCreate() 와 onStartCommand() 호출
startService() 메서드를 호출하면 service 가 처음 생성되는 경우에는 onCreate()를 거쳐서 onStartCommand() 메서드를 실행한다. 그 이후에 startService()를 호출하면 onCreate() 메서드는 거치지 않고 onStartCommand() 메서드가 실행된다. onCreate() 메서드는 이름 그대로 명령을 매번 처리하는 역할을 한다. 그래서 Activity와는 다르게 Service의 onCreate() 메서드에는 전달된 Intent를 사용할 수 없다.

#### onStartCommand()에서 백그라운드 스레드 실행
명령을 던져놓고 서비스에서 그 명령을 알아서 실행하는 작업에 startService()를 사용한다고 생각해보자. 표준 패턴은 onStartCommand()에서 백그라운드 스레드를 생성하고 스레드에서 작업을 진행하는 것이다. 기능 자체는 단순하다. 사용자가 버튼을 누르면 서비스에서 작업을 하고 작업 중에는 화면에 '작업 중'임을 알리는 애니메이션을 실행한다. 애니메이션은 UI 동작이기 때문에 메인 스레드에서만 정상 동작한다. 그런데 Service의 onStartCommand() 메서드에서 백그라운드 스레드를 사용하지 않고 작업을 진행해서 메인스레드를 점유했기 때문에 '작업 중'인 애니메이션이 동작하지 않는다.

#### 브로드캐스트로 컴포넌트 간 통신
서비스에서 작업 진행 상황에 따라 액티비티에 메시지를 보내려면 일반적으로 브로드캐스트를 사용한다. 예를 들어, 서버와 동기화를 하는 SyncService가 있는데, 화면에서 버튼이나 메뉴로 SyncService를 시작한다. 동기화 도중에는 ProgressBar로 '진행 중'을 표시하고, 동기화가 끝나면 ProgressBar를 없애고 종료 메시지를 표시하려고 한다. 이때 액티비티에서는 브로드캐스트 리시버를 등록하고 서비스에서는 sendBroadcast()를 실행한다.

## 서비스 재시작 방식
가용 메모리가 낮거나 포커스를 갖고 있는 액티비티의 시스템 리소스를 복구해야 할 때 안드로이드 시스템은 서비스를 강제 종료시킬 수 있다. startService()는 강제 종료 후 가능한 한 빨리 시스템에서 서비스를 재시작한다. 서비스도 프로세스 우선순위에 따라 언제든지 종료될 수 있는데, 시스템이 알아서 재시작한다고만 알고 넘어가면 안 된다. 서비스가 언제 재시작하는지, 재시작을 안하는 조건은 무엇인지 알아야 서비스를 안정적으로 다룰 수 있다.

### onStartCommand() 메서드의 리턴 상수
startService() 에서는 onStartCommand() 메서드에서 리턴하는 int 상수를 가지고서 재시작 방식을 제어한다. onStartCommand() 메서드의 시그니처는 다음과 같다.
~~~java
public int onStartCommand(Intent intent, int flags, int startId)
~~~
리턴 값으로 사용되는 int 상수를 하나씩 알아보자.

#### START_NOT_STICKY
onStartCommand() 메서드가 리턴된 상태에서 강제 종료되면 재시작하지 않는다. 명시적으로 startService()를 실행할 때만 의미 있는 작업에 사용한다. 예를 들어 화면에 보여줄 뉴스를 API로 가져와서 저장할 수 있는데, 메모리 이슈로 서비스가 강제로 종료되었다면 startService() 명령을 기다려서 최신 뉴스를 다시 가져오는 것이 나을 것이다.

#### START_STICKY
onStartCommand() 메서드의 기본 리턴 값이다. 정상적으로 종료되지 않았을 때 재시작한다. 재시작 시에는 다시 onStartCommand()를 호출하는데 이때 Intent 파라미터가 null로 전달된다. startCommand() 메서드에서 전달된 Intent 값을 사용할 때는 재시작하면서 NullPointerException 발생 가능성이 있다. 따라서 START_STICKY는, 전달된 Intent를 사용하지 않고 내부 상태 변수만 사용하는 서비스에 적합하다. 예를 들어, SNS 앱에서 새로운 메시지가 몇 개나 왔는지 정기적으로 API를 호출해서 확인한다면 Intent 파라미터가 전달될 필요가 없다. 이 때는 재시작할 때 Intent 파라미터가 null이어도 무관하다.

#### START_REDELIVER_INTENT
재시작하면서 onStartCommand()에 Intent를 다시 전달하여 실행한다. 어떻게든 해당 파라미터를 가지고 실행해야 하는 서비스가 이에 해당한다. 쇼핑몰 앱에서 API를 통해 특정 상품 목록을 가져온 후 DB에 저장하는 경우를 예로 들 수 있다.


## 멀티 스레드 이슈
startService() 에서 멀티 스레드 이슈를 조심해야 한다. 여러 곳에서 startService()를 동시에 호출할 수 있다. 어차피 UI 동작은 단일 스레드 모델을 따르기 때문에 onCreate(), onStartCommand() 메서드가 한번에 하나씩만 호출되지만, 스레드는 여러 개가 동시에 실행될 수 있다.

### 멤버변수는 최소한으로 사용
onStartCommand() 에서 백그라운드 스레드를 시작한다면, 여러 스레드가 동시에 실행될 수 있고 이때 값을 잘못 공유하면 문제가 발생할 여지가 생긴다. 앞에서 예를 든 쇼핑몰 앱을 생각해보자. 각 상점마다 API를 통해 상품 정보를 가져오는데, 전달된 Intent의 상점 id를 Service의 멤버 변수로 쓰면 의도치 않게 id 값이 도중에 변경돼서 데이터가 잘못 저장된다.

### 여러 작업 진행 중에는 stopSelfResult() 메서드 사용
여러 클라이언트에서 startService()를 실행한다면 모든 작업이 끝났을 때 서비스를 종료하려고 한다. 모든 작업이 끝나는 시점을 알 수 있을까? 별 고려 없이 매 작업마다 stopSelf() 메서드를 호출하면 진행 중인 작업에서 문제가 발생한다. 이 경우를 대비해서 stopSelf() 메서드의 변종인 stopSelfResult(int startId) 메서드가 존재한다. startId는 onStartCommand()에 전달된 값으로 이 startId가 가장 최근에 시작된 것이라면 그때에만 서비스를 종료한다. 이 메서드를 사용하면 각각의 작업이 끝날 때마다 stopSelfResult()를 실행해도 더 안전해진다.

## IntentService 클래스
서비스에서 멀티 스레딩이 필요한 경우가 많지는 않다. 동시에 여러 요청을 처리할 필요가 없다면 IntentService를 활용해야한다. IntentService는 내부적으로 1개의 백그라운드 스레드를 가지고 전달된 Intent를 순차적으로 처리한다(내부적으로 HandlerThread를 사용한다.)
IntentService에서는 백그라운드 스레드에서 실행되는 onHandleIntent(Intent) 메서드만 구현하면 된다.
~~~java
public class NewsReaderService extends IntentService {
    public NewsReaderService(){//1
        super("NewsReader");
    }

    @Override
    protected void onHandleIntent(Intent intent) {
        ...
    }
}
~~~
IntentService에는 기본 생성자가 없기 때문에 1 과 같이 생성자도 추가해야 한다. 그렇지 않으면 'no empty constructor exception'이 발생한다. 생성자에 들어가는 name 파라미터는 백그라운드 스레드의 스레드명으로 사용된다.

IntentService에서 내부적으로 구현한 onStartCommand() 메서드의 기본 리턴 값은 START_NOT_STICKY이다. 이 값을 변경하려면 생성자에서 아래 메서드를 호출하면 된다.
~~~java
setIntentRedelivery(true)
~~~
IntentService의 구조는 단순하다. onCreate() 에서 HandlerThread를 생성하고 시작하면서 HandlerThread의 Looper와 연결된 Handler를 만든다. onStartCommand() 메서드는 실행될 때마다 Handler에 메시지를 보내고 Handler의 handleMessage() 에서는 IntentService의 onHandleIntent() 메서드를 실행한다. handleMessage() 메서드에서는 onHandleIntent() 가 끝나면 바로 stopSelf(int startId)를 호출해서 서비스를 종료한다.





