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

**Room 의 3가지 주요 구성요소**
Room에는 다음과 같은 세 가지 주요 구성요소가 있습니다.

-**데이터베이스** : 데이터베이스 홀더를 포함하며 앱의 지속적인 관계형 데이터의 기본 연결을 위한 기본 액세스 포인트 역할을 합니다.

@Database로 주석이 지정된 클래스는 다음 조건을 충족해야 합니다.

RoomDatabase를 확장하는 추상 클래스여야 합니다.
주석 내에 데이터베이스와 연결된 항목의 목록을 포함해야 합니다.
인수가 0개이며 @Dao로 주석이 지정된 클래스를 반환하는 추상 메서드를 포함해야 합니다.
런타임 시 Room.databaseBuilder() 또는 Room.inMemoryDatabaseBuilder()를 호출하여 Database 인스턴스를 가져올 수 있습니다.

-**항목** : 데이터베이스 내의 테이블을 나타냅니다.
-**DAO** : 데이터베이스에 액세스하는 데 사용되는 메서드가 포함되어 있습니다.

## Room 을 사용해 내부 데이터베이스에 저장
