# Defer문
### 코드의 실행을 스코프가 종료되는 시점으로 연기하는 문법
#### 어떤 스코프 내의 동작을 마무리하기 위해 사용함
#### 함수, 반복문 등 스코프가 존재한다면 사용 가능

<br/>

#### 함수의 코드가 길어질 경우, 마지막에 수행할 기능을 미리 등록해두는 목적 (실수하지 않게)

<br/>

```swift
func deferFunction() {
    
    defer {
        print("나중에 실행하기")
    }
    
    print("먼저 실행하기")
}


deferFunction()    // 먼저 실행하기
                   // 나중에 실행하기
```

<br/>
<br/>

---

<br/>
<br/>

### defer문이 함수의 실행과정에서 한번 실행되어야, 해당 디퍼문의 실행이 예약되는 개념
#### 👉 defer문을 가장 앞에 배치시켜서 등록을 먼저 시키는게 좋을듯
#### 아래의 코드에서는 defer문이 실행되기 전 함수가 종료되기 때문에 defer문이 실행되지 않는다.
```swift
func deferFunction2() {
    
    if true {
        print("먼저 실행하기")
        return   // 함수 종료
    }
    
    defer {
        print("나중에 실행하기")
    }
}


deferFunction2()    // 먼저 실행하기
```

<br/>
<br/>

---

<br/>
<br/>

## 여러개의 defer문을 사용하는 경우
#### LIFO(스택)처럼 가장 마지막에 등록된 defer문이 먼저 실행됨
#### **일반적으로 하나의 defer문만 사용하는게 좋음**
```swift
func deferFunction3() {
    
    defer {
        print("1")
    }
    
    defer {
        print("2")
    }
    
    defer {
        print("3")
    }
}


deferFunction3()   // 3, 2, 1
```