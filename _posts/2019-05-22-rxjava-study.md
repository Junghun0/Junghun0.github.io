---
layout: post
title: "[RxJava] RxJava 정리"
subtitle: ' RxJava Study '
author: "JungHoon-Park"
header-style: text
header-mask: 0.3
comments: true
catalog:  true
tags:
  - RxJava
  - Study
---

# RxJavaStudy

## Observable Class

 - Observable 은 Observer Pattern 을 구현
 - Observer Pattern 은 객체의 상태 변화를 관찰하는 관찰자(Observer) 목록을 객체에 등록한다.
 - 상태 변화가 있을 때마다 메서드를 호출하여 객체가 직접 목록의 각 Observer에게 변화를 알린다.
 - Life Cycle 은 존재하지 않으며 보통 단일 함수를 통해 변화만 알린다.

- - -
### Observable의 세 가지 알림
1. **onNext** : Observable이 데이터의 발행을 알린다. 기존의 Observer Pattern 과 동일함
2. **onComplete** : 모든 데이터의 발행을 완료했음을 알린다. onComplete 이벤트는 단 한번만 발생하며, 발생한 후에는 더 이상 onNext 이벤트가 발생하면 안된다.
3. **onError** : Observable 에서 어떤 이유로 에러가 발생했음을 알린다. onError 이벤트가 발생하면 이후에 onNext 및 onComplete 이벤트는 발생하지 않는다. 즉, Observable의 실행을 종료

### just() 함수
 - 인자로 넣은 데이터를 차례로 발행하려고 Observable을 생성
 - 한 개의 값을 넣을 수도 있고 인자로 최대 10개를 넣을 수 있다.
 - 단 타입은 모두 같아야 한다.
 
 ```java
 Observable.just(1, 2, 3, 4, 5, 6)
                .subscribe(System.out::println);
 ```
 - 모든 데이터 발행이 완료되면 onComplete 이벤트 발생
 
### subscribe() 함수

- RxJava는 내가 동작시키기 원하는 것을 사전에 정의해둔 다음 실제 그것이 실행되는 시점을 조절 가능 -> 이때 사용하는 것이 **subscribe()** 이다.
- Observable 은 just() 등의 팩토리 함수로 데이터 흐름을 정의한 후 subscribe() 함수를 호출해야 실제로 데이터를 발행함

```java
public final Disposable subscribe() {
    return subscribe(Functions.emptyConsumer(), Functions.ON_ERROR_MISSING, Functions.EMPTY_ACTION, Functions.emptyConsumer());
}
```

```java
public final Disposable subscribe(Consumer<? super T> onNext) {
    return subscribe(onNext, Functions.ON_ERROR_MISSING, Functions.EMPTY_ACTION, Functions.emptyConsumer());
}
```
    
```java
public final Disposable subscribe(Consumer<? super T> onNext, Consumer<? super Throwable> onError) {
    return subscribe(onNext, onError, Functions.EMPTY_ACTION, Functions.emptyConsumer());
}
```
    
```java  
public final Disposable subscribe(Consumer<? super T> onNext, Consumer<? super Throwable> onError,
Action onComplete) {
    return subscribe(onNext, onError, onComplete, Functions.emptyConsumer());
}
```

1. 인자가 없는 subscribe() 함수는 onNext와 onComplete 이벤트를 무시하고 onError 이벤트가 발생했을 때만 OnErrorNotImplementedException을 던진다. 따라서 Observable로 작성한 코드를 테스트하거나 디버깅할 때 활용
2. 인자가 1개 있는 오버로딩은 onNext 이벤트를 처리한다. 이때도 onError 이벤트가 발생하면 OnErrorNotImplementedException을 던진다.
3. 인자가 2개인 함수는 onNext와 onError 이벤트를 처리한다.
4. 인자가 3개인 함수는 onNext, onError, onComplete 이벤트를 모두 처리할 수 있다.

  - 위의 함수 원형은 모두 Disposable 인터페이스의 객체를 리턴한다.

```java
void dispose()
boolean isDisposed()
```

 - dispose()는 Observable에게 더 이상 데이터를 발행하지 않도록 구독을 해지하는 함수이다.
 - Observable이 onComplete 알림을 보냈을 때 자동으로 dispose()를 호출해 Observable과 구독자의 관계를 끊는다.
 - onComplete 이벤트가 정상적으로 발생했다면 구독자가 별도로 dispose()를 호출할 필요는 없다.

