---
layout: post
title: "iOS 화면간 데이터 전달 방식"
subtitle: 'Data Deliver iOS Sample'
author: "JungHoon-Park"
header-style: text
header-mask: 0.3
comments: true
catalog:  true
tags:
  - Swift4
  - Mobile
  - iOS
---

Sample : iOS Data Deliver

---

# SampleDataDeliver_ios  
  
## 1. 뷰 컨트롤러에 직접값을 전달하기

   - 전달할 값을 준비
  
   - 값을 전달받을 프로퍼티 정의
  
   - 값을 전달받을 viewcontroller의 인스턴스를 직접 생성하거나, 이미 생성되어 있는 인스턴스의 참조를 읽어옴. 
  
   - 정의한 프로퍼티에 값을 대입 후 화면 전환. 

## 2. 이전 화면으로 값을 전달하기

 - 전달받는 viewcontroller의 인스턴스를 생성하는 것이 아니라 현재 존재하는 viewcontroller의 인스턴스의 참조값을 얻어온다.
  
 - 인스턴스가 초기화 될때 마다 호출되는 viewDidLoad() 메소드를 사용하지않고, 화면이 새롭게 그려질 때마다 호출되는 viewWillAppear() 메소드사용
    
#### 소스코드

**1_1. 전달할 값을 준비**

```swift
@IBOutlet weak var interval: UIStepper!
@IBOutlet weak var email: UITextField!
@IBOutlet weak var isUpdate: UISwitch!
@IBOutlet weak var intervalText: UILabel!
@IBOutlet weak var isUpdateText: UILabel!
override func viewDidLoad() {
    super.viewDidLoad()
}
@IBAction func onSwitch(_ sender: UISwitch) {
    if(sender.isOn == true){
        self.isUpdateText.text = "갱신함"
    }else{
        self.isUpdateText.text = "갱신하지 않음"
    }
}
    
@IBAction func onStepper(_ sender: UIStepper) {
    let value = Int(sender.value)
    self.intervalText.text = "\(value)분 마다"
}
```
**1_2. 값을 전달받을 프로퍼티 정의**
```swift
class SecondViewController : UIViewController{
    
@IBOutlet weak var resultInterval: UILabel!
@IBOutlet weak var resultUpdate: UILabel!
@IBOutlet weak var resultEmail: UILabel!
    
var paramInterval: Double = 0
var paramUpdate: Bool = false
var paramEmail: String = ""
    
override func viewDidLoad() {
    super.viewDidLoad()
    self.resultEmail.text = paramEmail
    self.resultUpdate.text = (self.paramUpdate == true ? "자동갱신" : "자동갱신안함")
    self.resultInterval.text = "\(Int(paramInterval))분 마다 갱신"
}
```

**1_3. 값을 전달받을 viewcontroller의 인스턴스를 직접 생성하거나, 이미 생성되어 있는 인스턴스의 참조를 읽어옴**
```swift
@IBAction func onSubmit(_ sender: Any) {
    guard let resultView = self.storyboard?.instantiateViewController(withIdentifier: "SecondViewController") as? SecondViewController else{
            return
    }
}
```
**1_4. 정의한 프로퍼티에 값을 대입 후 화면 전환**
```swift
@IBAction func onSubmit(_ sender: Any) {
    guard let resultView = self.storyboard?.instantiateViewController(withIdentifier: "SecondViewController") as? SecondViewController else{
        return
    }
    resultView.paramEmail = self.email.text!
    resultView.paramUpdate = self.isUpdate.isOn
    resultView.paramInterval = self.interval.value
        
    self.present(resultView,animated: true)
}
```
**1_5. segue 로 화면 전환할때**
```swift
//manual segue
@IBAction func onPerformSegue(_ sender: Any) {
    self.performSegue(withIdentifier: "ManualSubmit", sender: self)
}
    
override func prepare(for segue: UIStoryboardSegue, sender: Any?) {
    let destinationview = segue.destination
        
    guard let resultViewController = destinationview as? NaviResultViewController else{
        return
    }
        
    resultViewController.paramEmail = self.emailText.text!
    resultViewController.paramUpdate = self.switchbtn.isOn
    resultViewController.paramInterval = self.interval.value
}
```

**2_1. 전달받는 viewcontroller의 인스턴스를 생성하는 것이 아니라 현재 존재하는 viewcontroller의 인스턴스의 참조값을 얻어온다.**
```swift
@IBAction func secondSubmitBtn(_ sender: Any) {
    let valuebackfirst = self.presentingViewController
    guard let previewcontroller = valuebackfirst as? ValuebackFirst else{
        return
    }
        
    previewcontroller.paramEmail = self.inputEmail.text
    previewcontroller.paramInterval = self.secondStepper.value
    previewcontroller.paramUpdate = self.secondSwitch.isOn
        
    self.presentingViewController?.dismiss(animated: true, completion: nil)
}
```

**2_2. 인스턴스가 초기화 될때 마다 호출되는 viewDidLoad() 메소드를 사용하지않고, 화면이 새롭게 그려질 때마다 호출되는 viewWillAppear() 메소드사용**
```swift
override func viewWillAppear(_ animated: Bool) {
    if let email = paramEmail{
        resultEmail.text = email
    }
    if let update = paramUpdate{
        resultUpdate.text = update==true ? "자동갱신" : "자동갱신안함"
    }
    if let interval = paramInterval{
        resultInterval.text = "\(Int(interval))분마다 갱신"
    }
}
```

### [소스코드링크(github)](https://github.com/Junghun0/SampleDataDeliver_ios.git)

    
