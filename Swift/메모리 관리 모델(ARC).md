# 메모리 관리 모델
### Swift는 RC(Reference Counting)을 통해 메모리를 관리한다.
#### 컴파일 시점에 메모리 해제시점을 결정

<br/>
<br/>

### 메모리 - 힙영역
#### 메모리의 힙영역에 할당되는 데이터는 (클래스의 인스턴스, 클로저) **개발자의 관리가 필요**하다.
#### 할당된 데이터가 메모리에서 해제되지 않으면 **메모리 누수**가 발생하기 때문

<br/>

#### ☝️ 클래스의 인스턴스는 클래스의 주소(데이터영역)와 RC를 추가로 저장한다.

<br/>

---

<br/>

## 메모리 관리 모델의 종류

## GC
#### Garbage Collector의 약자로 Java에서 사용하는 모델이다.
#### 런타임 임의의 시간에 힙영역을 스캔하여 불필요한 부분을 해제시킨다.

<br/>
<br/>

## MRC
#### Objective-C에서 지원하는 메모리 관리 모델이다. (objc는 MRC, ARC를 모두 지원)
#### 개발자가 수동으로 RC를 관리하는 모델
#### 아래의 코드를 수동으로 작성해서 관리했었다. (실수할 가능성 높음)
* retain : RC 증가
* release : RC 감소

<br/>
<br/>

## ⭐️ **ARC**
#### Swift, Objective-C에서 지원하는 메모리 관리 모델
### retain, release코드를 컴파일러가 자동으로 삽입하여 RC를 관리해준다. (안정성 증가)

<br/>

> RC >= 1 ? 메모리에 유지 : 메모리에서 해제

<br/>

```swift
class Dog {
    var name: String
    var weight: Double
    
    init(name: String, weight: Double) {
        self.name = name
        self.weight = weight
    }
    
    deinit {
        print("\(name) 메모리 해제")
    }
}


var dog1: Dog?
var dog2: Dog?
var dog3: Dog?


dog1 = Dog(name: "댕댕이", weight: 7.0)     // RC + 1   RC == 1
dog2 = dog1                               // RC + 1   RC == 2
dog3 = dog1                               // RC + 1   RC == 3


dog3?.name = "깜둥이"
dog2?.name // 깜둥이
dog1?.name // 깜둥이


dog3 = nil                                // RC - 1   RC == 2
dog2 = nil                                // RC - 1   RC == 1
dog1 = nil                                // RC - 1   RC == 0    // 메모리 해제
```

<br/>

### RC가 증가하는 경우
1. 인스턴스를 새로 생성할 때
2. 기존 인스턴스를 다른 변수에 대입(할당)할 때

<br/>

### RC가 감소하는 경우
1. 인스턴스를 참조하던 변수가 메모리에서 해제되는 경우
2. 참조하던 변수에 nil이 할당되는 경우
3. 참조하던 변수에 다른 값을 할당하는 경우
4. 프로퍼티로 참조를 저장하다가, 속해 있는 인스턴스가 메모리에서 해제되는 경우


<br/>

---

<br/>

## 메모리 누수 (강한 참조 사이클)
### 인스턴스가 서로를 참조하는 강한 참조 사이클이 발생하면, 해당 인스턴스들은 메모리에서 해제되지 않는다.

<br/>

```swift
class Dog {
    var name: String
    var owner: Person?
    
    init(name: String) {
        self.name = name
    }
    
    deinit {
        print("\(name) 메모리 해제")
    }
}


class Person {
    var name: String
    var pet: Dog?
    
    init(name: String) {
        self.name = name
    }
    
    deinit {
        print("\(name) 메모리 해제")
    }
}


var bori: Dog? = Dog(name: "보리")
var gildong: Person? = Person(name: "홍길동")

// 각 인스턴스의 저장 프로퍼티로 서로의 참조를 저장
bori?.owner = gildong
gildong?.pet = bori


// 강한 참조 사이클(Strong Reference Cycle)이 일어남
// 인스턴스의 메모리 주소를 저장한 스택영역 변수에 nil을 할당해도 힙영역의 인스턴스가 메모리에서 해제되지 않는다.
bori = nil        // deinit() 실행 X
gildong = nil     // deinit() 실행 X
```

<br/>
<br/>

## 메모리 누수 해결방안
### 참조하는 인스턴스의 RC를 증가시키지 않고, 참조하는 방법
1. Weak Reference (약한참조)
2. Unowned Reference (비소유 참조)

<br/>
<br/>

## Weak Reference
#### 인스턴스를 참조하는 변수 앞에 weak키워드 추가
#### 참조하던 인스턴스가 메모리에서 해제된 경우, 자동으로 nil이 할당된다.

<br/>

### weak var + Optional Type 으로만 사용 가능
* var : 참조하는 인스턴스가 사라지면 nil로 변해야하기 때문
* optional : 위와 같은 이유로 nil이 할당되려면 반드시 optional이어야 함

<br/>