```java
public void usingIsDisposed(){
    Observable<String> source = Observable.just("RED","GREEN","YELLOW","BLUE");

    Disposable disposable = source.subscribe(
            v -> System.out.println("onNext() : value :" + v),
            err -> System.err.println("onError() : err :" + err.getMessage()),
            () -> System.out.println("onComplete()")
);
System.out.println("isDisposed() : "+ disposable.isDisposed());
```

### create() 함수
 - onNext, onComplete, onError 같은 알람을 개발자가 직접 호출해야 한다.

```java
Observable<Integer> source = Observable.create(
        (ObservableEmitter<Integer> emitter) ->{
            emitter.onNext(100);
            emitter.onNext(200);
            emitter.onNext(300);
            emitter.onComplete();
    });
    source.subscribe(System.out::println);
```
 - subscribe() 을 호출하지 않으면 아무것도 출력되지 않는다.

### fromArray() 함수
 - 배열에 들어 있는 데이터를 처리할 때 사용

```java
public void makeFromArray(){
    Integer[] arr = {100,200,300,400};
    Observable<Integer> source = Observable.fromArray(arr);
    source.subscribe(System.out::println);
    System.out.println("---------------------");

    /* int[] 로 선언할 경우 -> toIntegerArray 메소드 사용
    int[] arr = {100,200,300,400};
    Observable<Integer> source = Observable.fromArray(toIntegerArray(arr));
    source.subscribe(System.out::println);
    */
}
private static Integer[] toIntegerArray(int[] intArray){
    return IntStream.of(intArray).boxed().toArray(Integer[]::new);
}
```

### fromIterable() 함수

```java
public void makeIterator(){
    List<String> names = new ArrayList<>();
    names.add("a");
    names.add("b");
    names.add("c");
    names.add("d");

    Observable<String> source = Observable.fromIterable(names);
    System.out.println(source.subscribe().isDisposed());
    source.subscribe(System.out::println);
    System.out.println(source.subscribe().isDisposed());
}
```

### fromCallable() 함수

 - 비동기 클래스나 인터페이스와의 연동
 - Callable 인터페이스
   - 비동기 실행 후 결과를 리턴하는 call() 메서드를 정의
   - Runnable 인터페이스와는 실행 결과를 리턴한다는 점에서 차이가 있다.
 
```java
//람다 표현식을 사용할 때
public void usingCallable() {

    Callable<String> callable = () -> {
        Thread.sleep(2000);
        return "Hello callable";
    };

    Observable<String> source = Observable.fromCallable(callable);
    source.subscribe(System.out::println);
}
```

```java
//람다 표현식을 사용하지 않을때
public void usingCallable2() {
Callable<String> callable = new Callable<String>() {
        @Override
        public String call() throws Exception {
            Thread.sleep(1000);
            return "Hello callable2";
        }
    };

    Observable<String> source = Observable.fromCallable(callable);
        source.subscribe(System.out::println);
    }
```

### fromFuture() 함수

 - 동시성 API 로 비동기 계산의 결과를 구할 때 사용
 - Executor 인터페이스를 구현한 클래스에 Callable 객체를 인자로 넣어 Future 객체를 반환함
 - get() 메서드를 호출하면 Callable 객체에서 구현한 계산 결과가 나올 때까지 블로킹 됨
 - Executors 클래스는 단일 스레드 실행자(SingleThreadExecutor)뿐 아니라 다양한 스레드풀(FixedThreadPool, CachedThreadPool)을 지원함
   - RxJava는 위와 같은 실행자를 사용하기보단 RxJava에서 제공하는 스케줄러를 활용하도록 권장함
 
```java
public void usingFuture(){
    Future<String> future = Executors.newSingleThreadExecutor().submit(() -> {
        Thread.sleep(1000);
        return "Hello Future";
    });

    Observable<String> source = Observable.fromFuture(future);
    source.subscribe(System.out::println);
}
```

### Single Class

- 1
    - Single.fromObservable 을 활용
    - 기존 Observable에서 첫 번째 값을 발행하면 onSuccess 이벤트를 호출한 후 종료 
```java
//1.기존 Observable 에서 Single 객체로 변환하기
        Observable<String> source = Observable.just("Hello Single!");
        Single.fromObservable(source).subscribe(System.out::println);
```

