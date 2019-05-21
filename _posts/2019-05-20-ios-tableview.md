---
layout: post
title: "[iOS] 테이블뷰 만들기"
subtitle: 'Make TableView iOS Sample'
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

Sample : iOS TableView

---

# SampleTableView_ios

##  1. subtitle 스타일 셀 TableView
##  2. custom 타입 TableView
##  3. alert 을 이용해 글입력 후 TableView 에 추가하기

- - -
### 소스코드

#### 1. subtitle 스타일 셀 TableView 

#### 1_1. UITableViewController 를 상속받는 swift 파일 생성 및 연결
  
#### 1_2. 데이터소스 만들기
```swift
//데이터소스 만들기 vo -> value object
class MovieVO{
    var thumbnail: String?
    var title: String?
    var description: String?
    var detail: String?
    var opendate: String?
    var rating: Double?
}
```
#### 1_3. 데이터소스와 테이블 뷰 바인딩 
```swift
class ListViewController: UITableViewController{
    //튜플 아이템으로 구성된 데이터 세트
    var dataset = [
        ("다크 나이트","영웅물에 철학에 음악까지 더해져 예술이 되다.","2008-09-04", 8.95 ),
        ("호우시절","때를 알고 내리는 좋은 비","2009-10-08",7.31),
        ("말할 수 없는 비밀","여기서 너까지 다섯 걸음","2015-05-07",9.19)
    ]
    
    lazy var list: [MovieVO] = {
        var datalist = [MovieVO]()
        for(title , desc , opendate , rating) in self.dataset{
            let mvo = MovieVO()
            mvo.title = title
            mvo.description = desc
            mvo.opendate = opendate
            mvo.rating = rating
            
            datalist.append(mvo)
        }
        return datalist
    }()
    
    //테이블 뷰의 행의 개수를 리턴
    override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return self.list.count
    }
    
    override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        //주어진 행에 맞는 데이터 소스를 읽어온다.
        let row = self.list[indexPath.row]
        //테이블 셀 객체를 직접 생성하는 대신 큐로부터 가져옴
        let cell = tableView.dequeueReusableCell(withIdentifier: "ListCell")!
        cell.textLabel?.text = row.title
        cell.detailTextLabel?.text = row.description
        return cell
    }
    
    override func tableView(_ tableView: UITableView, didSelectRowAt indexPath: IndexPath) {
        NSLog("선택된 행은 \(indexPath.row)")
    }
}
```

### 2. custom 타입 TableView 만들기
#### 2_1. 각 cell 의 label에 Tag값을 설정해 cell의 객체 참조하기
```swift
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    //주어진 행에 맞는 데이터 소스를 읽어온다.
    let row = self.list[indexPath.row]
    //테이블 셀 객체를 직접 생성하는 대신 큐로부터 가져옴
    let cell = tableView.dequeueReusableCell(withIdentifier: "ListCell")!
        
    let title = cell.viewWithTag(101) as? UILabel
    let desc = cell.viewWithTag(102) as? UILabel
    let opendate = cell.viewWithTag(103) as? UILabel
    let rating = cell.viewWithTag(104) as? UILabel
        
    title?.text = row.title
    desc?.text = row.description
    opendate?.text = row.opendate
    rating?.text = "\(row.rating!)"

    return cell
}
```
  
#### 2_2. 커스텀 클래스로 프로토타입 셀의 객체 제어하기
  
##### 1) 원하는 객체를 프로토타입 셀에 추가한다.
      
##### 2) MovieCell 클래스에 아울렛 변수를 등록한다.
      
##### 3) ListViewController 클래스에서 아울렛 변수를 제어한다.

```swift
class MovieCell: UITableViewCell{
    @IBOutlet weak var rating: UILabel!
    @IBOutlet weak var opendate: UILabel!
    @IBOutlet weak var desc: UILabel!
    @IBOutlet weak var title: UILabel!
}
```
  
