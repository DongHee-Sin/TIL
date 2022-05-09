# Queue (큐)
#### FIFO으로 동작하는 자료구조로 Swift에서는 큐를 기본 자료구조로 제공하지 않는다.
#### enqueue, dequeue로 데이터를 넣고 뺄 수 있다.

<br/>

### Queue는 배열을 사용하여 아래와 같이 구현이 가능
```swift
struct Queue<T> {
    private var queue: [T] = []
    
    public var count: Int {
        return queue.count
    }
    
    public var isEmpty: Bool {
        return queue.isEmpty
    }
    
    public mutating func enqueue(_ element: T) {
        queue.append(element)
    }
    
    public mutating func dequeue() -> T? {
        return isEmpty ? nil : queue.removeFirst()
    }
}

var intQueue: Queue = Queue<Int>()

intQueue.enqueue(1)
print(intQueue.dequeue())   // Optional(1)
```

<br/>

#### 간단하게 구현이 가능하지만, dequeue를 사용할 때 배열의 removeFirst()를 사용하므로 오버헤드가 발생한다. => O(n)

<br/>

---

<br/>

### 시간복잡도 개선
#### dequeue를 수행할 때 element를 바로 삭제하는게 아니라 nil로 할당하면서 head를 조절하고, head가 미리 설정해둔 값을 넘어갈 때 nil값을 한번에 정리해주면 시간복잡도가 조금 개선된다.
```swift
struct Queue<T> {
    private var queue: [T?] = []
    private var head: Int = 0

    public var count: Int {
        return queue.count
    }

    public var isEmpty: Bool {
        return queue.isEmpty
    }

    public mutating func enqueue(_ element: T) {
        queue.append(element)
    }

    public mutating func dequeue() -> T? {
        guard head < count, let element = queue[head] else { return nil }
        queue[head] = nil
        head += 1

        if head > 10 {
            queue.removeFirst(head)  // head = 11
            head = 0
        }
        
        return element
    }
}

var intQueue: Queue = Queue<Int>()
```