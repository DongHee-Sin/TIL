# Networking
## iOS에서 서버와 통신

<br/>

### 코드 레벨에서 개발자가 처리할 단계
#### (1) 서버에 데이터 요청 (request)
#### (2) 데이터를 JSON 형식으로 받음 (response)
#### (3) JSON 데이터를 Class/Struct로 변환하여 사용

<br/>

---

<br/>

## 데이터 요청의 4단계 (request)
1. URL 주소 (문자열)
2. URLSession객체 생성
3. dataTask : URL, 요청사항 등 입력
4. resume(시작)

<br/>

### (1) URL문자열로 URL 생성
#### URL타입으로 변환할 때 실패할 가능성이 있어서 OptionalType으로 생성됨
```swift
// 1. URL주소 - 문자열
let strURL: String = "http://kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json?&key=\(key)&targetDt=20210201"

// 2. URL 구조체 생성
let url = URL(string: strURL)!
```

<br/>

### (2) URLSession인스턴스 생성
#### URLSession : 네트워킹을 하는 객체 - 브라우저같은 역할
```swift
// 1. 싱글톤으로 생성
let session = URLSession.shared

// 2. 생성자로 생성
let session2 = URLSession(configuration: .default)
```

<br/>

### (3) Session에 (일시정지 상태로) 작업 부여
#### dataTask : HTTP통신을 수행하는 메서드
```swift
// 매개변수 : 1) URL / 2) 컴플리션 핸들러(클로저)
// url주소로 네트워크 통신을 수행한 후 결과를 핸들러의 매개변수(Optional)로 준다.
let task = session.dataTask(with: url) { (data, response, error) in
    // 데이터를 어떻게 처리할지 정의

    // 에러 확인
    if error != nil {
        print(error?.localizedDescription)
        return
    }
    
    // 전달받은 data를 원하는 형태로 변환해서 사용해야 함 (class or struct)
    guard let safeData = data else {
        return
    }

    // convert Data to String (그냥 테스트)
    print(String(decoding: safeData, as: UTF8.self))
}
```

<br/>

### (4) 작업시작
```swift
task.resume()   // 일시정지된 상태로 작업이 시작하기 때문
```

<br/>
<br/>

### => dataTask를 부여한 후 바로 실행도 가능
```swift
session.dataTask(with: url) { (data, response, error) in
    if error != nil {
        print(error?.localizedDescription)
        return
    }

    guard let safeData = data else {
        return
    }
}.resume()
```