---
layout: post
title: "[iOS] SideBar 만들기"
subtitle: 'Make SideBar iOS Sample'
author: "JungHoon-Park"
header-style: text
header-mask: 0.3
comments: true
catalog:  true
tags:
  - Swift4
  - Mobile
  - iOS
  - Study
---

Sample : iOS SideBar SWRevelViewController

---

# SideBar_ios

### SWRevelViewController 라이브러리 사용
#### - https://github.com/John-Lluch/SWRevealViewController
#### - SWRevealViewController.h / SWRevealViewController.m 추가
<div>
<img width="600" height="200" src="https://user-images.githubusercontent.com/30828236/58068227-7b605880-7bcb-11e9-8053-56b1f192609c.png"/>
</div>

#### - 브릿징 헤더란 ?
#### --> Objective-c 라이브러리를 사용하기 위해서 브릿징 헤더 라는 파일이 필요 !(Swift에게 Objective-c 클래스의 존재를 알려준다)

**- SampleSideBar_ios-Bridging-Header.h 파일에 import 해줌**

<div>
<img width ="600" height="130" src="https://user-images.githubusercontent.com/30828236/54476914-075da900-4846-11e9-8336-6cdb1ea38d95.png"/>
</div>

**- FrontViewController.swift**

```swift
class FrontViewController : UIViewController{
    @IBOutlet var sideBarButton: UIBarButtonItem!
    override func viewDidLoad() {
        super.viewDidLoad()
        
    //메인 컨트롤러의 참조 정보를 가져온다.
    if let revealVC = self.revealViewController(){
        //버튼이 클릭될 때 메인 컨트롤러에 정의된 revealToggle(_:)을 호출하도록 정의한다
        self.sideBarButton.target = revealVC
        self.sideBarButton.action = #selector(revealVC.revealToggle(_:))
        //제스처를 뷰에 추가한다!
        self.view.addGestureRecognizer(revealVC.panGestureRecognizer())
        }
    }
}
```
- - -

### 스토리보드
<div>
  <img width ="600" height ="500" src="https://user-images.githubusercontent.com/30828236/54477207-fd897500-4848-11e9-90ca-9e0b69ef160c.png"/>
</div>

#### * 메인 뷰컨트롤러와 테이블 뷰 -> reveal view controller set controller 속성의 segue 로 연결 / segue Identifier(sw_rear)

#### * 첫 화면과 연결되는 navigationviewcontroller -> reveal view controller set controller 속성 segue / segue Identifier(sw_front)

##### 사이드 바 커스텀 
<div>
  <img width="300" height="450" src="https://user-images.githubusercontent.com/30828236/54486855-6ae0e880-48d1-11e9-8977-53dd0dcdce89.png"/>
</div>




