# COW : Copy On Write
리소스를 복제했지만, 해당 값이 수정되지 않은 경우 새로운 리소스를 생성하지 않고 기존의 리소스를 공유하는 방식으로 리소스를 관리하는 방법이다.

</br>

흔히 사용되는 String같은 기본 자료형과 CollectionType(Array, Dictionary, Set)은 값타입으로 다른 변수나 상수에 할당할 경우 값이 복사되어 전달되는데 <br/>
Swift에서는 CollectionType의 리소스를 효율적으로 사용하기 위해 실제 원본 데이터 또는 복사된 데이터가 수정되어 메모리 공간을 추가로 사용해야되는 상황이 오기 전까지는 서로 같은 메모리 공간을 참조하여 메모리를 효율적으로 사용한다.

<br/>

```swift
var array = Array(repeating: "A", count: 100)
address(&array)

var newArray = array  // 실제로 복사되는 것이 아니라, 원본 데이터를 공유하고 있다.
address(&newArray)

newArray[0] = "B"     // 값이 변경되어 새로운 메모리 공간이 필요한 상황이 왔을 때, 실제로 복사한다.

address(&array)
address(&newArray)
```

<br/>

값을 복사하여 사용하지만, 복사본이나 원본 데이터가 수정되지 않는 경우 COW를 통해 메모리를 더 효율적으로 사용할 수 있다. <br/>
단, 원본이나 복사본 데이터가 수정되는 시점에 실제 데이터의 복사가 이루어지기 때문에 처음 값이 수정되는 시점에는 약간의 오버헤드를 발생시킨다고 한다.

<br/>

---

<br/>

## Substring (= String.SubSequence)
> typealias SubSequence = Substring

Substring도 COW 방식으로 동작함으로써 메모리 공간을 효율적으로 사용한다. <br/>
여러 방법을 통해 문자열의 일부를 저장하면 String.SubSequence 타입으로 문자열이 저장되는데, 이 타입은 원본 문자열의 메모리 공간을 공유하다가 값이 수정되어 새로운 메모리 공간이 필요해지는 시점에 실제로 복사되어 저장된다.
```swift
var greeting = "Hello, world!"


// ===== String.SubSequence는 별도 메모리를 할당받지 않고, 본체의 메모리 공간을 공유한다.

// String.Index
let index: String.Index = greeting.firstIndex(of: ",") ?? greeting.endIndex    // ,(콤마)의 인덱스
let subString1: String.SubSequence = greeting[..<index]                         // 처음부터 인덱스까지

// prefix
let subString2: String.SubSequence = greeting.prefix(5)    // 앞에서 5글자

// suffix
var subString3: String.SubSequence = greeting.suffix(5)    // 뒤에서 5글자



// ===== String Type으로 변환해서 저장하거나 수정이 발생하면 별도 메모리 공간이 필요

let string1: String = String(subString1)

subString3 = "abcd"



// ===== 원본값이 변경되면, 원본의 메모리를 공유받던 Substring 문자열들은 새로운 메모리 공간을 만들어서 저장된다.
greeting = "new greeting"
```