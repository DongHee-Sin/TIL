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
#### dataTask : HTTP통신을 수행하는 메서드 **(내부적으로 비동기적 처리가 되어있음)**
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

<br/>
<br/>

---

<br/>
<br/>

## 실제 네트워킹 예시
#### escaping closure를 사용하는 이유 : 비동기적으로 동작하는 네트워킹 작업이 끝난 후 필요한 작업을 하기 위해
#### 일반 return문을 사용하여 네트워킹 작업의 response를 반환하려 한다면 항상 nil이 반환된다. (네트워킹 작업이 끝나기 전에 값을 반환하기 때문)
1. 네트워킹 작업을 수행할 함수의 매개변수로 completionHandler 클로저를 받도록 작성한다.
2. 네트워킹 작업이 끝나면 매개변수로 받은 클로저를 실행한다. (클로저의 매개변수로 네트워킹 작업의 response를 전달)
3. 함수를 실행하는 곳에서 completionHandler의 기능을 정의한다. (매개변수로 들어온 네트워킹 결과 response를 사용)

<br/>

### (1) JSON파싱을 위한 타입 선언
```swift
// 서버에서 주는 데이터 ===========================================================

struct MovieData: Codable {
    let boxOfficeResult: BoxOfficeResult
}

// MARK: - BoxOfficeResult
struct BoxOfficeResult: Codable {
    let dailyBoxOfficeList: [DailyBoxOfficeList]
}

// MARK: - DailyBoxOfficeList
struct DailyBoxOfficeList: Codable {
    let rank: String
    let movieNm: String
    let audiCnt: String
    let audiAcc: String
    let openDt: String
}



// 내가 만들고 싶은 데이터 (우리가 쓰려는 Struct / Class) =======================
struct Movie {
    static var movieId: Int = 0   // 영화가 총 몇개 생성되었는지 확인
    let movieName: String
    let rank: Int
    let openDate: String
    let todayAudience: Int
    let totalAudience: Int
    
    init(movieNm: String, rank: String, openDate: String, audiCnt: String, accAudi: String) {
        self.movieName = movieNm
        self.rank = Int(rank)!
        self.openDate = openDate
        self.todayAudience = Int(audiCnt)!
        self.totalAudience = Int(accAudi)!
        Movie.movieId += 1
    }
    
}
```

<br/>

### (2) 네트워킹을 수행하는 class/struct 생성
#### 실제 프로젝트에서는 class로 사용하는 경우가 많음
* 다중 스레드 환경에서 스택이 여러개 생성되기도 하고(힙은 공유되는 영역), 구조체로 생성할 경우 인스턴스가 복사될 가능성도 있고...
```swift
// 서버와 통신하는 구조체 ===========================================================
struct MovieDataManager {
    
    let movieURL = "http://kobis.or.kr/kobisopenapi/webservice/rest/boxoffice/searchDailyBoxOfficeList.json?"
    
    let myKey = "7a526456eb8e084eb294715e006df16f"
    
    // 네트워킹하는 함수
    func fetchMovie(date: String, completion: @escaping ([Movie]?) -> Void) {
        let urlString = "\(movieURL)&key=\(myKey)&targetDt=\(date)"
        
        // 2) performRequest함수를 실행하면서, completion클로저를 정의
        // ==> 여기서는 request의 결과를 movies(매개변수)로 받아서 completion함수를 실행하도록 정의함
        // ==> performRequest함수가 네트워킹 작업을 끝낸 후 escaping closure를 실행하면 정의해둔 클로저가 실행되는것
        performRequest(with: urlString) { movies in
            // movies = [Movie](데이터) or nil
            completion(movies)
        }
    }
    
    // 1) 여기서 reqeust를 보내고, 결과를 매개변수로 사용하여 escaping closure를 실행함
    func performRequest(with urlString: String, completion: @escaping ([Movie]?) -> Void) {
        print(#function)
        
        // 1. URL 구조체 만들기
        guard let url = URL(string: urlString) else { return }
        
        // 2. URLSession 만들기 (네트워킹을 하는 객체 - 브라우저 같은 역할)
        let session = URLSession(configuration: .default)
        
        // 3. 세션에 작업 부여
        let task = session.dataTask(with: url) { (data, response, error) in
            if error != nil {
                print(error!)
                completion(nil)
                return
            }
            
            guard let safeData = data else {
                completion(nil)
                return
            }
            
            
            // 데이터 파싱하기
            if let movies = self.parseJSON(safeData) {
                //print("parse")
                completion(movies)
            } else {
                completion(nil)
            }
        }
        
        // 4.Start the task
        task.resume()   // 일시정지된 상태로 작업이 시작하기 때문
    }
    
    // JSON 데이터를 파싱해서 반환하는 함수
    func parseJSON(_ movieData: Data) -> [Movie]? {
        // 함수실행 확인 코드
        print(#function)
        
        let decoder = JSONDecoder()
        
        do {
            let decodedData: MovieData = try decoder.decode(MovieData.self, from: movieData)
            
            let dailyLists: [DailyBoxOfficeList] = decodedData.boxOfficeResult.dailyBoxOfficeList
            
            // 반복문으로 Movie타입 배열 생성 ⭐️
//            var myMovielists = [Movie]()
//
//            for movie in dailyLists {
//
//                let name = movie.movieNm
//                let rank = movie.rank
//                let openDate = movie.openDt
//                let todayAudi = movie.audiCnt
//                let accAudi = movie.audiAcc
//
//                let myMovie = Movie(movieNm: name, rank: rank, openDate: openDate, audiCnt: todayAudi, accAudi: accAudi)
//
//                myMovielists.append(myMovie)
//            }
            
            // 고차함수를 이용해 Movie타입 배열 생성하는 경우 ⭐️
            let myMovielists: [Movie] = dailyLists.map {
                Movie(movieNm: $0.movieNm, rank: $0.rank, openDate: $0.openDt, audiCnt: $0.audiCnt, accAudi: $0.audiAcc)
            }
            
            return myMovielists
            
        } catch {
            //print(error.localizedDescription)
            
            // (파싱 실패 에러)
            print("파싱 실패")
            
            return nil
        }
        
    }
    
}
```

