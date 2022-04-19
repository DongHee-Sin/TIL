# Collection - Dictionary
#### Key와 Value의 쌍으로 이루어지는 Collection
#### Key값은 중복이 불가능하다. (Value는 중복 가능)
#### 동일한 데이터 Type만 저장할 수 있다.
#### 데이터의 순서가 없다.
#### Dictionary의 **Key값은 Hashable**해야한다. [Hashable](https://babbab2.tistory.com/149)

<br/>

---

<br/>

## 선언
```swift
let someDictionary: [Int: String] = [1: "하나", 2: "둘", 3: "셋"]

let someDictionary2: Dictionary<Int, String> = [1: "하나", 2: "둘", 3: "셋"]
```

<br/>

---

<br/>

## 빈 Dictionary 생성
```swift
let someDictionary: [Int: String] = [:]

let someDictionary = [Int, String]()

let someDictionary3 = Dictionary<Int, String>()
```

<br/>

---

<br/>

## 함수
```swift
let someDictionary: [Int: String] = [1: "하나", 2: "둘", 3: "셋"]

// 조건에 맞는 데이터가 있는지 확인
someDictionary.contains(where: { (key, value) in
    key > 2 && value.count == 2
})


// 랜덤 데이터 반환 (Optional Tuple형태로)
someDictionary.randomElement()    // (key 2, value "둘")


// keys, values만 따로 빼기
someDictionary.keys
someDictionary.values


// 정렬도 가능 -> 배열 형태로 반환
someDictionary.keys.sorted()
someDictionary.values.sorted()
```
말고도 Array에서 사용하는 대부분의 함수를 사용 가능 (count, isEmpty 등..)

<br/>

---

<br/>

## 데이터 조회
#### 서브스크립트 문법으로 Key를 전달하면 Value를 반환한다.
#### Key에 해당하는 Value가 없을 수 있기 때문에 반환값은 Optional Type이다.
```swift
let someDictionary: [Int: String] = [1: "하나", 2: "둘", 3: "셋"]

print(someDictionary[1])  // Optional("하나")
print(someDictionary[4])  // nil
```

<br/>

기본값을 따로 지정해주는 방법도 있다.
```swift
let someDictionary: [Int: String] = [1: "하나", 2: "둘", 3: "셋"]

print(someDictionary[4, default: "기본값"])   // "기본값"
```

<br/>

---

<br/>

## Update (데이터 삽입, 교체, 추가)
#### 중복되는 Key값이 있다면 Value 업데이트
#### 중복되는 Key값이 없다면 데이터 추가

```swift
var someDictionary: [Int: String] = [1: "하나", 2: "둘", 3: "셋"]

// 서브스크립트 문법 사용
someDictionary[1] = "바뀐 하나"


// updateValue함수 사용
someDictionary.updateValue("바뀐 둘", forKey: 2)

print(someDictionary)  // [3: "셋", 1: "바뀐 하나", 2: "바뀐 둘"]
```

<br/>

updateValue함수는 oldValue를 리턴값으로 갖는다.
```swift
var someDictionary: [Int: String] = [1: "하나", 2: "둘", 3: "셋"]

// updateValue함수 사용
let test = someDictionary.updateValue("바뀐 둘", forKey: 2)

print(test)  // Optional("둘")
```

<br/>

---

<br/>

## 데이터 삭제
#### removeValue함수로 삭제도 가능하며, 삭제하는 Value를 리턴값으로 한다.
#### Array의 removeAll과 마찬가지로 keepingCapacity 매개변수도 사용할 수 있다.
```swift
var someDictionary: [Int: String] = [1: "하나", 2: "둘", 3: "셋"]

// 서브스크립트 문법으로 삭제
someDictionary[1] = nil

someDictionary[4] = nil   // Key값이 없어도 에러가 발생하진 않음


// removeValue함수로 삭제
someDictionary.removeValue(forKey: 2)  // 삭제한 Value를 반환한다.

someDictionary.removeValue(forKey: 2)  // 마찬가지로 Key값이 없어도 에러가 발생하지 않고, nil을 반환한다.



// 데이터 전체 삭제
someDictionary.removeAll()
someDictionary.removeAll(keepingCapacity: true)
```
 
<br/>

---

<br/>

## Dictionary의 비교
Dictionary는 순서가 중요하지 않기 때문에 Key: Value쌍의 데이터만 일치하면 "==" 연산을 수행했을 때 true가 반환된다.<br/>
Dictionary의 Key는 Hashable하기 때문
```swift
var someDictionary: [Int: String] = [1: "하나", 2: "둘", 3: "셋"]

var someDictionary2: [Int: String] = [1: "하나", 3: "셋", 2: "둘"]

print(someDictionary == someDictionary2)  // true
```

<br/>

---

<br/>

## for문과 함께 사용
#### for문을 돌리면 Named Tuple 형태로 값을 받는다.
```swift
var someDictionary: [Int: String] = [1: "하나", 2: "둘", 3: "셋"]

for item in someDictionary {
    print("key: \(item.key), value: \(item.value)")
}
// key: 2, value: 둘
// key: 3, value: 셋
// key: 1, value: 하나

// ---------------------------------------------------

var someDictionary: [Int: String] = [1: "하나", 2: "둘", 3: "셋"]

for (key, value) in someDictionary {
    print("key: \(key), value: \(value)")
}
// key: 2, value: 둘
// key: 3, value: 셋
// key: 1, value: 하나
```