# Any
### 모든 타입을 다룰 수 있다.
### 기본 타입, 인스턴스, 함수, 옵셔널 등등

<br/>

### 단, 저장된 타입의 메모리 구조를 알 수 없기 때문에, 항상 타입캐스팅하여 사용해야 하는 단점이 있다.
```swift
var any: Any = "Swift"
print(any)               // Swift

(any as! String).count   // 5
```

<br/>

```swift
// Any타입의 장점: 모든 타입을 담을 수 있는 배열을 생성 가능
// 기본타입, 클래스나 구조체의 인스턴스, 클로저 등등
let array: [Any] = [5, "안녕", 3.5, Person(), Superman(), {(name: String) in return name}]

for value in array {
    guard let str = value as? String else {
        continue
    }
    print(str)   // "안녕"
}
```

<br/>

---

<br/>

# AnyObject
### 클래스의 인스턴스만 저장할 수 있는 타입 
### AnyObject도 사용하기 위해서는 타입캐스팅 과정이 필요하다.

<br/>

```swift
let objArray: [AnyObject] = [Person(), Superman(), NSString()]
```

<br/>

### 실제로 AnyObject는 모든 클래스 타입이 암시적으로 준수하는 **프로토콜**이다.
그래서 범용적인 타입으로써(클래스의 인스턴스) 사용이 가능했던 것 <br/>
AnyObject 프로토콜을 상속하여 클래스에서만 사용될 수 있는 프로토콜을 만들 수 있다.
```swift
protocol SomeProtocol: AnyObject {
    func doSomething()
}
```

<br/>
<br/>

---

<br/>
<br/>

## switch문을 사용하여 분기처리

<br/>

```swift
// 타입캐스팅 + 분기처리
// 1) is 연산자를 사용하여 타입의 형태를 직접 체크
// 2) as 연산자를 사용하여 바인딩하고, 사용
for (index, item) in array.enumerated() {
    
    switch item {
    case is Int:                                  // = item is Int
        print("Index - \(index): 정수입니다.")
    case let num as Double:                       // = let num = item as? Double
        print("Index - \(index): 소수 \(num)입니다.")
    case is String:                               // item is String
        print("Index - \(index): 문자열입니다.")
    case let person as Person:                    // let person = item as? Person
        print("Index - \(index): 사람입니다.")
        print("이름은 \(person.name)입니다.")
        print("나이는 \(person.age)입니다.")
    case is (String) -> String:                   // item is (String) -> String
        print("Index - \(index): 클로저 타입입니다.")
    default:
        print("Index - \(index): 그 이외의 타입입니다.")
    }
}
```

