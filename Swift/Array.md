# Collection - Array
#### 데이터를 순서대로 저장하는 Collection
#### 같은 데이터 타입만 저장할 수 있다.
#### Index 0부터 순서대로 저장된다.

<br/>

---

<br/>

## 타입선언
```swift
let someArray: [Int] = [1, 2, 3]
let someArray: Array<Int> = [1, 2, 3]
```

<br/>

---

<br/>

## 빈 배열 생성
```swift
let someArray: [Int] = []  // 2차원 배열도 마찬가지로 []만 작성
let someArray = [Int]()
```

<br/>

---

<br/>

## 메서드
```swift
var someArray: [Int] = [1, 2, 3, 4, 5]

// 배열 데이터의 수
someArray.count               // 5


// 배열이 비었는지 확인
someArray.isEmpty             // false


// 특정 데이터가 있는지 확인
someArray.contains(3)         // true
someArray.contains(where: {   // true
    $0 > 4
})


// 원하는 index에 데이터 추가
someArray.insert(0, at: 0)    // [0, 1, 2, 3, 4, 5]
someArray.insert(contentsOf: [1, 1, 1, 1], at: 0)  // [1, 1, 1, 1, 0, 1, 2, 3, 4, 5]


// 특정 index의 값 제거하고 반환
let zeroIndexElement: Int = someArray.remove(at: 0)
print(zeroIndexElement)   // 1


// 첫번째 Index 데이터 삭제 후 반환
someArray.removeFirst()

// 첫번째 Index부터 n개의 데이터 삭제
someArray.removeFirst(2)

// 마지막 Index 데이터 삭제 후 반환
someArray.removeLast()

// 마지막 Index부터 n개의 데이터 삭제
someArray.removeLast(2)


// 데이터만 모두 삭제하고, 배열이 차지하고 있던 메모리 공간은 그대로 유지
// 추후에 빈 배열에 값을 새로 할당하면 메모리 공간을 만드는 작업을 생략할 수 있어서 아주 조금 더 속도가 빠름
someArray.removeAll(keepingCapacity: true)



// 데이터의 max(), min()값
someArray.max()  // 5
someArray.min()  // 0


// 데이터 위치 스왑
someArray.swapAt(0, 5)


// 랜덤 데이터 반환
someArray.randomElement()


// Index찾기 (앞에서부터 or 뒤에서부터)
someArray.firstIndex(of: 5)
someArray.firstIndex(where: {
    $0 % 2 == 0
})

someArray.lastIndex(of: 5)


// 데이터 추가
someArray.append(10)
```

<br/>

---

<br/>

## 데이터 접근 & 수정
서브스크립트 문법 사용
```swift
var someArray: [Int] = [1, 2, 3, 4, 5]

// 값 조회
someArray[0]  // 1


// 값 교체
someArray[0] = 10  // [10, 2, 3, 4, 5]


// 범위 교체
someArray[0...1] = [100, 200]  // [100, 200, 3, 4, 5]

someArray.replaceSubrange(0...1, with: [1, 2])  // 1, 2, 3, 4, 5]


// 범위 삭제
someArray[0...1] = []   // [3, 4, 5]
```

<br/>

---

<br/>

## enumerate
열거된 데이터를 Named 튜플 형태로 전달<br/>
index와 element를 튜플로 받아서 사용할 수 있음
```swift
var someArray: [Int] = [10, 20, 30, 40, 50]

for (index, element) in someArray.enumerated() {
    print("index: \(index), element: \(element)")
}
// index: 0, element: 10
// index: 1, element: 20
// index: 2, element: 30
// index: 3, element: 40
// index: 4, element: 50

//---------------------------------------------------

var someArray: [Int] = [10, 20, 30, 40, 50]

// tuple = (offset, element)
for tuple in someArray.enumerated() {
    print(tuple)
}
// (offset: 0, element: 10)
// (offset: 1, element: 20)
// (offset: 2, element: 30)
// (offset: 3, element: 40)
// (offset: 4, element: 50)

for tuple in someArray.enumerated() {
    print(tuple.offset)
    print(tuple.element)
}
```

<br/>

---

<br/>

## zip
#### 두 개의 시퀸스를 매개변수로 입력받고, 튜플쌍의 시퀸스를 반환한다.
```swift
let someArr1: [String] = ["kim", "park", "lee"]
let someArr2: [Int] = [1, 2, 3]

// type : Zip2Sequence<[String], [Int]>
let zipSequence = zip(someArr1, someArr2)

for eachData in zipSequence {
    print(eachData.0)
    print(eachData.1)
}
```

<br/>

---

<br/>

## removeLast VS popLast
### **공통점** :
#### 배열의 마지막 요소를 삭제한 후 반환한다.

<br/>

### **차이점** :
### (1)
#### removeLast의 반환값 => Non-Optional Type
#### popLast의 반환값 => Optional Type

<br/>

#### 즉, 빈 배열에 각각의 메서드를 실행했을 때 
#### removeLast는 런타임에러를 반환하는 반면
#### popLast는 nil을 반환한다.

<br/>

### (2)

#### 반환된 요소를 사용하지 않는 경우 popLast()에 @discardableResult를 사용해야 하는 반면 removeLast()는 필요하지 않음

```swift
var testArray: [Int] = []

testArray.removeLast()

testArray.popLast()   // Result of call to 'popLast()' is unused
```