```swift
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    //주어진 행에 맞는 데이터 소스를 읽어온다.
    let row = self.list[indexPath.row]
    //테이블 셀 객체를 직접 생성하는 대신 큐로부터 가져옴
    let cell = tableView.dequeueReusableCell(withIdentifier: "ListCell") as! MovieCell
        
    //데이터 소스에 저장된 값을 각 아울렛 변수에 할당
    cell.title?.text = row.title
    cell.desc?.text = row.description
    cell.opendate?.text = row.opendate
    cell.rating?.text = "\(row.rating!)"
        
    return cell
}
```
  
#### 3. alert 을 이용해 글입력 후 TableView 에 추가하기
```swift
class ListViewController2: UITableViewController{
//테이블 뷰에 연결될 데이터를 저장하는 배열
var list = [String]()
    
override func viewDidLoad() {
    super.viewDidLoad()
}
    
override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
    return self.list.count
}
override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
    // 1. cell 아이디를 가진 셀을 읽어온다. 없으면 UITableViewCell 인스턴스를 생성한다.
    let cell = tableView.dequeueReusableCell(withIdentifier: "cell") ?? UITableViewCell()
        
    //셀의 기본 텍스트 레이블 행 수 제한을 없앤다.
    cell.textLabel?.numberOfLines = 0 
        
    //셀의 기본 텍스트 레이블에 배열 변수의 값을 할당한다.
    cell.textLabel?.text = list[indexPath.row]
    return cell
}
override func tableView(_ tableView: UITableView, heightForRowAt indexPath: IndexPath) -> CGFloat {
    let row = self.list[indexPath.row]
        
    //높이를 조절해준다. 기본 높이 60 + 글의 길이가 30자를 넘어갈 때마다 20만큼씩 높이를 늘려준다.
    let height = CGFloat(60 + (row.count / 30) * 20)
    return height
}
    
@IBAction func add(_ sender: Any) {
    //1.알림창 객체의 인스턴스를 생성
    let alert = UIAlertController(title: "목록 입력", message: "추가할 글을 작성해주세요", preferredStyle: .alert)
    //2.알림창에 입력폼을 추가한다.
    alert.addTextField(){ (tf) in
        tf.placeholder = "내용을 입력하세요."
    }
    //3.OK 버튼 객체를 생성 : 아직 알림창 객체에 버튼이 등록되지않은 상태
    let ok  = UIAlertAction(title: "OK", style: .default){ (_) in
        //4.알림창의 0번째 입력필드에 값이 있다면
        if let title = alert.textFields?[0].text{
            //5.배열에 입력된 값을 추가하고 테이블을 갱신
            self.list.append(title)
            self.tableView.reloadData()
        }
    }
    //취소 버튼 객체를 생성 : 아직 알림창 객체에 버튼이 등록되지 않은 상태
    let cancel = UIAlertAction(title: "취소", style: .default, handler: nil)
        
    //6.알림창 객체에 버튼 객체를 등록한다.
    alert.addAction(ok)
    alert.addAction(cancel)
        
    //7.알림창을 띄운다.
    self.present(alert, animated: false)
    } 
}
```

### 셀프 사이징 셀

#### - estimatedRowHeight 프로퍼티
*개발자가 예상하는 RowHeight 이다. 테이블 뷰의 데이터 Reload 될때 각 행마다 대략적인 높이를 계산하는데 도움을 주는 역할을 수행한다.*

#### - UITableViewAutomaticDimension 객체
  
```swift
override func viewWillAppear(_ animated: Bool) {
    self.tableView.estimatedRowHeight = 50 //대충의 높이값
    self.tableView.rowHeight = UITableViewAutomaticDimension
    //개별 행마다 제어해주지 않아도 텍스트 길이에 따라 알아서 셀의 높이가 변한다.
}
```

### [소스코드링크(github)](https://github.com/Junghun0/SampleTableView_ios.git)
    
    
