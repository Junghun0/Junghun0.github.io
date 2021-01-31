---
layout: post
title: "[Android] Room Library Study"
subtitle: 'How to use Room Library'
author: "JungHoon-Park"
header-style: text
header-mask: 0.3
comments: true
catalog:  true
tags:
  - Mobile
  - Jetpack
  - Room
  - Study
---

Room Library 동작방식 및 사용해보기

---

## 의존성 추가

```xml
apply plugin: 'kotlin-kapt'
...
dependencies {
  def room_version = "2.2.5"

  implementation "androidx.room:room-runtime:$room_version"
  kapt "androidx.room:room-compiler:$room_version"

  // optional - Kotlin Extensions and Coroutines support for Room
  implementation "androidx.room:room-ktx:$room_version"

  // optional - Test helpers
  testImplementation "androidx.room:room-testing:$room_version"
}
```

### -kapt 란?
"자바 6부터 도입된 Pluggable Annotation Processing 를 Kotlin에서도 사용 가능하게 하는 것" 이라고 공식 문서에 나온다.<br/>
여기서 어노테이션 프로세싱이란 우리가 간단하게 '@명령어' 처럼 사용하는 주석 형태의 문자열을 실제 코드로 생성해주는 것이다.<br/>
@으로 시작하는 명령어를 어노테이션이라고 하는데, 어노테이션이 컴파일 시에 코드로 생성되기 때문에 실행 시에 발생할 수 있는 성능 문제가 많이 개선된다.<br/>

**Room 의 3가지 주요 구성요소**
Room에는 다음과 같은 세 가지 주요 구성요소가 있습니다.

- **데이터베이스** : 데이터베이스 홀더를 포함하며 앱의 지속적인 관계형 데이터의 기본 연결을 위한 기본 액세스 포인트 역할을 합니다.<br/>
@Database로 주석이 지정된 클래스는 다음 조건을 충족해야 합니다.<br/>
RoomDatabase를 확장하는 추상 클래스여야 합니다.<br/>
주석 내에 데이터베이스와 연결된 항목의 목록을 포함해야 합니다.<br/>
인수가 0개이며 @Dao로 주석이 지정된 클래스를 반환하는 추상 메서드를 포함해야 합니다.<br/>
런타임 시 Room.databaseBuilder() 또는 Room.inMemoryDatabaseBuilder()를 호출하여 Database 인스턴스를 가져올 수 있습니다.<br/>
- **Entity** : 데이터베이스 내의 테이블을 나타냅니다.<br/>
- **DAO** : 데이터베이스에 액세스하는 데 사용되는 메서드가 포함되어 있습니다.

## Room 을 사용해 내부 데이터베이스에 저장
- Entity<br/>
Entity는 데이터베이스에서 테이블이라고 생각하면 됩니다.
테이블명, 칼럼 명, 칼럼 타입, 기본키, 외래키 등을 정의할 수 있는 annotation들이 있다.<br/>
- DAO<br/>
DAO(Data Access Object)는 데이터베이스에 접근해 CRUD 등을 사용할 수 있게 하는 다양한 annotation들이 있다.<br/>
- Database<br/>
RoomDatabase 클래스를 상속받는 abstract class 이다.
@Database annotation안에 해당 database와 관련된 Entity 리스트를 포함해야 한다.<br/>

- @Ignore
@Ignore 어노테이션을 사용하면 해당 변수가 테입즐과 관계없는 변수라는 정보를 알릴 수 있다.


## Todo 앱 예제

1. **AppDatabase Class**
마치 SQLiteOpenHelper를 상속받아서 구현했던 것처럼 Room도 유사한 구조로 사용할 수 있다. Room은 RoomDatabase를 제공하는데 RoomDatabase를 상속받아 클래스를 생성하면 된다.
주의할 점은 추상 클래스로 생성해야 한다는 점이다. 기존 클래스와 동일하게 생성하고 class앞에 abstract 키워드를 붙이면 된다.<br/>

* @Database 속성정리
- entities 
Room 라이브러리가 사용할 엔터티(테이블) 클래스 목록<br/>
- version
데이터베이스의 버전<br/>
- exportSchema
true면 스키마 정보를 파일로 출력한다.<br/>

```java
@Database(entities = [Todo::class], version = 1)
abstract class AppDatabase : RoomDatabase() {
    abstract fun todoDao(): TodoDao
}
```

2. **Todo Class**<br/>
Entity Annotation을 사용해 Room에서 사용할 데이터 클래스를 선언해준다.<br/>
tablename 옵션을 통해 데이터 베이스 이름을 정해줄 수 있다.<br/>
그 외는 클래스명으로 설정된다.
```java
@Entity
data class Todo(
    var title: String
){
    //DB 의 AI
    @PrimaryKey(autoGenerate = true)
    var id: Int = 0
}
```

3. **TodoDao Class**
```java
@Dao
interface TodoDao {
    @Query("SELECT * FROM Todo")
    fun getAll(): LiveData<List<Todo>>

    @Insert
    suspend fun insert(todo: Todo)

    @Update
    suspend fun update(todo: Todo)

    @Delete
    suspend fun delete(todo: Todo)
}
```

4. **이후에 ViewModel 클래스에서 데이터베이스 인스턴스 가져오기**
```java
class MainViewModel(application: Application) : AndroidViewModel(application) {
    private val db = Room.databaseBuilder(
        application,
        AppDatabase::class.java, "todo-db"
    ).build()

    var todos: LiveData<List<Todo>>
    var newTodo: String? = null

    init {
        todos = getAll()
    }
    fun getAll(): LiveData<List<Todo>> {
        return db.todoDao().getAll()
    }

    fun insert(todo: String) {
        viewModelScope.launch(Dispatchers.IO) {
            db.todoDao().insert(Todo(todo))
        }
    }
```
<br/>
### [소스코드링크(github)](https://github.com/Junghun0/Jetpack_Samples)
