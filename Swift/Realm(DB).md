# Realm(swift) Database
Realm은 오픈소스 데이터베이스 관리 시스템(DBMS)로, 모바일에 특화된 NoSQL DB이다. <br/>
iOS에서는 SQLite와 CoreData의 대안으로 사용 가능하며, zero-copy design으로 설계되어 ORM보다 속도가 더 빠르고 raw SQLite보다 빠른 경우도 많다.<br/>
ORM이 아닌 객체 컨테이너 모델을 사용하여, 데이터 객체를 Realm에 객체 형태로 저장한다. 때문에 DB에서 가져온 데이터를 복잡한 가공과정 없이 바로 사용이 가능하다.

<br/>

> **ORM** : Object Relational Mapping (객체-관계 매핑) <br/>
> OOP의 객체와 관계형 데이터베이스의 데이터를 자동으로 매핑(연결)해주는 것을 의미한다. <br/>
> 객체와 DB의 테이블은 그 형태에서 불일치가 존재하는데, ORM을 통해 객체 간의 관계를 바탕으로 SQL을 자동 생성하여 불일치를 해결한다.

<br/>

### [zero-copy design](https://en.wikipedia.org/wiki/Zero-copy)
CPU가 한 메모리 영역에서 다른 메모리 영역으로 데이터를 복사하는 작업을 수행하지 않거나, 불필요한 데이터 복사를 피하는 것을 의미한다. <br/>
네트워크를 통해 파일을 고속으로 전달하는 등 많은 시간이 소요되는 작업에서 CPU 주기 및 메모리 대역폭을 절약하여 컴퓨터에서 실행되는 프로세스의 성능을 향상시키는데 자주 사용되는 방식이다.

<br/>

Realm Database도 zero-copy design을 사용하기 때문에 속도가 굉장히 빠르다. <br/>
Realm의 인스턴스를 생성하면 데이터베이스에 접근하여 read/write가 가능한 프록시 객체를 얻게되는데, <br/>
각 Realm의 인스턴스는 원본 데이터베이스를 바라보고 있기 때문에, 인스턴스를 통한 모든 데이터의 변경은 동일한 데이터베이스를 가르키는 모든 인스턴스에 반영된다. (클래스랑 비슷하게) 

<br/>

또한 Realm 데이터베이스의 read로 얻은 결과는 복사된 데이터가 아니라, 원본 데이터를 참조하는 값이다. <br/>
따라서 쿼리 결과(read)를 수정하면 디스크의 원본 데이터가 직접 수정된다. <br/>
또한 이 메모리 매핑은 결과가 라이브임을 의미하며, 항상 디스크의 현재 상태를 반영한다. 

<br/>
<br/>

---

<br/>
<br/>

