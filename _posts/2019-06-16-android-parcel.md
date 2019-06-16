---
layout: post
title: "[Android] Serializable & Parcel"
subtitle: 'Android Serializable & Parcel Sample'
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

Android Serializable & Parcel 사용해보기

---

# 직렬화란?
직렬화의 개념은 간단하다. 객체는 메모리에 존재하는 것이다. 이렇게 메모리에 존재하는 것을 파일 혹은 네트워크를 통해 저장 및 전송할 수 있을까. 객체 자체는 메서드와 변수들이 존재하고 함수와 변수는 메모리의 다른 영역에 저장된다. 이것을 그대로 파일이나 네트워크 등으로 전송한다는 것은 쉽지않다. 직렬화는 메서드는 신경쓰지 않고 단지 변수를 직렬화 시킨다. 함수를 포함한 클래스는 데이터가 아니다. 단지 코드일 뿐이다. 하지만 변수는 데이터코드이므로 코드를 가질 수 없다. 즉, 직렬화 개념은 `변수 데이터를 순차적으로 byte코드로 나열`하는 것이다.

### 안드로이드에서 직렬화 사용해보기
**전달할 데이터 준비**<br />
`Serializable`은 `Android SDK`가 아닌 표준 Java의 인터페이스이다.
이 인터페이스를 구현한 클래스의 객체는 다른 액티비티로 전송할 준비가 된다. `Serializable`은 해당클래스가 직렬화 대상이라고 알려주기만 할 뿐 어떤한 메서드도 가지지 않는 단순한 `마커 인터페이스(Marker Interface)`이므로, 쉽게 사용할 수 있다.
`Serializable`은 내부에서 `Reflection`을 사용하여 직렬화를 처리한다. `Reflection`은 프로세스 동작 중에 사용되며 처리 과정 중에 많은 추가 객체를 생성한다. 이 많은 쓰레기들은 GC의 타겟이 되고 GC의 과도한 동작으로 인해 성능저하 및 배터리 소모가 발생한다.<br />
~~~java
public class Person implements Serializable {
    private String name;
    private int age;
    private String school;
    private int tall;
    private double weight;

    public Person(String name, int age, String school, int tall, double weight) {
        this.name = name;
        this.age = age;
        this.school = school;
        this.tall = tall;
        this.weight = weight;
    }

    @Override
    public String toString() {
        return "Person{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", school='" + school + '\'' +
                ", tall=" + tall +
                ", weight=" + weight +
                '}';
    }
}
~~~

**데이터를 전달할 액티비티**

~~~java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_sample4);

    Person person = new Person("Name", 27, "Hansung", 180, 100);
    Button button = findViewById(R.id.serialize_send);
    button.setOnClickListener(v -> {
        Intent intent = new Intent(getApplicationContext(), Sample4_1Activity.class);
        Bundle bundle = new Bundle();
        bundle.putSerializable("person", person);
        intent.putExtras(bundle);
        startActivity(intent);
    });
}
~~~

**데이터를 받는 액티비티**
~~~java
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_sample4_1);
    Intent intent = getIntent();

    Person person = (Person) intent.getSerializableExtra("person");
    TextView textView = findViewById(R.id.serialize_result);

    textView.setText(person.toString());
}
~~~
<img width="400" height="500" src="https://user-images.githubusercontent.com/30828236/59558101-3f32e300-9025-11e9-894b-6353bca77c2f.png">

<img width="400" height="500" src="https://user-images.githubusercontent.com/30828236/59558100-3e9a4c80-9025-11e9-808d-35fe524c2b2c.png">

이런식으로 `Serializable`를 이용하면 쉽게 액티비티간에 객체를 주고받을 수 있다. 하지만 `Serializable`도 좋지만 `Parcelable`가 더 낫다는 글을 찾아볼 수 있었다.<br />`Paracelable` 또한 직렬화를 위한 다른 인터페이스이다. `Serializable`과는 다르게 표준 Java가 아닌 `Android SDK`의 인터페이스이다. `Serializable`과 다른점은 직렬화 처리 방법을 사용자가 명시적으로 작성하여 사용하기 때문에 자동으로 처리하기위한 `Reflection`이 필요 없다.<br />

