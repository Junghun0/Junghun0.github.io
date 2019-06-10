---
layout: post
title: "[Android] Service 란"
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

# Service 란?
화면 없이 `백그라운드에서 실행되는 프로세스`를 의미한다. 화면이 없다는 점을 제외하면 액티비티처럼 하나의 `애플리케이션 구성 요소`로 동작한다.<br /><br />
서비스도 애플리케이션의 구성 요소이므로 시스템에서 관리한다. 따라서 새로 만든 서비스는 항상 매니페스트 파일에 등록해야 한다. 서비스를 실행 시킬 때는 메인 액티비티에서 `startService()` 메소드를 호출하면 된다.<br /><br />
서비스가 하는 중요한 역할은 단말이 항상 실행되어 있는 상태로 다른 단말과 데이터를 주고받거나 단말의 상태를 모니터링하는 것이다. 따라서 서비스를 한 번 실행해 두면 실행된 상태가 계속 유지되어야 한다. 이를 위해 서비스가 비정상적으로 종료되더라도 시스템이 자동으로 재실행된다.<br /><br />
그런데 서비스가 한 번 실행된 후 종료되지 않고 계속 실행 중인 상태라면 `startService()` 메소드를 여러 번 호출하더라도 서비스의 상태에는 변화가 없게된다. 따라서 `startService()`는 서비스를 시작하는 목적보다는 `인텐트를 전달하는 목적`으로 더 많이 사용된다.<br /><br />
예를 들어, 액티비티에서 서비스 쪽으로 데이터를 전달하고 싶은 경우 인텐트 객체를 만들고 부가 데이터를 넣은 후 `startService() 메소드를 호출`하면서 전달하면 된다. 이때 서비스 객체가 메모리에 만들어져 있으면 onCreate() 메소드가 호출되지 않으므로 `onStartCommand() 메소드를 사용해 전달 받은 인텐트를 처리`하게 된다.<br />

## Service 콜백 메서드
서비스를 생성하려면 `Service`의 서브클래스를 생성해야 한다. 구현에서는 서비스 수명 주기의 주요 측면ㅇ르 처리하는 콜백 메서드를 몇 가지 재정의해야 하며 서비스에 바인드할 구성 요소에 대한 메커니즘을 제공해야 한다.

- **onStartCommand()**
시스템이 이 메서드를 호출하는 것은 또 다른 구성 요소가 서비스를 시작하도록 요청하는 경우이다. 이때 `startService()`를 호출하는 방법을 사용한다. 이 메서드가 실행되면 서비스가 시작되고 백그라운드에서 무기한으로 실행될 수 있다. 이것을 구현하면 서비스의 작업이 완료되었을 때 해당 서비스를 중단하는 것은 개발자 본인의 책임이며, 이때 `stopSelf()` 또는 `stopService()` 를 호출하면 된다.

- **onBind()**
시스템이 이 메서드를 호출하는 것은 또 다른 구성 요소가 해당 서비스에 바인드되고자 하는 경우이다. 이때 `bindService()`를 호출하는 방법을 쓴다. 이 메서드를 구현할 때에는 클라이언트가 서비스와 통신을 주고받기 위해 사용할 인터페이스를 제공해야 한다. 이때 `IBinder`를 리턴하면 된다. 이 메서드는 항상 구현해야 하지만, 바인딩을 허용하지 않고자 하면 null을 리턴하면 된다.

- **onCreate()**
시스템이 이 메서드를 호출하는 것은 서비스가 처음 생성되어 일회성 설정 절차를 수행하는 경우이다. (`onStartCommand()`또는`onBind()`호출하기 전). 서비스가 이미 실행 중인 경우, 이 메소드는 호출되지 않는다.

- **onDestroy()**
시스템이 이 메서드를 호출하는 것은 해당 서비스를 더 이상 사용하지 않고 소멸시키는 경우이다. 서비스에서 이를 구현해야 스레드, 리스너, 리시버 등 모든 리소스를 정리할 수 있다. 이는 서비스가 수신하는 마지막 호출이다.