# Realm 사용하기
[Realm Studio](https://www.mongodb.com/docs/realm/sdk/swift/) : DB 데이터를 실시간으로 확인하는 프로그램<br/>
[Realm Github](https://github.com/realm/realm-swift) 

<br/>

SPM으로 설치 후, Realm을 사용하는 파일에서 import RealmSwift

<br/>
<br/>

## 1. Scheme 설정
* 기본키에는 UUID, ObjectID 타입을 주로 사용하고, 값은 Realm에서 랜덤으로 부여해준다.
```swift
// Shopping : 테이블 이름
class Shopping: Object {
   // Persisted : 컬럼
    @Persisted var title: String         // 필수
    @Persisted var isFinish: Bool        // 필수
    @Persisted var isImportant: Bool?    // 선택 (Optional)
    
    // 컬럼(기본키) - 필수
    // UUID, ObjectID 타입을 주로 사용
    @Persisted(primaryKey: true) var objectId: ObjectId
    
    convenience init(title: String) {
        self.init()
        self.title = title
        self.isFinish = false
        self.isImportant = false
    }
}
```

<br/>
<br/>

---

<br/>
<br/>

## 2. Realm에 접근하는 상수 선언
```swift
let localRealm = try! Realm()
```

<br/>
<br/>

---

<br/>
<br/>

## 3. Create: 데이터 저장하기
```swift
func addData() {
        
        let task = Shopping(title: "제목")
        
        do {
            try localRealm.write {
                localRealm.add(task)
            }
        }
        catch {
            print("저장 실패")
        }
        
    }
```

<br/>
<br/>

---

<br/>
<br/>

## 4. Read: 데이터 가져오기 (테이블의 데이터를 모두 가져옴)
1. Realm에서 읽어온 데이터를 담을 배열 선언
   * 원하는 데이터를 필터링, 정렬하여 가져올 공간
   * Realm 테이블을 직접 수정하지 않고, 데이터만 가져와서 사용하는 구조
2. 해당 배열에 Realm의 데이터를 초기화(할당)
   * Realm에서 가져오는 데이터는 순서를 보장하지 않는다.
   * 일관적인 데이터 정렬 유지를 위해, 데이터를 가져올 때 특정 컬럼 기준으로 정렬하여 가져오는 것이 좋다.
```swift
class RealmViewController: UIViewController {

    let localRealm = try! Realm()
    
    // 1. Realm에서 읽어온 데이터를 담을 배열 선언
    var tasks: Results<Shopping>!
    
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        //2.  해당 배열에 Realm의 데이터를 초기화(할당)
        // title 기준으로 오름차순 정렬한 데이터 가져오기
        tasks = localRealm.objects(Shopping.self).sorted(byKeyPath: "title", ascending: true)
    }
    
}
```

<br/>
<br/>

### 가져온 데이터들은 배열처럼 접근 가능
```swift
extension RealmViewController: UITableViewDelegate, UITableViewDataSource {
    
    func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
        return tasks.count
    }
    
    
    func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
        let cell = tableView.dequeueReusableCell(withIdentifier: "ShoppingTableViewCell", for: indexPath) as! ShoppingTableViewCell
        
        cell.titleLabel.text = tasks[indexPath.row].title
        
        return cell
    }
}
```

<br/>
<br/>

---

<br/>
<br/>

## Realm Query

### 1. [Sort: 정렬](https://www.mongodb.com/docs/realm/sdk/swift/crud/read/#sort-query-results)
* byKeyPath : 기준이 될 컬럼
* ascending : 오름차순 여부 (Bool)
```swift
let localRealm = try! Realm()

tasks = localRealm.objects(Shopping.self).sorted(byKeyPath: "title", ascending: true)
```

<br/>
<br/>

### 2. Filter: 필터링
1. [Query문 사용](https://www.mongodb.com/docs/realm/sdk/swift/crud/filter-data/#operators-1)
2. [where문 사용](https://www.mongodb.com/docs/realm/sdk/swift/crud/filter-data/)

<br/>

필터링을 하려면 미리 가져온 테이블이 있어야 한다. (테이의 데이터를 기반으로 필터링하는 것)
```swift
// 2. where문 사용
tasks = localRealm.objects(Shopping.self).sorted(byKeyPath: "title", ascending: true)

let filtedTasks = tasks.where {
    $0.title == "치킨" && $0.isFinish == false
}
```

<br/>
<br/>

---

<br/>
<br/>

## Update: 데이터 수정하기
```swift
func updateTask() {
    
    do {
        // Realm Data Update
        try localRealm.write {
            
            // 하나의 레코드에서 특정 컬럼 데이터만 변경
            tasks[0].isFinish = false
            
            // 테이블에 있는 특정 컬럼 전체 값을 변경 (모든 레코드에 적용)
            Shopping.setValue(true, forKey: "isFinish")
            
            // 하나의 레코드에서 여러 컬럼들이 변경
            // 기본키를 통해 수정할 레코드를 찾고, 데이터를 수정하는 방식
            localRealm.create(Shopping.self, value: ["objectId": "abc123", "isFinish": false, "isImportant": false], update: .modified)
        }
        
    }
    catch {
        print("업데이트 실패")
    }
    
}
```

<br/>
<br/>

---

<br/>
<br/>

## Delete: 데이터 삭제하기
```swift
func removeMemo(at index: Int) {
    let taskToDelete = tasks[index]
    
    do {
        try localRealm.write {
            localRealm.delete(taskToDelete)
        }
    }
    catch {
        print("삭제 실패")
    }
}
```

<br/>
<br/>

---

<br/>
<br/>

## Realm Database에 옵저버 등록하기 (didSet)
[공식문서](https://www.mongodb.com/docs/realm/sdk/swift/react-to-changes/#register-a-realm-change-listener)

<br/>

### Realm 전체에 등록
* Observe realm notifications. Keep a strong reference to the notification token or the observation will stop.
* token이 메모리에서 해제되면 옵저빙도 해제된다.
```swift
let realm = try! Realm()

let token = realm.observe { notification, realm in
    // `notification`은 어떤 종류의 알림이 발생했는지 확인할 수 있는 열거형
    viewController.updateUI()
}
```

<br/>
<br/>

### 특정 테이블에 등록
* write transaction을 통해 추가, 변경, 삭제가 이루어지면 호출
* token이 메모리에서 해제되면 옵저빙도 해제된다.
```swift
class RealmViewController: UIViewController {
    
    // 토큰
    var notificationToken: NotificationToken?
    
    override func viewDidLoad() {
        super.viewDidLoad()
        
        let realm = try! Realm()
        let tableToObserve = realm.objects(Shopping.self)   // 옵저버를 등록할 테이블
        
        notificationToken = tableToObserve.observe { [weak self] (changes: RealmCollectionChange) in
            switch changes {
            case .initial:
                // Results are now populated and can be accessed without blocking the UI
            case .update(_, let deletions, let insertions, let modifications):
                // Query results have changed, so apply them to the UITableView
            case .error(let error):
                // An error occurred while opening the Realm file on the background worker thread
            }
        }
    }
}
```

<br/>
<br/>

### 특정 객체(레코드)에 등록
* 객체가 삭제되거나 내부 속성이 변경될 때 호출된다.
```swift
// Define the dog class.
class Dog: Object {
    @Persisted var name = ""
}

// 토큰
var objectNotificationToken: NotificationToken?


func objectNotificationExample() {
    // 옵저버 등록할 객체(레코드)
    let dog = Dog()
    dog.name = "Max"
    
    // Open the default realm.
    let realm = try! Realm()

    // Realm에 옵저버 등록할 객체(레코드) 등록
    try! realm.write {
        realm.add(dog)
    }

    // 토큰에 옵저버 등록
    objectNotificationToken = dog.observe { change in
        switch change {
        case .change(let object, let properties):
            for property in properties {
                print("Property '\(property.name)' of object \(object) changed to '\(property.newValue!)'")
            }
        case .error(let error):
            print("An error occurred: \(error)")
        case .deleted:
            print("The object was deleted.")
        }
    }

    // 속성이 변경되면 옵저버가 호출된다.
    try! realm.write {
        dog.name = "Wolfie"
    }
}
```