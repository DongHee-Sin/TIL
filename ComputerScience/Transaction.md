# Transaction
데이터베이스의 상태를 변화시키기 위해 수행되는 질의(Query)를 하나의 묶음으로 처리한 단위를 의미한다. <br/>
만약 중간에 실행이 중단되면 처음부터 다시 질의를 수행하는 롤백(Rollback)을 수행하고, 오류없이 실행을 마치면 Commit 하는 실행 단위이다.

<br/>

즉, Transaction은 작업 수행의 논리적 단위이며, DBMS의 성능은 초당 Transaction의 실행 수로 측정된다. (TPS: Transaction Per Second)

<br/>

### Transaction의 필요성
DB 서버에 여러 클라이언트가 동시에 접속하거나 데이터 갱신 처리 등을 하는 과정에서 중단될 수 있는 경우, 데이터 부정합을 방지하기 위한 목적으로 사용

<br/>

---

<br/>

## Transaction의 특징 (ACID)
* Atomicity, Consistency, Isolation, Durability
* DB Transaction이 안전하게 수행된다는 것을 보장하기 위한 성질을 나타내는 약어

<br/>
<br/>

### 원자성 (Atomicity)
Transaction의 작업이 부분적으로 실행되거나 중단되지 않음을 보장하는 속성이다. (All Or Nothing) <br/>
Transaction에 의해 변경된 내역을 유지하면서, 이전에 Commit된 상태를 임시 영역에 따로 저장하다가 진행중인 Transaction에 문제가 발생할 경우, 진행하던 Transaction 내역을 날리고 임시 영역에 저장했던 상태로 Rollback한다. <br/>
* 롤백 세그먼트(Rollback Segment) : 이전 Commit 데이터들이 임시로 저장되는 영역
* Save Point : Transaction 작업 내용이 많을 경우, 항상 처음부터 작업을 수행하면 비효율적일 수 있다. 따라서 Transaction의 작업 중 성공이 확실한 부분에 대해서는 Rollback이 되지 않도록 SavePoint를 지정한다.

<br/>
<br/>

### 일관성 (Consistency)
Transaction이 성공하면 일관적인 데이터베이스 상태를 유지한다. (데이터 타입의 일관성) <br/>
시스템이 가지고 있는 고정요소는 Transaction의 수행 전, 수행 후 상태가 같아야 한다. <br/>
ex) 송금 금액 Int 컬럼이 String으로 변하면 안 됨

<br/>

기본키, 외래키 등 데이터의 제약 조건을 모두 만족하는 것으로 일관성을 보장한다. 즉, 정해진 규칙에 의해서만 수정이 가능한 특성이다. <br/>
데이터 변경사항에 대한 트리거를 통해 여러 테이블에 같은 데이터가 유지될 수 있게 처리한다. (didSet처럼 값이 변경되면 관련된 데이터들의 값도 변경되도록 하는듯..?)

<br/>
<br/>

### 격리성 (Isolation)
Transaction 작업을 수행 시 다른 Transaction의 작업이 끼어들지 못하도록 보장하는 것을 말한다. <br/>
데이터를 읽거나 쓸 때는 다른 Transaction이 접근하지 못하도록 잠그고, 수행이 끝나면 잠금을 풀어서 다른 Transaction이 접근할 수 있도록 하는 방식으로, Lock & Excute Unlock을 통해 보장한다. <br/>
(참고: 동시성 프로그래밍 & 세마포어)

<br/>

Transaction에 따라 Lock의 접근 기준을 다르게 설정 가능하다. : Table Lock, Column Lock, Record Lock 등

> Exclusive Lock (배타적 잠금) : 쓰기 잠금이라고도 불리며, 어떤 Transaction에서 데이터를 변경하고자 할 때, 해당 Transaction의 작업이 완료될 때까지 해당 테이블 혹은 레코드에 대해 다른 Transaction이 접근하여 읽거나 쓰지 못하도록 막는 것 <br/>
> Shared Lock (공유 잠금) : Transaction이 데이터를 읽을 때, 다른 여러 Transaction에게 데이터 쓰기는 허용하지 않고, 데이터 읽기만 허용하는 것 <br/>
> Dead Lock : Lock, Unlock을 잘못 사용하여 모든 Transaction이 아무것도 수행할 수 없는 상태가 되는 것

<br/>
<br/>

### 지속성 (Durability)
성공적으로 완료된 Transaction의 결과는 영구적으로 반영되는 것을 의미한다. <br/>
(Commit이 되면 영원히 보장)