Switch문
===

## 특징
* if문보다 응용 범위가 좁은편 (더 한정적인 조건문)
* 실제 앱에서 분기처리에 더 많이 사용됨
* if문보다 가독성이 좋음
* case에 부등식(<, =, >)을 사용할 수 없고, 범위연산자(1...10)으로 정의해야 함
* 특정 조건에서 실행하고자 하는 코드가 없으면 break 키워드를 사용
* fallthrough 키워드를 사용하여 조건에 관계없이 다음 case에 해당하는 코드를 실행시킬 수 있음
* enum, Bool타입처럼 한정적인 값이 비교값으로 들어오는게 아니라면 default문을 반드시 사용

---
<br/>

## 사용
```swift
switch 5 {
case ...0:
   print("~0")
case 1..<10:       // 내부 구현: 1..<9 ~= 5(비교값)
   print("1~9")
   fallthrough
case 10...:
   print("10~")
default:
   break
}
// 출력
// 1~9
// 10~  : fallthrough 키워드를 사용하여 조건에 관계없이 실행됨
```

---
<br/>

## 바인딩

### Value Binding
바인딩: 새로운 변수(또는 상수)에 새로운 식별자로 할당하는 것
```swift
var first: Int = 10

var second: Int = first  // first의 값을 second라는 새로운 식별자에 바인딩
```

<br/>

### Switch문에서 사용하는 바인딩 개념
비교값을 변수(var)로 바인딩하면 내부 구문에서 변형하여 사용 가능(많이 사용되는 방식은 아님)<br/>
Int는 Struct이므로 바인딩된 값의 변형은 원본값에 영향을 미치지 않는다. (값이 복사되어 할당)
```swift
let someInt: Int = 0

switch someInt {
case var bindingInt:
    bindingInt += 1
    print("binding number is \(bindingInt)")  // binding number is 1
default:
    break
}

print(someInt)  // 0
```

<br/>

### Switch문에서의 바인딩 활용 (with where절)
where절(조건)과 함께 사용됨<br/>
다른 상수(변수)에 값을 바인딩한 후 where(조건절)을 통해 조건을 확인
```swift
let someInt: Int = 0

switch someInt {
case let bindingInt where bindingInt % 2 == 0:
    print("짝수")
case let bindingInt where bindingInt % 2 != 0:
    print("홀수")
default:
    break
}
// 출력: 짝수
```

<br/>

### 왜 굳이 바인딩하고 where절을 통해 조건을 줄까?
* switch구문은 비교값과 case구문이 같은지를 확인한다. 즉, 위 코드에서 someInt의 값이 짝수, 홀수인지 비교하기 위해 case에 "someInt % 2 == 0" 라는 조건식을 넣으면 결과값이 true or false가 나오므로 비교값인 Int와 Type이 다르기 때문에 컴파일 오류가 발생한다.
* switch문은 반드시 정확한 값, 또는 일정 범위(1...10)를 case에 입력해야 한다.
* 위와 같은 이유로 사용자가 만약 if문처럼 조건식을 switch에서 사용하길 원한다면, 바인딩을 통해 새로운 식별자로 값을 할당한 후 where절을 사용하여 조건식을 사용하는 것이다.