- 2
    - just()를 통해 Observable에 single() 함수를. 호출한다.
    - single() 함수는 default value 를 인자로 갖는다.
    - Observable에서 값이 발행되지 않을 때도 인자로 넣은 기본값을 대신 발행함
```java
//2. single() 함수를 호출해 Single 객체 생성하기
        Observable.just("Hello Single!")
                .single("default Item")
                .subscribe(System.out::println);
```

- 3
    - 여러 개의 데이터를 발행할 수 있는 Observable을 Single 객체로 변환
    - first() 를 호출하면 Observable 이 Single객체로 변환
    - 하나 이상의 데이터를 발행하더라도 첫 번째 데이터 발행 후 onSuccess 이벤트 발생
```java
//3. first() 함수를 호출해 Single 객체 생성하기
        String[] colors = {"Red","Blue","Gold"};
        Observable.fromArray(colors)
                .first("default value")
                .subscribe(System.out::println);
```

- 4
    - empty() 함수를 통해 Single 객체를 생성
    - 첫 번째 데이터 발행 후 onSuccess 이벤트가 발생
```java
//4. empty Observable 에서 Single 객체 생성하기
        Observable.empty()
                .single("default value")
                .subscribe(System.out::println);
```

- 5
    - take() 함수를 통해 Single 객체를 생성
```java
//5. take() 함수에서 Single 객체 생성하기
Observable.just(new Order("Order_1"),new Order("Order_2"))
        .take(1)
        .single(new Order("default order"))
        .subscribe(System.out::println);
```

### Hot Observable

 - 구독자가 존재 여부와 관계없이 데이터를 발행하는 Observable
 - 여러 구독자를 고려할 수 있다.
 - 예를 들어 마우스 이벤트, 키보드 이벤트, 시스템 이벤트, 센서 데이터 등이 있다.
 - 구현할 때 배압을 고려해야한다.
 	- 배압(back pressure) 는 데이터를 발행하는 속도와 구독자가 처리하는 속도의 차이가 클 때 발생한다.
 
```java
public void usingFuture(){
    Future<String> future = Executors.newSingleThreadExecutor().submit(() -> {
        Thread.sleep(1000);
        return "Hello Future";
    });

    Observable<String> source = Observable.fromFuture(future);
    source.subscribe(System.out::println);
}
```

### Subject Class

- Cold Observable 을 Hot Observable 로 바꿔준다.
- Observable 의 속성과 구독자의 속성이 모두 있다.
- Observable처럼 데이터를 발행할 수도 있고 구독자처럼 발행된 데이터를 바로 처리할 수도 있다.
- AsyncSubject, BehaviorSubject, PublishSubject, ReplaySubject 등이 있다.

### AsyncSubject Class

 - Observable에서 발행한 마지막 데이터를 얻어올 수 있는 Subject 클래스다.
 - 완료되기 전 마지막 데이터에만 관심이 있으며 이전 데이터는 무시한다.
 - AsyncSubject 객체는 정적 팩토리 함수인 create()로 생성한다. (Observable.create() 와 같은 기능)
 
 
```java
AsyncSubject<String> subject = AsyncSubject.create();
    subject.subscribe(data -> System.out.println("SubScriber #1 =>"+data));
    subject.onNext("1");
    subject.onNext("3");
    subject.subscribe(data -> System.out.println("Subscriber #2 =>"+ data));
    subject.onNext("5");
    subject.onComplete();
```

```java
Float[] temperature = {10.1f, 13.4f, 12.5f};
    Observable<Float> source = Observable.fromArray(temperature);

    AsyncSubject<Float> subject = AsyncSubject.create();
    subject.subscribe(data -> System.out.print("Subscriber #1 => "+ data));
    source.subscribe(subject);
```

```java
AsyncSubject<Integer> subject = AsyncSubject.create();
        subject.onNext(10);
        subject.onNext(11);
        subject.subscribe(data -> System.out.println("Subscriber #1 =>" + data));
        subject.onNext(12);
        subject.onComplete();
        subject.onNext(13);
        subject.subscribe(data -> System.out.println("Subscriber #2 =>" + data));
        subject.subscribe(data -> System.out.println("Subscriber #3 =>" + data));
```