## 사용해보기
### Service
#### **onStartCommand()** 메서드의 리턴 값
- `START_NOT_STICKY`<br />
시스템이 서비스를 `onStartCommand()` 리턴 후에 중단시키면 서비스는 재생성하면 안된다. 다만 전달할 인텐트가 있는 경우는 예외이다. 이는 서비스가 불필요하게 실행되는 일을 피할 수 있는 가장 안전한 옵션이며, 애플리케이션이 완료되지 않은 모든 작업을 단순히 재시작할 수 있을 때 좋다.
- `START_STICKY`<br />
시스템이 서비스를 `onStartCommand()` 반환 후에 중단시키는 경우, 서비스를 재생성하고 `onStartCommand()`를 호출하되 마지막 인텐트를 다시 전달하지는 마세요. 그 대신, 시스템이 `null 인텐트`로 `onStartCommand()`를 호출합니다. 다만, 서비스를 시작할 보류 인텐트가 있는 경우만은 예외이며, 이 경우 이들 인텐트가 전달됩니다. 이것은 명령을 실행하지는 않지만 무기한으로 실행 중이며 작업을 기다리고 있는 미디어 플레이어(또는 그와 비슷한 서비스)에 적합합니다.
- `START_REDELIVER_INTENT`<br />
시스템이 `onStartCommand()` 반환 후에 서비스를 중단시키는 경우, 서비스를 재생성하고 이 서비스에 전달된 마지막 인텐트로 `onStartCommand()`를 호출하세요. 모든 보류 `인텐트가 차례로 전달`됩니다. 이것은 즉시 재개되어야 하는 작업을 능동적으로 수행 중인 서비스(예를 들어 파일 다운로드 등)에 적합합니다.

#### 서비스바인딩?
startService() 메소드 대신 `bindService()`메소드를 통해 시작되는 서비스를 서비스 바인딩(`Service Bind` or `Bound Service`)라고 한다.<br />
- 이 서비스는 마치 클라이언트-서버 관계처럼 동작한다. 서비스가 서버 역할을 한다.
- 액티비티는 서비스에 어떠한 요청을 할 수 있고, 서비스로부터 어떠한 결과를 받을 수 있다.
- 프로세스간 통신에도 사용된다.
- 서비스 바인딩은 연결된 액티비티가 사라지면 서비스도 소멸된다.(`백그라운드에서 무한히 실행되지 않는다.`)
- 하나의 서비스에 다수 액티비티를 연결할 수 있다.
- 애플리케이션 안의 기능을 외부에 제공하는 경우에 많이 사용된다.<br /><br />
**구현 및 동작** <br />
- 서비스 바인딩 객체를 생성하려면 콜백 메소드인 onBind()를 구현해야 한다.
- `onBind()`는 `IBinder`를 반환하는데, 바로 이 객체가 서비스와 클라이언트 사이의 인터페이스 역할을 한다.
- 클라이언트가 `bindService()`를 호출하면, 클라이언트가 서비스에 연결되면서 `IBinder`가 반환되고, 클라이언트가 IBinder를 받으면 이 인터페이스를 통해 주고 받는 것이 가능해진다.
- 서비스가 제공하는 다른 메소드 호출가능하다.
- 여러 클라이언트가 하나의 서비스에 동시 접속이 가능하다.
- 클라이언트가 서비스와의 접속을 끝내려면 `unbindService()`를 호출한다.
- 서비스에 연결된 클라이언트가 하나도 남아있지 않으면 서비스를 소멸시킨다.<br />
<img width="421" src="https://user-images.githubusercontent.com/30828236/59193024-412d1a00-8bbf-11e9-998d-7c1783dbc2f1.png">
**서비스 생명주기. 왼쪽 다이어그램은 `startService()`로 생성된 경우이고, 오른쪽 다이어그램은 서비스가 `bindService()`로 생성된 경우의 수명주기를 나타낸다.**<br /><br />
- 서비스의 전체 수명 주기는 `onCreate()`가 호출된 시점과 `onDestroy()`가 반환된 시점 사이에 일어납니다. 액티비티와 마찬가지로 서비스는 `자신의 초기 설정을 onCreate()에서 수행`하며, 남은 리소스를 모두 `onDestroy()`에서 릴리스합니다. 예를 들어 음악 재생 서비스의 경우 음악이 재생될 스레드를 onCreate()에서 생성하고, 그럼 다음 onDestroy()에서 해당 스레드를 중단할 수 있습니다.
`onCreate()` 및 `onDestroy()` 메서드는 모든 서비스에 대해 호출됩니다. 이는 서비스가 startService()로 생성되었든 bindService()로 생성되었든 상관없이 적용됩니다.
- 서비스의 활성 수명 주기는 `onStartCommand()` 또는 `onBind()`에 대한 호출로 시작됩니다. 각 메서드에 Intent가 전달되며 이것은 각각 `startService()` 또는 `bindService()`에 전달된 것입니다.
서비스가 시작되면 수명 주기 전체가 종료되는 것과 동시에 활성 수명 주기도 종료됩니다 (서비스는 onStartCommand()가 반환된 뒤에도 여전히 활성 상태입니다). 서비스가 바인드된 경우, `onUnbind()`가 반환되면 활성 수명 주기가 종료됩니다.

