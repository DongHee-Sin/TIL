# self, Self
## self (소문자) :
### 인스턴스를 가르킴 (모든 인스턴스들이 암시적으로 생성하는 프로퍼티)

<br/>

### (1) 인스턴스 내부에서 범위를 명확하게 할 때 사용
```swift
class SomeClass {
   var name: String

   init(name: String) {
      self.name = name
   }
}
```

<br/> 

### (2) 값타입(구조체, 열거형)에서 인스턴스 자체의 값을 치환할 때 사용
```swift
struct SomeStruct {
    private var num: Int = 0
    
    mutating func addNum(_ num: Int) {
        self.num += num
    }
    
    mutating func reset() {
        // self(인스턴스 자신)에 새로운 구조체를 생성해서 할당
        self = SomeStruct()
    }
}

```

<br/>

### (3) 타입 프로퍼티/메서드에서는 타입 자체를 가르킨다.
```swift
struct SomeStruct {
    static var name: String = "a"
    
    static func printName() {
        // 타입 멤버에서 self는 타입 자체를 의미함 (SomeStruct)
        print(self.name)
    }
}
```

<br/>

### (4) 타입 인스턴스를 가르킬 수 있다.
#### 데이터 영역에 저장되는 타입 자체를 가르킴.
```swift
class SomeClass {
    static var name: String = "class"
}

// 타입 자체를 저장
// SomeClass.Type ==> 메타타입
let typeInstance: SomeClass.Type = SomeClass.self

// 타입 자체를 저장하므로 타입 프로퍼티 바로 접근도 가능
typeInstance.name         // 같은 의미
typeInstance.self.name    // 같은 의미
```

<br/>

---

<br/>

## Self (대문자) :
### 타입을 가르킴 (타입을 지칭하는 모든 부분에서 Self로 대체가 가능한듯)

<br/>

### (1) 타입을 선언하는 위치에서 사용
```swift
extension Int {
    // Int 대신 Self를 사용
    var zero: Self {
        return 0
    }
}

123.zero  // 0
```

<br/>

### (2) 타입 프로퍼티, 타입 메서드를 지칭하는 자리에서 대신 사용
```swift
extension Int {
    static var zero: Self = 0
    
    static func returnZero() -> Self {
        return Self.zero    // self키워드도 가능 ==> 타입 인스턴스를 가르키기 때문 (타입 메서드라서)
    }
}

Int.returnZero()   // 0
```

<br/>
<br/>

---

<br/>
<br/>

## 프로토콜에서의 Self/self 사용
#### 프로토콜을 채택하는 타입과 해당 타입의 인스턴스를 가르킨다.
#### 확장성 👍

<br/>

```swift
// BinaryInteger는 프로토콜임
extension BinaryInteger {
    // ==> 채택하는 모든 타입에서 사용할 함수
    func squared() -> Self {  // 타입자체(Int)를 가르킴
        return self * self    // 인스턴스(7)를 가르킴
    }
}

7.squared()  //49
```