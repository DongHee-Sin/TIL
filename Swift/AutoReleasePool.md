# AutoReleasePool
**AutoReleasePool**은 ARC에 의존하지 않고 객체의 release시점을 직접 제어하기 위해 사용한다. <br/>
Objective-C를 사용하던 시절(?)에는 앱의 메모리 사용량이 과도하게 많아지는 것을 방지하기 위해 AutoReleasePool의 사용이 중요했지만, ARC를 사용하는 Swift가 등장함에 AutoReleasePool을 직접 사용하는 경우는 많지 않아졌다.

<br/>

AutoReleasePool은 RC가 감소되는 시점을 미루면서 이후에 RC가 감소되는 것을 보장받기 위한 기법이다. <br/>
AutoReleasePool 블록 내부에서 생성된 객체는 블록이 종료되는 시점에 release된다.

<br/>

---

<br/>

## AutoReleasePool 사용 전
```swift
func imageLoadTest() {
    let imagePath: String = largeImagePath
    
    for _ in 1...10 {
        
        for _ in 1...10000 {
            let image = UIImage(contentsOfFile: imagePath)!
        }
    }
}
```
위 코드는 고용량의 이미지를 다량 로드받는 메서드이다.

위와 같은 예시 코드가 있을 때, 총 10 * 10000개의 UIImage 인스턴스가 생성되고, 해당 인스턴스들은 메서드 종료 시점에 일괄적으로 메모리에서 해제된다.
<br/>

실제 고용량 이미지는 아니었지만, 테스트를 진행해보니 1.2GB까지 메모리 사용량이 올라갔다가 메서드가 종료되는 시점에 모두 해제되고 정상범주(?)로 돌아왔다.

<br/>

만약 우주 이미지처럼 고용량의 이미지를 사용하여 저런 코드를 구현한다면 앱이 중간에 다운되는 문제가 발생할 수 있다. <br/>
autoreleasepool을 사용함으로써 이런 문제를 해결해줄 수 있다.

<br/>

---

<br/>

## AutoReleasePool 사용 후
```swift
func imageLoadTest() {
    let imagePath: String = largeImagePath
    
    for _ in 1...10 {
        
        autoreleasepool {
            for _ in 1...10000 {
                let image = UIImage(contentsOfFile: imagePath)!
            }
        }
    }
}
```
autoreleasepool 코드를 추가했다. <br/>
이렇게 코드를 작성하면 autoreleasepool 블록이 종료되는 시점에 내부에서 생성된 인스턴스를 모두 release시켜주는 것 같다. 
<br/>

이렇게 작성된 코드로 테스트를 진행해보니 메모리 사용량이 최대 145mb 이상으로 올라가지 않았다. <br/>

첫번째 루프가 종료될 때마다 (= autoreleasepool 블록이 종료될 때마다) 생성되었던 UIImage 인스턴스를 release시켜주기 때문이다.

<br/>

---

<br/>

## AutoReleasePool이 ARC를 사용하는 Swift에서 필요할까?
굳이 사용하진 않을 것 같다. <br/>
일단 URL을 기반으로 Image를 로드할 때는 일반적으로 캐싱을 사용하고, 10000개 이상의 많은 이미지를 하나의 메서드에서 로드할 일은 거의 없을 것이기 때문... <br/>

혹시나 저렇게 많은 이미지를 가져온다 하더라도 뷰에 한번에 보여주지도 못할 뿐더러 저렇게 많은 이미지를 표현하기 위해서라면 tableView, collectionView를 사용하게될 것이기 때문에 한번에 로드할 이유도 없다.. <br/>

위같은 코드가 예시 코드로 가장 많이 보였는데.. 단순히 어떻게 동작하는지만 확인하면 될 것 같다. (실제로 저런 코드를 사용할 일은 없을듯..)

<br/>

Realm을 사용하여 백그라운드 스레드에서 데이터를 다량 저장할 때, 메모리에서 작업이 해제됨을 보장하기 위해 사용하기도 하는 듯 한데.. 지금 시점에서는 굳이..? 라는 생각이 든다. <br/>

그냥 백그라운드 스레드에서 저장할 데이터에 대한 처리를 한 후, 미리 만들어둔 직렬큐로 Realm 데이터를 저장하는 작업을 처리하면 되는거 아닌가..? 메인 스레드를 사용해도 되고.. Realm이 단순 읽기/쓰기 속도는 엄청 빠르다고 알고 있어서 저렇게 autoreleasepool을 사용하면서까지 백그라운드 스레드에서 모든 것을 처리하는 것은 잘 와닿지 않는다... 지금은