<br/>

### (3) ViewController에서 네트워킹 인스턴스 사용
```swift
// 네트워킹 결과를 받을 빈 배열 생성
var downloadedMovies = [Movie]()

// 데이터를 다운로드 및 분석/변환하는 구조체
let movieManager = MovieDataManager()


// 실제 다운로드 코드
// completionHandler의 기능을 후행클로저로 정의 : 매개변수로 movies(네트워킹의 response)를 사용하는 코드
movieManager.fetchMovie(date: "20210201") { (movies) in
    // movies = [Movie](데이터) or nil
    if let movies = movies {
        
        // 배열 받아서 빈배열에 넣기
        downloadedMovies = movies
        dump(downloadedMovies)
        
        print("전체 영화 갯수 확인: \(Movie.movieId)")
    } else {
        print("영화데이터가 없습니다. 또는 다운로드에 실패했습니다.")
    }
}
```

<br/>
<br/>

---

<br/>
<br/>

# CRUD 네트워킹

## GET
#### 데이터 읽어오기 / 조회
```swift
func getMethod() {
    
    // URL구조체 만들기
    guard let url = URL(string: "http://dummy.restapiexample.com/api/v1/employees") else {
        print("Error: cannot create URL")
        return
    }
    
    // URL요청 생성
    var request = URLRequest(url: url)
    request.httpMethod = "GET"
    
    
    // 요청을 가지고 작업세션시작
    URLSession.shared.dataTask(with: request) { data, response, error in
        // 에러가 없어야 넘어감
        guard error == nil else {
            print("Error: error calling GET")
            print(error!)
            return
        }
        // 옵셔널 바인딩
        guard let safeData = data else {
            print("Error: Did not receive data")
            return
        }
        // HTTP 200번대 정상코드인 경우만 다음 코드로 넘어감
        guard let response = response as? HTTPURLResponse, (200 ..< 299) ~= response.statusCode else {
            print("Error: HTTP request failed")
            return
        }
            
        // 원하는 모델이 있다면, JSONDecoder로 decode코드로 구현 ⭐️
        print(String(decoding: safeData, as: UTF8.self))


    }.resume()     // 시작
}


getMethod()
```

<br>
<br/>

## POST
#### 서버에 데이터 업로드 / 생성하기
```swift
func postMethod() {
    
    guard let url = URL(string: "http://dummy.restapiexample.com/api/v1/create") else {
        print("Error: cannot create URL")
        return
    }
    
    // 업로드할 모델(형태) - 서버로 보낼 데이터 형식 (encode)
    struct UploadData: Codable {
        let name: String
        let salary: String
        let age: String
    }
    
    // 실제 업로드할 (데이터)인스턴스 생성
    let uploadDataModel = UploadData(name: "Jack", salary: "3540", age: "23")
    
    // 모델을 JSON data 형태로 변환
    guard let jsonData = try? JSONEncoder().encode(uploadDataModel) else {
        print("Error: Trying to convert model to JSON data")
        return
    }
    
    // URL요청 생성
    var request = URLRequest(url: url)
    request.httpMethod = "POST"
    request.setValue("application/json", forHTTPHeaderField: "Content-Type") // 요청타입 JSON
    request.setValue("application/json", forHTTPHeaderField: "Accept") // 응답타입 JSON
    request.httpBody = jsonData
    
    
    // 요청을 가지고 세션 작업시작
    URLSession.shared.dataTask(with: request) { data, response, error in
        // 에러가 없어야 넘어감
        guard error == nil else {
            print("Error: error calling POST")
            print(error!)
            return
        }
        // 옵셔널 바인딩
        guard let safeData = data else {
            print("Error: Did not receive data")
            return
        }
        // HTTP 200번대 정상코드인 경우만 다음 코드로 넘어감
        guard let response = response as? HTTPURLResponse, (200 ..< 299) ~= response.statusCode else {
            print("Error: HTTP request failed")
            return
        }
        
        // 원하는 모델이 있다면, JSONDecoder로 decode코드로 구현 ⭐️
        print(String(decoding: safeData, as: UTF8.self))
        
    }.resume()   // 시작
}

postMethod()
```

