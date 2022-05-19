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
    
    // 선택: 요청이 성공했는지 여부 확인 (200~299 코드)
    guard let response = response as? HTTPURLResponse, (200...299) ~= response.statusCode else {
        print("Error: HTTP request failed")
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

<br/>
<br/>

---

<br/>
<br/>

## JSON 데이터 파싱
#### response로 받은 JSON데이터를 class/struct로 변환

<br/>

### (1) response에 맞는 타입 생성
#### [JSON to Swift변환 사이트](https://app.quicktype.io)
```swift
// 서버에서 주는 데이터의 형태 ================================

struct MovieData: Codable {
    let boxOfficeResult: BoxOfficeResult
}

// BoxOfficeResult
struct BoxOfficeResult: Codable {
    let dailyBoxOfficeList: [DailyBoxOfficeList]
}

// DailyBoxOfficeList
struct DailyBoxOfficeList: Codable {
    let rank: String
    let movieNm: String
    let audiCnt: String
    let audiAcc: String
    let openDt: String
}
```

<br/>

### (2) 데이터를 변환해주는 함수 생성
#### URLSession.dataTask함수로 네트워크 요청하여 받은 response-Data를 매개변수로 사용
#### JSONDecoder타입을 사용하여 JSON데이터를 파싱
```swift
func parseJSON(_ movieData: Data) -> [DailyBoxOfficeList]? {
    
    do {
        // JSONDecoder : 자동으로 원하는 클래스/구조체 형태로 분석해주는 타입
        let decoder = JSONDecoder()
        
        // decode() 함수의 매개변수
        // 1) MovieData.self => Metatype : (변환하려는)타입의 타입
        // 2) movieData => Data : response로 받은 Data
        let decodedData: MovieData = try decoder.decode(MovieData.self, from: movieData)

        return decodedData.boxOfficeResult.dailyBoxOfficeList
        
    } catch {
        
        return nil
    }
    
}
```

<br/>
<br/>

---

<br/>
<br/>

## dataTask함수에 JSON파싱 함수 적용
#### dataTask()의 핸들러 클로저에서 사용
```swift
let task = session.dataTask(with: url) { (data, response, error) in
    if error != nil {
        print(error?.localizedDescription)
        return
    }

    guard let response = response as? HTTPURLResponse, (200...299) ~= response.statusCode else {
        print("Error: HTTP request failed")
        return
    }
    
    guard let safeData = data else {
        return
    }

    // 파싱하여 나온 결과값을 변수에 담아서 사용 (Optional Type)
    let movieArray = parseJSON1(safeData)
    dump(movieArray)    // print처럼 콘솔에 데이터를 찍지만, 좀 더 깔끔하게 보여주는 함수

}
```