```swift
class Dog {
    var name: String
    weak var owner: Person?     // weak 키워드 ==> 약한 참조
    
    init(name: String) {
        self.name = name
    }
    
    deinit {
        print("\(name) 메모리 해제")
    }
}


class Person {
    var name: String
    weak var pet: Dog?         // weak 키워드 ==> 약한 참조
    
    init(name: String) {
        self.name = name
    }
    
    deinit {
        print("\(name) 메모리 해제")
    }
}


var bori: Dog? = Dog(name: "보리")
var gildong: Person? = Person(name: "홍길동")


// 강한 참조 사이클이 일어나지 않음
bori?.owner = gildong
gildong?.pet = bori



// 메모리 해제가 잘됨(사실 이 경우 한쪽만 weak으로 선언해도 상관없음)
// bori = nil
// gildong = nil



// ⭐️ 약한 참조의 경우, 참조하던 인스턴스가 사라지면 nil로 자동 초기화된다.
gildong = nil
bori?.owner   // nil
```

<br/>

---

<br/>

## Unowned Reference
#### 인스턴스를 참조하는 변수 앞에 unowned키워드 추가

<br/>

```swift
class Dog1 {
    var name: String
    unowned var owner: Person1?    // Swift 5.3 이전버전에서는 비소유참조의 경우, 옵셔널 타입 선언이 안되었음
    
    init(name: String) {
        self.name = name
    }
    
    deinit {
        print("\(name) 메모리 해제")
    }
}

class Person1 {
    var name: String
    unowned var pet: Dog1?
    
    init(name: String) {
        self.name = name
    }
    
    deinit {
        print("\(name) 메모리 해제")
    }
}


var bori1: Dog1? = Dog1(name: "보리1")
var gildong1: Person1? = Person1(name: "홍길동1")


// 강한 참조 사이클이 일어나지 않음
bori1?.owner = gildong1
gildong1?.pet = bori1


// 메모리 해제가 잘됨(사실 이 경우 한쪽만 unowned로 선언해도 상관없음)
//bori1 = nil
//gildong1 = nil



// ⭐️ 비소유 참조는 참조하고 있던 인스턴스가 사라져도 nil로 초기화 되지 않는다.
gildong = nil
bori?.owner   // nil로 자동 초기화되지 않아서 런타임에러 발생


// 에러를 방지하기 위해서는 nil로 재설정하는 과정이 필요함
gildong = nil
bori?.owner = nil
bori?.owner
```

<br/>
<br/>

## Weak vs Unowned
#### 핵심 : 참조하던 인스턴스가 사라졌을 때, nil로 자동 초기화 되는가??

<br/>

### **Weak** : 소유자에 비해, 짧은 생명주기를 가진 인스턴스를 참조할 때 주로 사용
#### ==> 인스턴스를 nil로 확인 가능 / nil인 경우 작업을 중단하는 분기처리 가능
#### 항상 Optional Type이기 때문에 바인딩을 필요로 함

<br/>

### **Unowned** : 소유자 보다 인스턴스의 생명주기가 더 길거나, 같은 경우 사용
#### ==> 인스턴스 nil로 확인 불가 / 참조하던 인스턴스가 해제되었는데 접근하면 에러 발생
#### Non-Optional Type으로 사용 가능 (에러 가능성이 있긴 하지만 바인딩을 필요로 하지 않음)

<br/>
<br/>

#### ==> unowned는 사용시 고려해야할 것이 있어서(nil), 실제 프로젝트는 weak 키워드를 많이 사용함
#### 이론적으로는 unowned가 살짝 더 빠름..!!

<br/>
<br/>

---

<br/>
<br/>

## unowned를 사용하기 좋은(?) 상황
#### 인스턴스-클로저의 강한 참조 순환 해결

<br/>

```swift
// 순환참조가 발생하는 코드
class Human {
    var name = "NAME"
    lazy var getName: () -> String = {   // lazy를 지우면 Cannot find 'self' in scope 에러 발생
        return self.name                 // 클래스를 정의하는 시점에는 self(인스턴스)가 없으니깐(?)
    }
    
    init(name: String) {
        self.name = name
    }
 
    deinit {
        print("Deinit!")
    }
}

var human: Human? = .init(name: "NAME")
print(human!.getName())  //


human = nil  // deinit 호출 X



// ------------------------------------------------------------------------------------------------

// 순환참조 해결
class Human {
    var name = "NAME"
    lazy var getName: () -> String = { [unowned self] in
        return self.name
    }
    
    init(name: String) {
        self.name = name
    }
 
    deinit {
        print("Deinit!")
    }
}

var human: Human? = .init(name: "NAME")
print(human!.getName())  //


human = nil  // deinit 호출됨
```

<br/>

#### 위와 같은 코드에서 lazy 클로저 부분은 인스턴스가 메모리에 존재해야만 접근할 수 있고, 이는 self가 반드시 존재한다는 의미이므로 unowned를 사용하여 옵셔널바인딩 없이 사용이 가능