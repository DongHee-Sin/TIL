# Collection - Set
#### 수학에서의 집합과 비슷한 개념
#### 순서가 없고, 내부 요소가 중복되지 않는다.
#### 각 요소는 유일한 값 (Hashable)
#### Set은 값 검색에 Hashing 알고리즘을 사용하기 때문에 검색속도가 빠르다 O(1)

<br/>

---

<br/>

## 타입 선언
```swift
let someSet: Set<Int> = [1, 1, 2, 3]  // [1, 3, 2]

let someSet2: Set = [1, 1, 2, 3]  // [1, 3, 2]
```

<br/>

---

<br/>

## 빈 Set 생성
```swift
let someSet: Set<Int> = []

let someSet2 = Set<Int>()
```

<br/>

---

<br/>

## Update (데이터 삽입, 교체, 추가)
#### Set에는 서브스크립드 문법이 없다.
```swift
var someSet: Set<Int> = [1, 1, 2, 3]  // [1, 3, 2]

// 이미 동일한 값이 있으면? -> 변하지 않음
someSet.update(with: 1)  //  return Optional(1)

// 새로운 값 추가
someSet.update(with: 5)  //  return nil
```

<br/>

---

<br/>

## 요소 삭제
```swift
var someSet: Set<Int> = [1, 1, 2, 3]  // [1, 3, 2]


someSet.remove(1)   // return 1

someSet.remove(5)   // return nil
```

<br/>

---

<br/>

## Set의 비교
Dictionary와 마찬가지로 순서가 없고, Hashable해서 데이터만 같으면 "==" 연산에서 true가 반환됨

<br/>

---

<br/>

## 부분집합 / 상위집합 / 서로소
```swift
// 부분집합 여부
someSet.isSubset(of: <#T##Set<Int>#>)

// 진부분집합 여부
someSet.isStrictSubset(of: <#T##Set<Int>#>)



// 상위집합 여부
someSet.isSuperset(of: <#T##Set<Int>#>)

// 진상위집합 여부
someSet.isStrictSuperset(of: <#T##Set<Int>#>)



// 서로소 여부 (완전 다른지?)
someSet.isDisjoint(with: <#T##Set<Int>#>)
```

<br/>

---

<br/>

## 합집합, 교집합, 차집합, 대칭차집합
```swift
// 합집합
let unionSet = someSet.union(<#T##other: Sequence##Sequence#>)


// 교집합
let interSet = someSet.intersection(<#T##other: Sequence##Sequence#>)


// 차집합
let subSet = someSet.subtracting(<#T##other: Sequence##Sequence#>)


// 대칭차집합
let symmetricSet = someSet.symmetricDifference(<#T##other: Sequence##Sequence#>)
```

<br/>

---

<br/>

## 정렬
#### Set에 sorted()함수를 사용하면 배열로 반환한다.
```swift
var someSet: Set<Int> = [1, 1, 2, 3]

var someArray: [Int] = someSet.sorted()
```