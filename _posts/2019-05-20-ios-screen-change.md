---
layout: post
title: "[iOS] 간단한 화면전환 방식"
subtitle: 'Using screen change'
author: "JungHoon-Park"
header-img: "img/post-bg-halting.jpg"
header-mask: 0.3
comments: true
catalog:  true
tags:
  - Swift4
  - Mobile
  - iOS
  - Study
---

Sample : iOS Screen Change

---

### 1. **뷰 컨트롤러에서 다른 뷰 컨트롤러를 호출하여 화면 전환하기(present)**

### 2. **내비게이션 컨트롤러를 사용하여 화면 전환하기**

### 3. **화면 전환용 객체 세그웨이(Segueway)를 사용하여 화면 전환하기**

* 뷰 컨트롤러의 뷰 위에 다른 뷰를 가져와 바꿔치기 하기 ( 하나의 뷰 컨트롤러가 두 개 이상의 루트 뷰를 관리해야 하므로 좋은방법은 아님,
  iOS 에서는 하나의 뷰 컨트롤러 아래에 하나의 루트 뷰를 관리하는 MVC 패턴을 기본으로 함)
  
---

#### 소스코드

**1_1. 화면이동**
```swift
@IBAction func presentSecondView(_ sender: Any) {
    guard let secondViewInstance = self.storyboard?.instantiateViewController(withIdentifier: "SecondViewController") else{
        return
    }
    secondViewInstance.modalTransitionStyle = UIModalTransitionStyle.flipHorizontal
    self.present(secondViewInstance, animated: true, completion: nil)
}
```
**1_2. 뒤로이동**
```swift
@IBAction func presentBackPressed(_ sender: Any) {
    self.presentingViewController?.dismiss(animated: true, completion: nil)
}
```

**2_1. 화면이동**
```swift
@IBAction func naviGoSecond(_ sender: Any) {
    guard let naviSecondview = self.storyboard?.instantiateViewController(withIdentifier: "NavigationSecondView") else{
        return
    }
    self.navigationController?.pushViewController(naviSecondview, animated: true)
}
```
**2_2. 뒤로이동**
```swift
@IBAction func popNavigationVIew(_ sender: Any) {
    self.navigationController?.popViewController(animated: true)
}
```
**3_1. 화면이동**
 * Storyboard 에서 다른 ViewController 와 연결 -> Action Segue
 * 출발점이 뷰 컨트롤러 자체인 경우 -> Manual Segue
 ```swift
@IBAction func manualSegueBtn(_ sender: Any) {
    self.performSegue(withIdentifier: "ManualSegue", sender: self)
}
 ```
 * UIStoryboardSegue 클래스를 서브클래싱항 새로운 가능을 갖춘 세그웨이 객체정의 -> CustomSegue
 Storyboard 에서 segue 클릭 후 StoryboardSegue 탭에 class 를 새로 정의한 클래스로 설정

    - custom segue 정의

```swift
import UIKit

class NewSegue: UIStoryboardSegue{
    
override func perform() {
    //세그웨이의 출발지 뷰 컨트롤러
    let srcUVC = self.source
        
    //세그웨이의 목적지 뷰 컨트롤러
    let destUVC = self.destination
        
    //fromView 에서 toView 로 뷰를 전환한다
    UIView.transition(from: srcUVC.view,
                    to: destUVC.view,
                    duration: 2,
                    options: .transitionCurlDown,
                    completion: nil)
    }
}
```
**3_2. 뒤로이동**

 *Storyboard 에서 상단의 Exit 아이콘과 연결(Action Segue, Manual Segue, Custom Segue)*
```swift
@IBAction func goBackSegue(_ sender: UIStoryboardSegue){}
```

**3_3. Segue 전처리 메소드**

*Segue 실행하기 전에 값을 저장해둘 필요가 있거나, 경고창을 띄워주는 등의 처리를 해야 할 경우 전처리 메소드 사용 - Segue 실행되기 전에 자동으로 실행됨*
```swift
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    if(segue.identifier == "ActionSegue"){
        NSLog("action segue 실행!")
    }else if(segue.identifier == "ManualSegue"){
        NSLog("Manual segue 실행!")
    }else if(segue.identifier == "CustomSegue"){
        NSLog("Custom segue 실행!")
    }else{
        NSLog("알수없는 segue 입니다.")
    }
}
```

### [소스코드링크(github)](https://github.com/Junghun0/SampleScreenChange_ios.git)
