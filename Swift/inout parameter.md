# inout parameter
```swift
var num1: Int = 123
var num2: Int = 456

func swap(a: Int, b: Int) {
   // let a
   // let b
    var temp = a
    a = b          // Cannot assign to value: 'a' is a 'let' constant
    b = temp       // Cannot assign to value: 'a' is a 'let' constant
}
```
위 코드와 같이 매개변수로 들어온 두 Int의 값을 swap하는 함수를 만든다면<br/>
주석과 같이 컴파일 에러가 발생한다.

<br/>

Int자료형은 Struct(구조체)이고, 구조체는 값타입이다.<br/>
값타입은 매개변수로 전달될 때 복사되어 전달하기 때문에 함수의 스택 프레임에는 임시상수가 복사되어 저장된다.(만약 Class를 매개변수로 전달하면 참조를 저장)

<br/>

그렇기에 함수를 호출한 곳에서 매개변수로 사용된 값과 스택 프레임에 저장된 매개변수는 다른 데이터이고, 일반적으로 함수 내부의 코드에서 매개변수로 전달받은 외부의 값을 변경하는건 불가능하다.

<br/>

---

<br/>

만약 함수 내부에서 매개변수로 전달받은 데이터를 직접 수정하고 싶다면 inout parameter를 사용할 수 있다.

<br/>

함수를 선언할 때 매개변수 타입 앞에 inout키워드를 추가하면 값을 복사하여 전달하지 않고, 참조를(메모리 주소를) 전달하게 된다.

<br/>

```swift
var num1: Int = 123
var num2: Int = 456

// inout키워드를 사용하면
func swap( a: inout Int, b: inout Int) {
    
    let temp = a
    a = b
    b = temp
}

// & : 참조(메모리 주소)를 함수에게 전달하겠다는 의미
swap(a: &num1, b: &num2)

num1  // 456
num2  // 123
```

<br/>

---

<br/>

### inout parameter를 사용할 때 주의할점
* 상수(let) 또는 리터럴 값을 inout parameter로 전달할 수 없다.
* parameter의 기본값을 사용할 수 없다.
* 가변 파라미터를 사용할 수 없다.