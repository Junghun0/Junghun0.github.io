---
layout: post
title: "[iOS] UserDefaults 사용해보기"
subtitle: 'Use UserDefaults iOS Sample'
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

Sample : iOS UserDefaults

---

# UserDefaults_ios

## UserDefaults 란?

#### - iOS 앱 실행 과정에서 저장해야 할 데이터가 비교적 단순한 구조라면 기본 저장소에 저장하기 위해 제공하는 객체

#### - UserDefaults는 *런타임 환경에서 동작하는 객체* 이다. 앱이 실행되는 동안 기본저장소에 접근하여 데이터를 가져오고 기록하는 역할

#### - UserDefaults는 *싱글톤 패턴* 으로 설계되어 있어 *앱 전체에서 딱 하나의 인스턴스만 생성* 됨-> 앱 전체가 공유해서 사용하는 형태

#### - UserDefaults 객체를 호출하면 각각의 호출에 응답하기 위한 인스턴스가 생성되는 것이 아니라 하나의 UserDefaults 객체가 모든 요청을 받아 처리

#### - 사용시 직접 초기화 메소드를 호출하여 인스턴스를 생성하지 않고, 이미 생성되어 있는 인스턴스를 참조해서 사용해야 함 -> UserDefaults 객체의 메소드나 프로퍼티 대부분은 클래스 메소드나 클래스 프로퍼티로 정의되어 있다.

- - - 

## 소스코드

### - 데이터 저장 할때
```swift
let plist = UserDefaults.standard
//저장된 값을 꺼내어 각 컨트롤에 설정한다.
self.name.text = plist.string(forKey: "name")
self.married.isOn = plist.bool(forKey: "married")
self.gender.selectedSegmentIndex = plist.integer(forKey: "gender")
```
### - 데이터 불러 올때
```swift
@IBAction func changeGender(_ sender: UISegmentedControl) {
    let value = sender.selectedSegmentIndex // 0남자 1여자
        
    let plist = UserDefaults.standard// 기본 저장소 객체를 가져옴
    plist.set(value, forKey: "gender")//"gender" 키값으로 저장
    plist.synchronize()//동기화처리
}
```

# Property List 

#### - Property List 는 객체 직렬화를 위한 XML형식의 파일 / .plist 라는 확장자를 가짐
 
#### - 단순한 데이터를 XML 포맷에 맞추어 key - value 형식으로 저장하는 것
 
#### - Property List 는 데이터 타입을 '추상화' 하여 저장한다.
 
#### - 저장할 수 있는 데이터타입 -> 원시타입 (String, Int, Float, Double, Bool 등) , 레퍼런스 타입(Foundation 프레임워크에서 제공하는 것들)
 
## 소스코드

```swift
//1. data.plist 파일을 읽어온다.
let paths = NSSearchPathForDirectoriesInDomains(.documentDirectory, .userDomainMask, true)
    
let path = paths[0] as NSString
let plist = path.strings(byAppendingPaths: ["data.plist"])[0]
let data = NSMutableDictionary(contentsOfFile: plist)
    
//2. 저장된 데이터를 읽어온다.
let name = data?.value(forKey: "이름") as? String
let age = data?.value(forKey: "나이") as? Int
    
//2_1 읽어온 데이터를 출력한다.
if let _name = name{
    print(_name)
}
    
if let _age = age{
    print(_age)
}
    
//값을 입력 또는 수정한다.
data?.setValue("값 수정!!",forKey: "이름")
data?.setValue(27,forKey: "나이")
data?.setValue("남",forKey: "성별")
    
//딕셔너리 객체를 파일에 저장한다.
data?.write(toFile: plist, atomically: true)
```
### [소스코드링크(github)](https://github.com/Junghun0/UserDefaults_ios.git)