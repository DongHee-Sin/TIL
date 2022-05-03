# 확장 (Extension)
## **상속 vs 확장**
### 상속 : 수직 확장
* 저장 프로퍼티를 추가하거나
* 메서드를 변형, 추가하여 사용하는 것

<br/>

### 확장 : 수평 확장
* 현재 존재하는 타입에 메서드를 추가하여 사용
* 추가 메모리 공간이 필요한 저장 프로퍼티는 확장할 수 없음

<br/>

---

<br/>

## 확장 예시
```swift
// 기존 타입  (클래스, 구조체, 열거형 모두 가능)
class SomeClass {
}


extension SomeClass {
    // 메서드만 확장 가능
    func doSomething() {
        print("123")
    }
    
    // 저장 프로퍼티는 확장 불가
    // var storedProperty: Int = 0   ==> Extensions must not contain stored properties
}


let someInstance: SomeClass = SomeClass()
someInstance.doSomething()  // 123
```

<br/>

## 상속과 확장
```swift
class Person {
    var id = 0
    var name = "이름"
    var email = "1234@gmail.com"
    
    func walk() {
        print("사람이 걷는다.")
    }
}


class Student: Person {
    var studentId = 1
    
    override func walk() {
        print("학생이 걷는다.")
    }
    
    func study() {
        print("학생이 공부한다.")
    }
}


extension Student {  // 스위프트에서는 확장에서 구현한 메서드에 대한 재정의가 불가 ⭐️ (@objc 붙이면 가능)
    func play() {
        print("학생이 논다.")
    }
}


class Undergraduate: Student {
    var major = "전공"
    
    override func walk() {
        print("대학생이 걷는다.")
    }
    
    override func study() {
        print("대학생이 공부한다.")
    }
    
    func party() {
        print("대학생이 파티한다.")
    }
    
    
//    override func play() {     // 스위프트에서는 확장에서 구현한 메서드에 대한 재정의가 불가 ⭐️
//        print("대학생이 논다.")
//    }
}
```

<br/>

---

<br/>

## 확장의 장점 (실사용)
### 기존 타입의 기능 추가 (= 소급-모델링)
```swift
extension Int {
    var squared: Int {
        return self * self
    }
}

5.squared  // 25
3.squared  // 9
7.squared  // 49
```

<br/>

---

<br/>

## (본질적으로) 메서드인 형태만 확장 가능
### 1) [(타입, 인스턴스) 연산 프로퍼티](#연산-프로퍼티-확장)
### 2) [(타입, 인스턴스) 메서드](#메서드의-확장)
### 3) [새로운 생성자](#생성자의-확장) (⚠️ 클래스의 경우 편의 생성자만 추가 가능 / 지정생성자, 소멸자는 원래 타입 내부에 구현)
### 4) [서브스크립트](#서브스크립트-확장)
### 5) [새로운 중첩 타입 정의 및 사용](#중첩타입) / ex) 클래스 내부에 열거형 구현 등

<br/>

---

<br/>

## 연산 프로퍼티 확장
### 타입 연산 프로퍼티
```swift
extension Double {
    // 타입 연산 프로퍼티 확장
    static var zero: Double {
        return 0.0
    }
}

Double.zero   // 0.0
```

<br/>

### 인스턴스 연산 프로퍼티
```swift
extension Double {
    var km: Double { return self * 1_000.0 }   // 인스턴스 자신에 1000 곱하기
    var m: Double { return self }
    var cm: Double { return self / 100.0 }
    var mm: Double { return self / 1_000.0 }
    var ft: Double { return self / 3.28084 }
}


let oneInch = 25.4.mm
print("1인치는 \(oneInch) 미터")       // 1인치는 0.0254 미터"


let threeFeet = 3.ft
print("3피트는 \(threeFeet) 미터")     // 3피트는 0.914399970739201 미터"
```

<br/>

---

<br/>

## 메서드의 확장
### 타입 메서드
```swift
// ex) 애플이 만든 타입 메서드
Int.random(in: 1...100)
```

<br/>

### 인스턴스 메서드
```swift
extension Int {
    /// 숫자를 콤마 찍어서 문자열로 반환
    func insertComma() -> String {
            let numberFormatter = NumberFormatter()
            numberFormatter.numberStyle = .decimal
            let result = numberFormatter.string(from: NSNumber(value: self))!
            return result
    }
}

1000000.insertComma()   // "1,000,000"
```