#### 소스코드
- 서비스 파일 생성
<img width="437" src="https://user-images.githubusercontent.com/30828236/59186409-bd1e6680-8bad-11e9-9c89-f86c5aeb4c7a.png"/>
이후에 Manifest 에 추가됨<br />

~~~xml
<service
    android:name=".MyService"
    android:enabled="true"
    android:exported="true"></service>
~~~

- MainActivity<br />
두개의 버튼으로 서비스 시작과 서비스 중지기능을 추가한다.
~~~java
public class MainActivity extends AppCompatActivity {

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }

    public void onStartService(View view) {
        Intent intent = new Intent(this, MyService.class);
        startService(intent);
    }

    public void onStopService(View view) {
        Intent intent = new Intent(this, MyService.class);
        stopService(intent);
    }
}
~~~

- MyService
~~~java
public class MyService extends Service {
    private Thread mThread;
    private int mCount = 0;

    public MyService() {
    }

    //startService() -> onStartCommand 호출됨
    @Override
    public int onStartCommand(Intent intent, int flags, int startId) {
        if (mThread == null){
            mThread = new Thread("My Thread"){
                @Override
                public void run() {
                    for (int i = 0; i < 100; i++) {
                        try{
                            mCount++;
                            Thread.sleep(1000);
                        }catch (InterruptedException e){
                            break;
                        }
                        Log.d("My Service","서비스 동작 중"+mCount);
                    }
                }
            };
            mThread.start();
        }
        return START_STICKY;
        //서비스가 예기치않게 종료되어도 서비스가 자동으로 재시작함
    }

    //onStopService() -> onDestroy()호출됨
    @Override
    public void onDestroy() {
        super.onDestroy();
        Log.d("My Service","onDestroy() 서비스 중지");
        if (mThread != null){
            mThread.interrupt();
            mThread = null;
            mCount = 0;
        }
    }

    @Override
    public IBinder onBind(Intent intent) {
        // TODO: Return the communication channel to the service.
        throw new UnsupportedOperationException("Not yet implemented");
    }
}
~~~

### Intent Service
- **Intent Service**
`Service`의 서브클래스로, Worker Thread를 사용하여 모든 시작 요청을 처리하되 한 번에 하나씩 처리한다. 서비스가 여러 개의 요청을 동시에 처리하지 않아도 되는 경우 Intent Service 를 사용하는 것이 좋다.
  - 애플리케이션의 기본 스레드와는 별도로, `onStartCommand()`에 전달된 모든 인텐트를 실행하는 기본 작업자 스레드를 생성한다.
  - 한 번에 인텐트를 하나씩 `onHandleIntent()` 구현에 전달하는 작업 큐를 생성하므로, 다중 스레딩에 대해 걱정할 필요가 없다.
  - 시작 요청이 모두 처리된 후 서비스를 중단하므로 `stopSelf()`를 호출할 필요가 없다.
  - `onBind()`의 기본 구현을 제공하여 null을 반환하도록 합니다.
  - `onStartCommand()`의 기본 구현을 제공하여, `Intent`를 작업 큐로 보내고 그 다음은 `onHandleIntent()`구현으로 보낸다.

#### 소스코드
- MainActivity <br />
버튼을 하나 추가하여 Intent Service 를 시작시키도록 한다.
~~~java
public void onStartIntentService(View view) {
    //내부적으로 순서대로 동작한다. 스스로 자기 자신을 중지시킨다.
    Intent intent = new Intent(this, MyIntentService.class);
    startService(intent);
}
~~~

- MyIntentService <br />
~~~java
public class MyIntentService extends IntentService {
    public MyIntentService() {
        super("MyIntentService");
    }


    @Override
    protected void onHandleIntent(Intent intent) {
        for (int i = 0; i < 5; i++) {
            try{
                Thread.sleep(1000);
            }catch (InterruptedException e){
                Thread.currentThread().interrupt();
            }
            Log.d("My Intent Service","인텐트 서비스 동작 중"+i);
        }
    }
}
~~~