### BehaviorSubject Class

 - 구독자가 구독을 하면 가장 최근 값 혹은 기본값을 넘겨주는 클래스
 - createDefault() 함수로 생성

 ```java
AsyncSubject<Integer> subject = AsyncSubject.create();
    subject.onNext(10);
    subject.onNext(11);
    subject.subscribe(data -> System.out.println("Subscriber #1 =>" + data));
    subject.onNext(12);
    subject.onComplete();
    subject.onNext(13);
    subject.subscribe(data -> System.out.println("Subscriber #2 =>" + data));
    subject.subscribe(data -> System.out.println("Subscriber #3 =>" + data));
        
 /*
Subscriber #1 =>6
Subscriber #1 =>1
Subscriber #1 =>3
Subscriber #2 =>3
Subscriber #1 =>5
Subscriber #2 =>5
*/
```

### PublishSubject Class

 - 구독자가 subscribe() 함수를 호출하면 값을 발행하기 시작
 - AsyncSubject 클래스처럼 마지막 값만 발행하거나 BehaviorSubject 클래스처럼 발행한 값이 없을 때 기본값을 대신 발행하지도 않는다.
 - 해당 시간에 발생한 데이터를 그대로 구독자에게 전달받음
 
```java
PublishSubject<String> subject = PublishSubject.create();
    subject.subscribe(data -> System.out.println("Subscriber #1 =>" + data));
    subject.onNext("1");
    subject.onNext("3");
    subject.subscribe(data -> System.out.println("Subscriber #2 =>" + data));
    subject.onNext("5");
    subject.onComplete();
        
    /*
    Subscriber #1 =>1
    Subscriber #1 =>3
    Subscriber #1 =>5
    Subscriber #2 =>5
    */
```

### ReplaySubject Class

 - 구독자가 새로 생기면 항상 데이터의 처음부터 끝까지 발행하는 것을 보장
 - 모든 데이터 내용을 저장해두는 과정 중 메모리 누수가 발생할 가능성을 염두에 두고 사용할 때 주의해야함
 - create() 함수를 이용해 생성

```java
ReplaySubject<String> subject = ReplaySubject.create();
    subject.subscribe(data -> System.out.println("Subscriber #1 ->" + data));
    subject.onNext("10");
    subject.onNext("20");
    subject.subscribe(data -> System.out.println("Subscriber #2 ->" + data));
    subject.onNext("30");
    subject.onComplete();

    /*
    Subscriber #1 ->10
    Subscriber #1 ->20
    Subscriber #2 ->10
    Subscriber #2 ->20
    Subscriber #1 ->30
    Subscriber #2 ->30
    */
```

### RxJava의 제네릭 함수형 인터페이스

|  <center>인터페이스 이름</center> |  <center>포함 메서드</center> |  <center>설명</center> |
|:--------|:--------:|--------:|
|**Predicate< T >** | <center>boolean test(T t) </center> |*t 값을 받아서 참이나 거짓을 반환한다* |
|**Consumer< T >** | <center>void accept(T t) </center> |*t 값을 받아서 처리한다.반환값은 없다.* |
|**Function< T, R >** | <center>R apply(T t) </center> |*t 값을 받아서 결과를 반환한다.* |


### 리액티브 함수 분류

|  <center>연산자 종류</center> |  <center>연산자 함수</center> |
|:--------|:--------:|
|**생성 연산자** | **just()**, **fromXXX()**, **create()**, interval(), range(), timer(), intervalRange(), defer(), repeat()
|**변환 연산자** | **map()**, **flatMap()**, concatMap(), switchMap(), groupBy(), scan(), buffer(), window()
|**필터 연산자** | **filter()**, **take()**, **skip()**, distinct()
|**결합 연산자** | zip(), combineLatest(), merge(), concat()
|**조건 연산자** | amb(), takeUntil(), skipUntil(), all()
|**에러 처리 연산자** | onErrorReturn(), onErrorResumeNext(), retry(), retryUntil()
|**기타 연산자** | **subscribe()**, subscribeOn(), observeOn(), **reduce()**, count()

#### 생성 연산자

- #### interval()
    - 일정 시간 간격으로 데이터 흐름을 생성
    - 일정시간(period)를 쉬었다가 데이터를 발행
```java
public static Observable<Long> interval(long period, TimeUnit unit) {
    return interval(period, period, unit, Schedulers.computation());
}
```
- 첫번째 함수와 동작은 같고 최초 지연 시간을 조절할 수 있다. (initialDelay 0이면 바로 데이터를 발행)
```java
public static Observable<Long> interval(long period, TimeUnit unit, Scheduler scheduler) {
    return interval(period, period, unit, scheduler);
}
```
    
