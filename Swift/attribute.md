# attribute symbol (@)
### 컴파일러에게 추가적인 정보를 제공하는 키워드를 의미

<br/>

### 사용되는 이유
* 선언에 추가정보 제공 : ex) @available(iOS 10.0 -> 선언(class, func 등)의 위에 작성)
* 타입에 추가정보 제공 : ex) escaping closure

<br/>
<br/>

---

<br/>
<br/>

## 선언에 사용하는 경우 (class, func)

### **@discardableResult**
* 함수의 리턴값을 버릴 수 있다는 정보를 제공 (리턴값을 사용하지 않아도 경고 메시지를 보여주지 않는다.)
* swift 5.2버전 이전에는 함수의 리턴값을 사용하지 않는다는 의미로 와일드카드 패턴을 사용했었다.
   ```swift
   // 스위프트 5.2 이전
   _ = someFunction()

   // 스위프트 5.2 이후
   @discardableResult
   func someFunction() -> Int {
      return 1
   }
   someFunction()
   ```

<br/>
      
### **@available**
* 특정 버전 이상에서만 사용이 가능하다는 의미
* **컴파일러가** API의 사용가능성을 결정
   ```swift
   @available(iOS 10.0, macOS 10.12, *)
   class SomeType { ... }

   @available(iOS 10.0, macOS 10.12, *)
   func doSomething() { ... }
   ```

* vs **#available** 키워드
   * 조건문에서 사용 (if / guard / while)
   * 버전 조건에 맞는 경우에만 조건문을 실행
   * **런타임 시점**에 API의 사용가능성을 결정
      ```swift
      if #available(iOS 11.0, *) { ... }
      ```

<br/>

### **@objc**
* 스위프트로 작성한 코드를 오브젝티브C 코드에서도 사용할 수 있게 만드는 어트리뷰트
* 프토토콜에서 선택적 요구사항을 정의할 때 사용

<br/>
<br/>

---

<br/>
<br/>

## 타입 앞에 사용하는 경우