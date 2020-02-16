---
layout: post
title: "[Flutter] Flutter 개발환경 구성하기"
subtitle: ' About Flutter Setting '
author: "JungHoon-Park"
header-style: text
header-mask: 0.3
comments: true
catalog:  true
tags:
  - Flutter
  - Mobile
  - Study
---

# Flutter 란 ?
Flutter 는 Google에서 개발한 크로스 플랫폼 모바일 앱 개발 프레임워크 이다. 크로스 플랫폼 기술은 하나의 코드를 여러 플랫폼에서 사용할 수 있게 해주는 기술을 말한다. 모바일에서는 iOS - Android 환경 모두에서 사용할 수 있게 해주는 것 이다. 이렇게 되었을 때 개발할 때와 유지 보수할 때 플랫폼 별로 대응할 필요가 없게 되어 비용이 크게 절감된다.

## 개발환경 세팅하기 (mac)
- **https://flutter.dev** 로 이동하여 Flutter SDK 를 다운받는다.<br/>
![스크린샷 2020-02-16 오후 5 22 33](https://user-images.githubusercontent.com/30828236/74601372-fd40cc00-50e0-11ea-8ddd-b856baf1c18d.png)

- 설치가 완료되면 해당 SDK 를 원하는 디렉토리에 옮겨준다.
- 터미널을 통해 환경변수를 잡아준다. (mac OS 버전 Catalina 기준)
터미널에서 .zshrc 파일에 flutter - bin 의 디렉토리 경로를 잡아준다.<br/>
export PATH="$PATH:/Users/*******/Developer/flutter/bin" <br/>
Mojave 버전이라면 .bashrc 파일에 추가해주면 된다.
- 안드로이드 스튜디오에서 Plugin -> Flutter 를 설치하고 안드로이드 스튜디오를 재시작 해준다.
- 재시작 하고 나면 ![스크린샷 2020-02-16 오후 5 28 04](https://user-images.githubusercontent.com/30828236/74601429-b6070b00-50e1-11ea-97ed-333115381b42.png)

Start a new Flutter Project 탭이 생성된 것을 확인할 수 있다.<br/>
* 만약 설치를 다했고 안드로이드 스튜디오를 재시작 해도 해당 탭이 보이지 않는다면 Plugin 에서 Android APK support 와 Android NDK Support 이 두개가 모두 설치되어 있고 활성화가 되어있는지 확인해보고 안되있다면 설치 후 활성화 해주면 해당 탭을 확인할 수 있다.