<br/>

### mutating 인스턴스 메서드
#### 구조체, 열거형에서 자신의 속성을 변경하는 메서드는 mutating키워드가 필요함
```swift
extension Int {
    mutating func square() {
        self = self * self
    }
}

var someInt: Int = 3

someInt.square()

print(someInt)   // 9
```

<br/>

---

<br/>

## 생성자의 확장
#### 클래스의 경우 편의생성자만 확장이 가능함
#### 값타입(구조체, 열거형)의 경우, 지정 생성자 형태로도 자유롭게 생성자를 구현할 수 있음

<br/>

### **클래스**
#### 편의 생성자만 추가 가능
#### 지정생성자, 소멸자는 본래의 클래스에서 정의해야 함
```swift
// UIColor의 기본 생성자
var color = UIColor(red: 0.3, green: 0.5, blue: 0.1, alpha: 1)

// Extension - 편의생성자
extension UIColor {
    
    convenience init(color: CGFloat) {
        self.init(red: color/255, green: color/255, blue: color/255, alpha: 1)
    }
    
}

// 확장된 편의생성자 사용
UIColor(color: 1)
```

<br/>

### **구조체**
#### 구조체는 편의 생성자가 존재하지 않음
#### 이니셜라이저를 따로 생성하지 않으면 멤버와이즈 이니셜라이저가 자동 생성
#### 값타입의 경우, 지정 생성자 형태로도 생성자 구현이 가능 (상속이 없기 때문)
```swift
struct Point {
    var x = 0.0, y = 0.0
    
    init(x: Double, y: Double) {
        self.x = x
        self.y = y
    }
    
    // 생성자를 따로 만들지 않으면 아래의 2개 멤버와이즈 이니셜라이저가 자동 생성
    // init(x: Double, y: Double)
    // init()
}
extension Point {
    init() {
        self.init(x: 1.0, y: 1.0)
    }

    init(num: Double) {
        self.init(x: num, y: num)
    }
}
```
### 구조체 본체에 생성자 구현
* 기본생성자, 멤버와이즈 생성자 제공 X

### 구조체 본체에 생성자 구현X, extension으로 생성자 추가 구현
* 기본생성자, 멤버와이즈 생성자 제공
* extension으로 추가한 생성자도 사용 가능

<br/>

---

<br/>

## 서브스크립트 확장
```swift
// 공식문서 예제
// n자리의 십진수 반환하는 서브스크립트 추가
extension Int {
    
    subscript(num: Int) -> Int {
        
        var decimalBase: Int = 1
        
        for _ in 0..<num {
            decimalBase *= 10
        }
        
        return (self / decimalBase) % 10
    }
}

123456789[0]   // 9
123456789[1]   // 8
123456789[2]   // 7
```

<br/>

---

<br/>

## 중첩타입
#### 타입 내부에 다른 타입을 정의

<br/>

```swift
extension Int {
    
    enum Kind {       // 음수인지, 0인지, 양수인지
        case negative, zero, positive
    }
    
    // 연산 프로퍼티로 구현
    // 자신(self)를 기준으로 switch문 사용
    var kind: Kind {
        switch self {
        case 0:                   // 0인 경우
            return Kind.zero
        case let x where x > 0:   // 0보다 큰경우
            return Kind.positive
        default:                  // 나머지 (0보다 작은 경우)
            return Kind.negative
        }
    }
}

let a = 1
a.kind       // positive

let b = 0
b.kind       // zero

let c = -1
c.kind       // negative


// 이런식으로 접근도 가능
let test: Int.Kind = Int.Kind.negative
```

<br/>

### Int형에 추가한 Kind타입을 사용한 함수
```swift
func printIntegerKinds(_ numbers: [Int]) {
    for number in numbers {
        switch number.kind {
        case .negative:
            print("- ", terminator: "")
        case .zero:
            print("0 ", terminator: "")
        case .positive:
            print("+ ", terminator: "")
         }
    }
    print("")
}

printIntegerKinds([3, 19, -27, 0, -6, 0, 7])      // + + - 0 - 0 +
```