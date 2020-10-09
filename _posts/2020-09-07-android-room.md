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

## Room 을 사용해 내부 데이터베이스에 저장