<br/>
<br/>

## PUT
#### 서버에 있는 데이터 업데이트하기
#### PATCH : (부분적인)업데이트
```swift
func putMethod() {
    guard let url = URL(string: "https://reqres.in/api/users/2") else {
        print("Error: cannot create URL")
        return
    }
    
    // 업로드할 모델(형태) - 서버로 보낼 데이터 형식 (encode)
    struct UploadData: Codable {
        let name: String
        let job: String
    }
    
    // 실제 업로드할 (데이터)인스턴스 생성
    let uploadDataModel = UploadData(name: "Nicole", job: "iOS Developer")
    
    // 모델을 JSON data 형태로 변환
    guard let jsonData = try? JSONEncoder().encode(uploadDataModel) else {
        print("Error: Trying to convert model to JSON data")
        return
    }
    
    // URL요청 생성
    var request = URLRequest(url: url)
    request.httpMethod = "PUT"
    request.setValue("application/json", forHTTPHeaderField: "Content-Type")
    request.httpBody = jsonData
    
    // 요청을 가지고 작업세션시작
    URLSession.shared.dataTask(with: request) { data, response, error in
        guard error == nil else {
            print("Error: error calling PUT")
            print(error!)
            return
        }
        guard let safeData = data else {
            print("Error: Did not receive data")
            return
        }
        guard let response = response as? HTTPURLResponse, (200 ..< 299) ~= response.statusCode else {
            print("Error: HTTP request failed")
            return
        }
        
        // 원하는 모델이 있다면, JSONDecoder로 decode코드로 구현 ⭐️
        print(String(decoding: safeData, as: UTF8.self))
        
    }.resume()
}

putMethod()
```

<br/>
<br/>

## DELETE
#### 서버에 있는 데이터 삭제
```swift
func deleteMethod() {
    guard let url = URL(string: "https://jsonplaceholder.typicode.com/posts/1") else {
        print("Error: cannot create URL")
        return
    }
    
    // URL요청 생성
    var request = URLRequest(url: url)
    request.httpMethod = "DELETE"
    
    // 요청을 가지고 작업세션시작
    URLSession.shared.dataTask(with: request) { data, response, error in
        guard error == nil else {
            print("Error: error calling DELETE")
            print(error!)
            return
        }
        guard let safeData = data else {
            print("Error: Did not receive data")
            return
        }
        guard let response = response as? HTTPURLResponse, (200 ..< 299) ~= response.statusCode else {
            print("Error: HTTP request failed")
            return
        }
        
        // 원하는 모델이 있다면, JSONDecoder로 decode코드로 구현 ⭐️
        print(String(decoding: safeData, as: UTF8.self))
        
    }.resume()
}

deleteMethod()
```

<br>
<br>

---

<br>
<br>

## Alamofire
### [공식문서](https://github.com/Alamofire/Alamofire)
#### 네트워킹을 쉽게 도와주는 라이브러리
#### 유지보수가 잘 되고있고(?) 많이들 사용하는 검증된 라이브러리

<br/>

### **vs URLSession**
#### URLSession은 로우레벨의 코드를 작성 가능하고, 외부 라이브러리를 사용하지 않아도 된다는 장점이 있다.
#### 하지만, 사용이 복잡하고 코드 가독성이 좋지 않아서 Alamofire라이브러리를 많이 사용함.

<br/>

#### URLSession을 한 단계 추상화한 방식이 Alamofire 라이브러리임
#### 보편적으로 많이 사용하지만, 유지보수와 유닛 테스트가 어렵다는 단점이 있다.

<br/>

### Alamofire를 한 단계 더 추상화한 Moya 라이브러리
#### [공식문서](https://github.com/Moya/Moya)
#### [우형 기술블로그](https://techblog.woowahan.com/2704/)
#### Network Layer를 템플릿화 하여 재사용성을 높이고, 테스트가 용이하며 개발자가 request, response에만 신경쓸 수 있도록 해준다.
