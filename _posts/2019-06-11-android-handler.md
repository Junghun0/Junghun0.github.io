---
layout: post
title: "[Android] Handler 사용해보기"
subtitle: ' About Handler '
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

# Handler?

안드로이드는 백그라운드에서 실행되어야 하는 기능을 서비스라는 애플리케이션 구성요소로 정의한다. 하지만 별도의 프로세스가 아닌 동일한 프로세스 내에서 위의 문제를 해결하려면 하나의 프로세스 안에서 여러 개의 작업이 동시 수행되는 멀티 스레드 방식을 사용할 수 있다. 스레드는 동시 수행이 가능한 작업 단위이며, 현재 수행되는 작업 이외의 기능을 동시에 처리하고자 할 때 새로운 스레드를 만들어 처리할 수 있다.<br /><br />
이러한 멀티 스레드 방식은 같은 프로세스 안에 들어 있으면서 메모리 리소스를 공유하게 되므로 효율적인 처리가 가능하지만 동시에 리소스를 접근할 경우에 데드락이 발생하여 시스템이 비정상적으로 동작할 수 없다.<br />
안드로이드에서 UI처리를 위해 사용되는 기본 스레드는 '메인 스레드(Main Thread)'라고 부른다. 이 메인 스레드에서 이미 UI에 접근하고 있으므로 새로 생성한 다른 스레드에서는 핸들러(Handler) 객체를 이용해 메시지를 전달함으로써 메인 스레드에서 처리하도록 만들 수 있다.<br />
**동시 접근에 따른 데드락 문제를 해결하는 가장 간단한 방법은 작업을 순서대로 처리하는 것이다. 이 역할은 메인스레드의 핸들러가 담당하여 처리한다.**<br />

앱을 실행할 때 프로세스가 만들어지면 그 안에 메인 스레드가 함께 만들어지고 최상위에서 관리되는 애플리케이션 구성 요소인 액티비티, 브로드캐스트 리시버 등과 새로 만들어지는 윈도우를 관리하기 위한 메시지 큐(Message Queue)를 실행하게 된다. 이 메시지 큐를 이용해 순차적으로 코드를 수행할 수 있으며, 이렇게 메시지 큐를 이용해 메인 스레드에서 처리할 메시지를 전달하는 역할을 담당하는 것이 핸들러 클래스이다.<br />
결국 핸들러는 실행하기를 원하는 특정 기능이 있을 때 핸들러가 포함되어 있는 스레드에서 순차적으로 실행시킬 때 사용하게 된다. 핸들러를 이용하면 특정 메시지가 미래의 어떤 시점에 실행되도록 스케줄링 할 수도 있다.<br />
<img width="732" alt="스크린샷 2019-06-11 오후 1 17 10" src="https://user-images.githubusercontent.com/30828236/59243487-4a63c880-8c4b-11e9-8dbb-18ab7efcea65.png">
**[핸들러를 사용할 때 필요한 세 가지 단계]**<br /><br />

새로 만든 스레드(스레드 #1)가 수행하려는 정보를 메인 스레드로 전달하기 위해서는 먼저 핸들러가 관리하는 메시지 큐에서 처리할 수 있는 메시지 객체 하나를 참조해야 한다. 이 첫 번째 과정에서 obtainMessage() 메소드를 이용할 수 있으며 호출의 결과로 메시지 객체를 리턴받게 된다.<br />
이 메시지 객체에 필요한 정보를 넣은 후 sendMessage() 메소드를 이용해 메시지 큐에 넣을 수 있다. 메시지 큐에 들어간 메시지는 순서대로 핸들러가 처리하게 되며 이때 handleMessage() 메소드에 정의된 기능이 수행된다. 이때 handleMessage()에 들어있는 코드가 수행되는 위치는 새로 만든 스레드가 아닌 메인 스레드가 된다.<br />

~~~java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_sample1);
    handler = new ProgressHandler();
    progressBar = findViewById(R.id.progress_bar);
    textView = findViewById(R.id.textView);
    isRunning = true;
}

@Override
protected void onStart() {
    super.onStart();
    progressBar.setProgress(0);
    Thread thread1 = new Thread(new Runnable() {
        @Override
        public void run() {
            try{
                for (int i = 0; i < 20 && isRunning; i++) {
                    Thread.sleep(1000);
                    Message msg = handler.obtainMessage();
                    handler.sendMessage(msg);
                }
            }catch (Exception e){
                Log.e("MainActivity",""+e.toString());
            }
        }
    });
    isRunning = true;
    thread1.start();
}

@Override
protected void onStop() {
    super.onStop();
    isRunning = false;
}

public class ProgressHandler extends Handler{
    public void handleMessage(Message msg){
        progressBar.incrementProgressBy(5);
        if (progressBar.getProgress() == progressBar.getMax()){
            textView.setText("Done");
        }else{
            textView.setText("Working..."+ progressBar.getProgress());
        }
    }
}
~~~

**핸들러의 메시지 전달 함수**
- Handler.sendEmptyMessage(int what)<br />
Message what(ID) 를 사용할 경우 사용하는 메서드<br />

- Handler.sendMessage(Message msg)<br />
Message what, arg1, obj 등 ID와 정보 등을 같이 사용하는 메서드

<img width="350" src="https://user-images.githubusercontent.com/30828236/59356923-d9183880-8d64-11e9-93b1-092479c51916.png">
<img width="350" src="https://user-images.githubusercontent.com/30828236/59356924-d9183880-8d64-11e9-9224-4449ccae3936.png">