- #### timer()
    - interval() 함수와 유사하지만 한 번만 실행하는 함수
    - 일정 시간이 지난 후에 한 개의 데이터를 발행하고 onComplete() 이벤트가 발생
    - interval()과 마찬가지로 메인 스레드가 아닌 계산 스케줄러에서 실행됨
 
```java
public static Observable<Long> timer(long delay, TimeUnit unit) {
        return timer(delay, unit, Schedulers.computation());
    }
```
- #### range()
    - 주어진 값 n 부터 m 개의 Integer 객체를 발행한다.
    - interval() 과 timer() 는 Long 객체를 발행했지만 range()는 Integer 객체를 발행하는 것이 다르다.
    - 스케줄러에서 실행되지 않는다.
    - 현재 스레드에서 실행함
    - range()는 반복분(for,while)을 대체 할 수 있다.
 
```java
public static Observable<Integer> range(final int start, final int count) {

 }
```
- #### intervalRange()
    - interval() 함수처럼 일정한 시간 간격으로 값을 출력하지만 range() 함수처럼 n 부터 m 개만큼의 값만 생성하고 onComplete 이벤트 발생
    - 리턴타입은 Long
    - interval() 함수처럼 무한히 데이터 흐름을 발행하지 않는다.
    - 계산 스케줄러에서 실행됨
	 	
```java
public static Observable<Long> intervalRange(long start, long count, long initialDelay, long period, TimeUnit unit) {
        return intervalRange(start, count, initialDelay, period, unit, Schedulers.computation());
    }
```
- #### defer() 
    - timer() 함수와 비슷하지만 데이터 흐름 생성을 구독자가 subscribe() 함수를 호출할 때까지 미룰 수 있다.
    - 이때 새로운 Observable 이 생성됨
    - 스케줄러가 NONE 이므로 현재 스레드 에서 실행
    - 인자로는 Callable < Observable< T >> 를 받음
    - Callable 객체이므로 구독자가 subscribe()를 호출할 때까지 call()메서드의 호출을 미룰 수 있다.
```java
@SchedulerSupport(SchedulerSupport.NONE)
    public static <T> Observable<T> defer(Callable<? extends ObservableSource<? extends T>> supplier) {     
    }
```	
- #### repeat()
    - repeat() 함수는 인자를 입력하지 않으면 영원히 반복 실행
    - repeat(N) 함수를 활용해 N번 만큼만 반복 실행하게 할수있다.
    - 동작이 한 번 끝난 다음에 다시 구독하는 방식으로 동작
    - 다시 구독할 때마다 동작하는 스레드의 번호가 달라진다.

### 변환 연산자
- #### map()
    - 입력값을 어떤 함수에 넣어서 원하는 값으로 변환하는 함수
    - 일정시간(period)를 쉬었다가 데이터를 발행
    - 스케줄러를 지원하지 않는다. 즉 현재 스레드에서 실행

```java
@SchedulerSupport(SchedulerSupport.NONE)
public final <R> Observable<R> map(Function<? super T, ? extends R> mapper)
```
- #### flatMap()
    - 입력값을 어떤 함수에 넣어서 원하는 값으로 변환하는 함수
    - 결과가 Observable 로 나온다
    - 스케줄러를 지원하지 않는다. 즉 현재 스레드에서 실행
    - T를 넣으면 여러 개의 R이 나오도록 매핑한다.
    - 먼저 들어온 데이터를 처리하는 도중에 새로운 데이터가 들어오면 나중에 들어온 데이터의 처리 결과가 먼저 출력될 수도 있다.(인터리빙/interleaving)

```java
@CheckReturnValue
    @SchedulerSupport(SchedulerSupport.NONE)
    public final <R> Observable<R> flatMap(Function<? super T, ? extends ObservableSource<? extends R>> mapper) {
        return flatMap(mapper, false);
    }
```
### RxJava 스케줄러 종류
- - -

| 스케줄러 | RxJava 2.x |
|---|:---:|
| `뉴 스레드 스케줄러` | newThread()|
| `싱글 스레드 스케줄러` | **single()** |
| `계산 스케줄러` | computation() |
| `IO 스케줄러` | io() |
| `트램펄린 스케줄러` | trampoline() |
| `메인 스레드 스케줄러` | **지원안함** |
| `테스트 스케줄러` | **지원안함** |
    
### [소스코드링크(github)](https://github.com/Junghun0/RxJavaStudy.git)