### 안드로이드에서 Parcelable 사용해보기
**전달할 데이터 준비**
~~~java
public class Person_Parcel implements Parcelable {
    private String name;
    private int age;
    private String school;
    private int tall;
    private double weight;

    public Person_Parcel(String name, int age, String school, int tall, double weight) {
        this.name = name;
        this.age = age;
        this.school = school;
        this.tall = tall;
        this.weight = weight;
    }

    protected Person_Parcel(Parcel in) {
        name = in.readString();
        age = in.readInt();
        school = in.readString();
        tall = in.readInt();
        weight = in.readDouble();
    }

    public static final Creator<Person_Parcel> CREATOR = new Creator<Person_Parcel>() {
        @Override
        public Person_Parcel createFromParcel(Parcel in) {
            return new Person_Parcel(in);
        }

        @Override
        public Person_Parcel[] newArray(int size) {
            return new Person_Parcel[size];
        }
    };

    @Override
    public int describeContents() {
        return 0;
    }

    @Override
    public void writeToParcel(Parcel dest, int flags) {
        dest.writeString(name);
        dest.writeInt(age);
        dest.writeString(school);
        dest.writeInt(tall);
        dest.writeDouble(weight);
    }

    @Override
    public String toString() {
        return "Person_Parcel{" +
                "name='" + name + '\'' +
                ", age=" + age +
                ", school='" + school + '\'' +
                ", tall=" + tall +
                ", weight=" + weight +
                '}';
    }
}
~~~
`Parcelable`을 implements 부터 해준다. Parcelable은 필수적으로 정의 해야하는 메소드가 두개 있다.
- **describeContents()**<br />
Parcel의 내용을 기술해야 한다. FileDescriptor 같은 특별한 객체가 들어가면 이 부분을 통해서 알려줘야 한다. 보통은 0을 리턴한다.<br />
- **writeToParcel()**<br />
Parcel안에 데이터를 넣는 작업을 한다. 위의 코드처럼 Parcel.write***(data) 같이 사용된다.<br /><br />
이 두가지 메서드를 정의 해야하는 동시에 CREATOR라는 static field를 반드시 가지고 있어야 한다.<br /><br />
- **createFromParcel(Parcel source)**<br />
parcel된 데이터를 다시 원래대로 만들어 주는 작업을 한다.<br />
- **newArray(int size)**<br />
Parcel.createTypeArray()를 호출했을 때 불리며 new T[size]로 반환해준다.

**데이터 전달하기**
~~~java
ArrayList<Person_Parcel> personList;
@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_sample4);
    personList = new ArrayList<>();

    personList.add(new Person_Parcel("Person1", 27, "Hansung", 180, 100));
    personList.add(new Person_Parcel("Person2", 29, "Hansung", 170, 70));
    personList.add(new Person_Parcel("Person3", 22, "Hansung", 160, 50));

    Button button = findViewById(R.id.parcelable_send);
    button.setOnClickListener(v -> {
        Intent intent = new Intent(getApplicationContext(), Sample4_1Activity.class);
        intent.putParcelableArrayListExtra("persons", personList);
        startActivity(intent);
    });
}
~~~

**데이터 받기**
~~~java
ArrayList<Person_Parcel> personList;

@Override
protected void onCreate(Bundle savedInstanceState) {
    super.onCreate(savedInstanceState);
    setContentView(R.layout.activity_sample4_1);
    
    personList = new ArrayList<>();
    Intent intent = getIntent();
    personList = intent.getParcelableArrayListExtra("persons");

    Log.i("person1", "Person1 Info->" + personList.get(0).toString());
    Log.i("person2", "Person2 Info->" + personList.get(1).toString());
    Log.i("person3", "Person3 Info->" + personList.get(2).toString());
}
~~~
<img width="823" src="https://user-images.githubusercontent.com/30828236/59558362-8de37b80-902b-11e9-8abd-420677e62428.png">


### Parcelable vs Serializable

<img width="585" alt="스크린샷 2019-06-16 오전 11 42 11" src="https://user-images.githubusercontent.com/30828236/59558381-d3a04400-902b-11e9-8887-d7b40d4cc0ff.png">

출처 : (https://medium.com/udacity-android-nanodegree/android-parcelable-vs-serializable-f2d7d2f4a2a)









