# Result Type (Swift 5 ~ )
### 기존의 do-catch를 사용한 에러처리의 단점
* 정상적인 경우, 에러가 발생하는 경우를 나눠서 따로 처리해줘야 했다.
* throws, try와 같은 키워드가 필요했다.
* 실제 함수를 호출하는 곳에서 에러형식을 특정짓기 어렵다.
```swift
// Error 프로토콜을 채택한 열거형 정의
enum HeightError: Error {
    case maxHeight
    case minHeight
}


// throwing함수 (에러를 던질 수 있는 함수)
func checkingHeight(height: Int) throws -> Bool {
    
    if height > 190 {
        throw HeightError.maxHeight
    } else if height < 130 {
        throw HeightError.minHeight
    } else {
        if height >= 160 {
            return true
        } else {
            return false
        }
    }
}


// 에러 가능성이 있는 함수의 실행
// try 키워드를 함수 앞에 사용해야 하며, do-catch문을 통해 정상적인 실행의 경우와 에러가 발생한 경우를 각각 처리해야 한다.
do {
    let _ = try checkingHeight(height: 200)
    print("놀이기구 타는 것 가능")
} catch {
    print("놀이기구 타는 것 불가능")
}
```

<br/>

---

<br/>

### Result Type은 내부적으로 열거형으로 구성된다. (정상적인 경우, 에러가 발생하는 경우)
#### [공식문서](https://developer.apple.com/documentation/swift/result)
* 제네릭으로 연관값을 받아서 사용한다. (실제 사용할 때 구체적인 타입을 명시한 후 사용하게 됨)
* Failure은 Error프로토콜을 채택한 타입만 사용할 수 있다.
```swift
@frozen enum Result<Success, Failure> where Failure : Error {
   case success(Success)
   case failure(Failure)
}
```

<br/>

### 👍 Result Type을 사용한 에러처리 방법
* 리턴 타입이 성공/실패의 정보를 모두 담을 수 있는 Result Type(열거형)으로 리턴된다.
* Error를 직접 던지지 않고, Result 열거형으로 반환된 것을 분기처리 하기 때문에 throws, try 키워드가 불필요
* 실제 함수를 정의할 때 에러 타입을 명시적으로 선언하고, 타입캐스팅이 불필요
* 성공/실패의 경우를 깔끔하게 처리 가능

```swift
// Error 프로토콜을 채택한 열거형 정의
enum HeightError: Error {
    case maxHeight
    case minHeight
}


// 에러 발생 가능성이 있는 함수 정의
// 리턴값인 Result Type에는 성공/실패했을 경우에 대한 정보가 모두 있다.
// 타입 프로퍼티에 구체적 타입 명시 (성공: Bool, 실패: HeightError)
// 따라서 throws키워드 불필요 (에러를 던지는 과정이 필요없음)
func resultTypeCheckingHeight(height: Int) -> Result<Bool, HeightError> {
    
    if height > 190 {
        return Result.failure(.maxHeight)
    } else if height < 130 {
        return Result.failure(HeightError.minHeight)
    } else {
        if height >= 160 {
            return Result.success(true)
        } else {
            return Result.success(false)
        }
    }
}


// 리턴값을 상수(변수)로 받음
// Type: Result<Bool, HeightError>
let result = resultTypeCheckingHeight(height: 200)


// switch문을 사용한 분기처리
switch result {
case .success(let data):       // 연관값을 바인딩하여 사용하기도 좋음!
    print("결과값은 \(data)입니다.")
case .failure(let error):
    print(error)
}
```

<br/>

### Result Type에는 다양한 메서드가 내장되어 있다.
* get() : 저장된 값이 success인 경우를 반환한다. failure인 경우 Error를 던지기 때문에 do-catch, try를 같이 사용해야 한다.
* map : 성공 값을 매핑하여 새로운 결과를 반환
* mapError : 실패 값을 매핑하여 새로운 결과를 반환

<br/>
<br/>

> ### Result Type은 기존의 에러처리 패턴을 대체하려는 목적이 아니라 개발자에게 에러처리에 대한 다양한 선택지를 제공하기 위한 문법이다.

<br/>
<br/>

---

<br/>
<br/>

## ResultType with Alamofire
Alamofire를 사용한 네트워크 코드 개선

<br/>

### 기존에 사용하던 방식
completion의 매개변수로 3개의 Optional Type을 전달하기 때문에 실제 코드를 사용하는 부분에서 옵셔널 바인딩이 필요하다.
```swift
func request<T: Decodable>(type: T.Type, router: Router, completion: @escaping (T?, AFError?, Int?) -> Void) {

    AF.request(router)
        .responseDecodable(of: type) { response in
            switch response.result {
            case .success(let result): completion(result, nil, response.response?.statusCode)
            case .failure(let error): completion(nil, error, response.response?.statusCode)
        }
    }
}
```

<br/>

### ResultType 적용
성공과 실패에 대한 정보를 담은 열거형으로 반환하기 때문에 사용하는 부분에서 Switch문으로 간편하게 처리가 가능하다.
```swift
func request<T: Decodable>(type: T.Type, router: Router, completion: @escaping (Result<T, AFError>) -> Void) {
        
    AF.request(router)
        .responseDecodable(of: type) { response in
            switch response.result {
            case .success(let result): completion(.success(result))
            case .failure(let error): completion(.failure(error))
        }
    }
}
```

<br/>
<br/>

### 결과값이 없는 통신이라면?
불필요하게 괄호()가 많이 사용되어 가독성에 방해가 될 수 있다.
```swift
completion(.success(()))
```

<br/>

이를 해결하기 위해 ResultType을 확장하여 처리할 수 있다.
```swift
// Extension
extension Result where Success == Void {
    static var success: Result { .success(()) }
}

// 사용
completion(.success)
```