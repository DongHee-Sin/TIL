# Optional
### 값이 없을 수 있다는 것을 의미

<br/>

일반적인 데이터타입 키워드 뒤에 "?"를 붙여서 옵셔널 타입임을 표현한다.
```swift
var someInt: Int? = 5           // Optional(5)
var someString: String? = "a"   // Optional("a")
```

<br/>

값이 없는 옵셔널 타입에 접근하면 nil을 반환한다.
```swift
var someInt: Int?  // 이처럼 선언만 하고 값을 초기화하지 않으면 Optional 타입에서는 nil로 자동 초기화된다.
print(someInt)     // nil
```

### nil은 실제로 값이 없는게 아니라 값이 없음을 표현하는 키워드
### 옵셔널 타입은 내부적으로 enum으로 구성되어 있다. (데이터 타입 & nil)

<br/>

---

<br/>

## Optional을 사용하는 이유
* 값이 들어있지 않은 변수(상수)에 접근하면 에러가 발생한다.
* 스위프트에서는 이런 문제를 방지하기 위해 Optional타입을 사용하여 에러의 발생을 방지한다.

<br/>
<br/>

### 사용자는 옵셔널값을 사용하기 위해 추출하는 과정을 거쳐야한다. (Unwrapping)

<br/>

---

<br/>

## **Unwrapping의 4가지 방법**

<br/>

### **1. 강제 추출(Forced Unwrapping)**
"!" 키워드를 사용한다. <br/>
값이 있다는 것을 확신할 수 있을때만 사용. <br/>
값이 없다면 런타임에러가 발생하므로 신중하게 사용해야 한다.
```swift
var str: String? = "HI"

print(str)    // Optional("HI")
print(str!)   // HI
```

<br/>

### **2. 조건문을 사용하여 nil이 아닌것을 확인한 후 사용**
조건문을 통해 nil이 아님을 확인한 후 강제 추출로 값을 사용
```swift
var str: String? = "HI"

// 1. if문 사용
if str != nil {
    print(str!)   // HI
}

// 2. guard문 사용
func printStr() {
    guard str != nil else {return}
    print(str!)
}
printStr()        // HI

```

<br/>

### **3. 옵셔널 바인딩 ★★★**
바인딩이 성공하면 특정 작업을 수행하겠다는 의미 <br/>
옵셔널 값을 새로운 상수에 바인딩하여 사용
```swift
var str: String? = "HI"

// if let 바인딩
if let s = str {
    print(s)     // HI
}

// guard let 바인딩
func printStr() {
    guard let s = str else {
        return
    }
    print(s)
}
printStr()       // HI
```

<br/>

### **4. 닐 코얼레싱(Nil-Coalescing)연산자**
디폴트값(기본값)을 제공하는 방법
```swift
var str: String? = "HI"

print(str)             // Optional("HI")

print(str ?? "기본값")   // HI

str = nil

print(str ?? "기본값")   // 기본값
```

<br/>

---

<br/>

## 매개변수와 Optional
#### 함수의 매개변수가 Optional Type인 경우 **기본값을 nil로** 부여하는게 좋음
#### 기본값을 주지 않으면 함수를 호출할 때마다 nil을 번거롭게 전달해줘야됨
#### 기본값을 미리 설정해두면 값을 전달하고 싶지 않을 때 호출 과정에서 생략해줄 수 있음
```swift
func someFunction(name: String, age: Int? = nil) {
    print("name: \(name), age: \(age ?? 1)")
}

someFunction(name: "홍길동", age: nil)

someFunction(name: "김개똥")
```