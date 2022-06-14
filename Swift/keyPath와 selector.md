# keyPath & selector

## keyPath
#### 프로퍼티에 간접적으로 접근하기 위한 키경로(문자열)
#### Objective-C부터 사용되던 방식
```swift
let dong = Person(name: "dong")

// 기존의 프로퍼티 접근 방법 (직접적인 방법)
dong.name

// 아래와 같은 방법으로도 접근하고 싶은 상황 (간접적으로)
"dong.name"
dong[keyPath: "name"]
```

<br/>

### 필요한 경우 :
중첩타입 내부에서 정의된 특정 프로퍼티에 접근하는 경우 (경로를 다 써주면 코드가 길어질 수 있음)
```swift
let name = school1.affiliate.classMember.name
```

<br/>

### keyPath 사용 (Swift 5 ~)
1. 미리 경로를 지정한다. (keyPath) : 백슬래시(\)를 사용
2. 해당 경로를 사용하여 접근
```swift
// 경로 지정 (백슬래시 + 타입이름부터 시작)
let namePath = \School.affiliate.classMember.name

// 서브스크립트 문법으로 접근
school1[keyPath: namePath]
```

<br/>

### 경로를 추가하는 방법
```swift
let namePath = \School.affiliate.classMember

// appending() : 경로를 추가해서 새로운 경로를 반환
let newNamePath = namePath.appending(path: \.name)
```

<br/>
<br/>

---

<br/>
<br/>

## selector
#### selector는 구조체로, 메서드의 실행 주소를 저장만 가능 (실행할 수는 없다.)
#### Objective-C부터 사용되던 방식 (@objc 어트리뷰트 키워드가 필요)
#### 클로저와 차이점 : 클로저는 메서드의 주소를 저장하고, 언제든 접근하여 실행시킬 수 있다.
#### 연산 프로퍼티(실질 메서드)와 메서드를 가르키는 방식이 다름
```swift
// 연산 프로퍼티를 가르킬때
let eyesSelector = #selector(getter: Dog.doubleNum)    // 계산(읽기) 속성
let nameSelector = #selector(setter: Dog.doubleNum)    // 계산(쓰기) 속성


// 메서드를 가르킬때
let runSelector = #selector(Dog.run)
```

<br/>
<br/>

### 실제 프로젝트에서의 사용 (addTarget)
 addTarget : 버튼이 눌렸을 때 어떤 인스턴스의 메서드를 실행시킬지 연결하는 함수
```swift
class ViewController: UIViewController {
    
    lazy var myButton: UIButton = UIButton()

    override func viewDidLoad() {
        super.viewDidLoad()
        
        view.addSubview(myButton)

        // ⭐️ 버튼에 대응되는 동작 연결
        myButton.addTarget(self, action: #selector(doSomething(_:)), for: .touchUpInside)

        
    }
    
    // selector는 Objective-C에서 인식할 수 있는 속성/메서드만 가르킬 수 있음
    @objc func doSomething(_ sender: Any) {

    }
}
```