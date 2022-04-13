# stride (성큼성큼 걷다?!)
stride: 성큼성큼 걷다, 걸음, 걸음걸이와 같은 의미를 가지고 있다.

<br/>

## 사용
```swift
let test = stride(from: 15, through: 0, by: -3)  // 15, 12, 9, 6, 3, 0

let test2 = stride(from: 15, to: 0, by: -3)      // 15, 12, 9, 6, 3
```
from: 시작하는 수 <br/>
through, to: 도착하는 수
   * through: 도착하는 수를 포함하는 리스트
   * to: 도착하는 수를 포함하지 않는 리스트

by: 얼만큼 간격을 두고 생성할지 결정

<br/>

---

<br/>

## 반복문(for)과 함께 사용
```swift
for num in stride(from: 5, through: 0, by: -1) {
    print(num, terminator: " ")
}
// 출력: 5 4 3 2 1 0
```
