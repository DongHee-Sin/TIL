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

<br/>

### nil은 실제로 값이 없는게 아니라 값이 없음을 표현하는 키워드
### 옵셔널 타입은 내부적으로 enum으로 구성되어 있다. (데이터 타입 & nil)
```swift
// 옵셔널 타입의 내부 구현
enum Optional<Wrapped> {  // 제네릭 문법
    case some(Wrapped)    // 연관값 Wrapped는 Type이름 (ex. String, Int...)
    case none             // (= nil)
}
```

<br/>

### **구현 예시**
```swift
enum Optional {
    case some(Int)
    case none
}

let someInt: Optional = .some(5)

print(someInt)  // some(5)
```

<br/>

### **옵셔널 추출 예시** (애플이 미리 만들어둠)
```swift
// 열거형의 연관값 데이터를 바인딩시키는 방식으로 추출함
var someInt: Int? = 5

switch someInt {
case .some(let value):
    print(value)
case .none:
    print("nil")
}
```

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

<br/>

---

<br/>

## IUO (옵셔널 묵시적 추출)
### 별도의 옵셔널 추출 과정 없이 자동으로 옵셔널이 해제되는 방식
### Optional Type이며, Non-Optional Type으로 처리해야 하는 상황이 생기면 값을 자통으로 추출

<br/>

### 값을 강제로 추출하기 때문에 nil이 들어있는 상태에서 접근하면 런티임 에러가 발생한다.

<br/>

```swift
// ? 가 아닌 ! 키워드를 통해 타입을 선언함
var someValue: Int! = 5

print(someValue)    // Optional(5)

// Non-Optional Type에 할당
var someValue2: Int = someValue

print(someValue2)   // 5
```

<br/>

### 즉, 위 코드처럼 IUO를 직접 사용할 때에는 Optional타입으로 사용된다.
### IUO값을 Non-Optional Type에 할당할 때 별도 추출 과정을 생략하고 할당이 가능한것.

<br/>

### IUO는 프로퍼티 지연 초기화를 위해서 사용한다. (lazy var와 비슷)

<br/>
<br/>

### **언제 사용할까???**
```swift
// 스토리보드 인스턴스의 연결
@IBOutlet weak var someLabel: UILabel!
```

<br/>

### 위 코드처럼 @IBOutlet에 IUO가 사용되는 이유 :
* IUO를 사용하지 않으면 ViewController가 생성되는 시점에 UILabel의 인스턴스가 존재해야 한다.
* 그런데 스토리보드로 UI를 생성하면 ViewController의 인스턴스가 먼저 생성된 후, 스토리보드 인스턴스들이 연결되는 구조로 동작한다.
* 옵셔널로 처리하지 않으면 ViewController 인스턴스가 생성될 때에는 스토리보드 인스턴스는 메모리에 올라오지 않았으므로 에러가 나온다.

<br/>

* ==> IUO로 선언해두면 ViewController의 인스턴스가 생성될 때 일단 옵셔널과 동일하게 nil로 초기화가 되고, 바로 직후에 스토리보드 인스턴스들이 연결된다.
* ==> 각 스토리보드 인스턴스는 IUO로 선언되어 있기 때문에 내부에서 접근할 때 옵셔널 체이닝을 하지 않아도 바로 사용이 가능하게 되는것

<br/>

---

<br/>

## 옵셔널 체이닝
#### 옵셔널 타입에 대해, 접근연산자를 호출하는 방법
#### 접근연산자(.)를 사용하여 옵셔널 타입에 접근시 "?" 키워드를 사용하는 것 ==> 앞의 값이 옵셔널일 가능성을 내포한다는 의미
* 체이닝의 결과는 항상 옵셔널이다. (마지막으로 접근한 타입이 옵셔널이 아니더라도)
* 체이닝 과정에서 nil이 하나라도 있다면, 이후 표현식을 평가하지 않고 nil을 반환

<br/>

```swift
class Dog {
    var name: String?
    var weight: Int
    
    init(name: String, weight: Int) {
        self.name = name
        self.weight = weight
    }
}


class Person {
    var dog: Dog?
}


let dong: Person? = Person()

dong?.dog?.name   // nil

dong?.dog = Dog(name: "콩", weight: 10)

dong?.dog?.name   // Optional("콩")
```

<br/>
<br/>

### 실제 데이터를 사용하기 위해서는 결국 언래핑 과정이 필요함
1. 값이 있음이 확실한 경우 ? 대신 !를 사용하여 강제 언래핑 가능
2. 옵셔널 바인딩
3. 닐 코얼레싱 연산자 (??)

<br/>

---

<br/>

### 아래 내용은 주교재 270~ 참고

<br/>

### 함수 관련 표기법
#### cat 인스턴스의 myMaster 함수에 접근하여 리턴값으로 받는 인스턴스의 name프로퍼티로 접근하는 체이닝
#### 함수 실행() 앞의 "?"는 함수 자체가 옵셔널임을 나타내고, 실행문 뒤의 "?"는 함수의 리턴값이 옵셔널타입임을 나타냄
```swift
cat?.myMaster?()?.name
```

<br/>

### Dictionary 관련 표기법
#### library 인스턴스의 books라는 딕셔너리 저장 프로퍼티에 접근하여 value의 저장 프로퍼티 name에 접근하는 체이닝
```swift
// books?           ==> 딕셔너리(저장 프로퍼티)가 옵셔널 타입
// books["Apple"]?  ==> 딕셔너리의 결과값(value)이 없을 수 있다는 의미 (원래 옵셔널임)
library.books?["Apple"]?.name
```

<br/>

### 옵셔널 체이닝에서 함수의 실행
```swift
var bori: Dog? = Dog(name: "보리", weight: 20)

bori?.layDown()
bori?.sit()             
```
#### 앞의 타입이 nil일 경우 메서드가 실행되지 않음