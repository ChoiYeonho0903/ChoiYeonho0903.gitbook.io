# Transaction

### Transaction이란?

💡 데이터베이스의 상태를 변화시키는 하나의 논리적인 단위

* 즉, 여러 개의 쿼리들을 하나로 묶는 단위

###

### ACID

#### 원자성 (Atomicity)

💡 일을 모두 수행되었거나 되지 않았거나를 보장한다.

* all or nothing
* 커밋과 롤백

#### 일관성 (Consistency)

💡 허용된 방식으로만 데이터를 변경해야 하는 것을 의미한다.

* 데이터베이스에 기록된 모든 데이터는 여러가지 조건, 규칙에 따라 유효함을 가져야 한다.

#### 격리성 (Isolation)

💡 트랜잭션 수행 시 서로 다른 트랜잭션은 독립적이다.

* 데이터베이스는 순자적으로 실행되는 것처럼 작동되어야 한다.
* 하지만 실제로 순차적으로 동작하면 성능이 안좋다.

#### 지속성 (Durability)

💡 트랜잭션이 정상 종료된 다음에는 영구적으로 데이터베이스에 작업의 결과가 저장되어야 한다.

###

### 트랜잭션 연산

#### Commit 연산

* 트랜잭션이 성공적으로 수행되었을 때 선언한다. (작업 완료)
* commit 연산이 실행된 후에야 트랜잭션의 수행 결과가 데이터베이스에 반영된다.

#### Rollback 연산

* 트랜잭션이 수행을 실패했을 때 선언한다. (작업 취소)
* rollback 연산이 실행되면 트랜잭션이 지금까지 실행한 연산의 결과를 취소하고 수행 전의 상태로 돌아간다.

###

### Transaction 상태

<figure><img src="../../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

* 활동 (Active) : 트랜잭션이 실행 중인 상태
* 부분 완료 (Partially Committed) : 트랜잭션의 마지막 연산까지 실행했지만, Commit 연산이 실행되기 직전의 상태
* 완료 (Commit) : 트랜잭션이 성공적으로 종료되어 commit 연산을 실행한 후의 상태
* 실패 (Failed) : 트랜잭션 실행에 오류가 발생해 중단된 상태
* 철회 (Aborted) : 트랜잭션이 비정상적으로 종료되어 rollback 연산을 수행한 상태

###

### Transaction 격리수준

#### (1) READ\_UNCOMMITED

#### 💡 하나의 트랜잭션이 커밋되기 전에 다른 트랜잭션이 읽을 수 있다.

#### DIRTY READ 발생

💡 다른 트랜잭션에서 처리한 작업이 완료되지 않았음에도 불구하고 다른 트랜잭션에서 볼 수 있게 되는 현상

<figure><img src="../../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

* 사용자 A의 트랜잭션이 commit 되기전에 사용자 B가 SELECT 하는 경우 사용자 A의 트랜잭션이 Rollback 되어도 사용자 B는 알지 못한다.

#### (2) READ\_COMMITED

💡 커밋이 완료된 데이터만 트랜잭션에서 조회할 수 있다.

* 다른 트랜잭션이 커밋하지 않은 정보는 읽을 수 없다.
* Undo 로그
  * UPDATE, DELETE으로 인해 데이터를 변경했을 대 변경되기 전의 데이터를 보관하는 곳이다.
    * INSERT 문장의 경우, 해당 데이터의 row id를 저장하고 이를 이용하여 물리적 메모리에 바로 접근할 수 있도록 보장한다.
  * 트랜잭션의 <mark style="color:red;">**롤백 대비용, 트랜잭션의 격리 수준을 유지**</mark>하면서 동시성을 제공하는 역할을 한다.
* 어떤 트랜잭션이 접근한 행을 다른 트랜잭션이 <mark style="color:red;">**수정할 수 있다**</mark><mark style="color:red;">.</mark>

<figure><img src="../../.gitbook/assets/image (1).png" alt=""><figcaption></figcaption></figure>

* 사용자 A 트랜잭션이 UPDATE 절을 통해 데이터를 수정하면 이전 데이터는 Undo 영역에 백업된다.
  * Undo 영역 : 격리수준과 롤백에 사용된다.
* 사용자 B 트랜잭션이 SELECT 절을 통해 데이터 조회시 employee 테이블이 아닌 Undo 영역에서 데이터를 가져온다.

#### NON-REPEATABLE READ 발생 (반복 가능하지 않은 조회)

💡 하나의 트랜잭션 내에서 동일한 SELECT 쿼리를 실행했을 때 항상 같은 결과가 다른 현상

<figure><img src="../../.gitbook/assets/image.png" alt=""><figcaption></figcaption></figure>

* 사용자 A의 UPDATE 절 전후로 사용자 B가 동일한 SELECT 절을 통해 조회했을 때 결과값이 일치하지 않는다.
  * 동일한 SELECT 쿼리를 실행했을 때 항상 같은 결과를 보장해야 한다는 ‘REPEATABLE READ’ 정합성을 위반한다.

#### (3) REPEATABLE\_READ

💡 Undo 로그를 활용하여 NON-REPEATABLE READ 해결

* MySQL의 InnoDB 스토리지 엔진에서 기본적으로 사용되는 격리 수준
* Undo 영역에 백업됩 모든 데이터에는 **변경을 발생한 트랜잭션**의 번호가 포함되어 있다.
* REPEATABLE\_READ 격리 수준에서는 실행 중인 트랜잭션보다 작은 트랜잭션에서 변경한 데이터만 볼 수 있다.

<figure><img src="../../.gitbook/assets/image (24).png" alt=""><figcaption></figcaption></figure>

* 사용자 A 트랜잭션(번호 : 12) 전후로 사용자 B (번호 : 12) 트랜잭션에서 SELECT 하는 경우
* 사용자 B 트랜잭션(번호 : 10)의 두번째 SELECT시에 12번보다 트랜잭션 번호가 작기 때문에 Undo 영역에서 데이터를 가져온다.

#### PHANTOM READ 발생

💡 트랜잭션 시작 시점 데이터를 읽었을 때 존재하지 않았던 데이터가 다시 같은 조건으로 데이터를 읽어 들였을 때 존재해 (유령처럼) 일치하지 않은 결과값을 반환하는 현상

<figure><img src="../../.gitbook/assets/image (3).png" alt=""><figcaption></figcaption></figure>

* 사용자 A가 employees 테이블에 INSERT를 실행하기 전과 후에 사용자 B가 SELECT … FOR UPDATE 쿼리로 employees 테이블을 조회했을 때의 결과
* 동일한 트랜잭션 내에서의 동일한 쿼리는 항상 같은 결과를 출력해야 하지만 다른 결과를 보여주고 있다.

#### MySQL InnoDB 스토리지 엔진에서 PHANTOM READ 해결

* InnoDB 스토리지 엔진은 레코드 락과 갭 락을 합친 **넥스트 키 락**을 사용한다.
  * 레코드 락 (Record Lock)
    * 해당 레코드의 인덱스에 락을 건다.
    * 따로 생성한 인덱스가 없는 테이블은 InnoDB가 자체적으로 생성한 클러스터 인덱스를 이용해 락을 건다.
  * 갭 락 (Gap Lock)
    * 인덱스 레코드와 인접한 앞/뒤 사이 공간에 락을 건다.
* 락을 걸어서 수정, 삽입, 삭제등의 다른 트랜잭션을 막는다.

#### (4) SERIALIZABLE

💡 트랜잭션을 순차적으로 진행시키는 것

* InnoDB 테이블에서 순수한 SELECT 작업은 아무런 레코드 Lock 없이 실행된다.
* 하지만 SERIALIZABLE 격리 수준에서는 읽기 작업도 공유 잠금을 획득해야 한다.
* 한 트랜잭션에서 읽고 쓰는 레코드를 다른 트랜잭션에서는 절대 접근할 수 없다.

###

### Transaction Lock

* 공유 Lock
  * 특정 자료에 공유 Lock을 걸면 다른 트랜잭션이 해당 자료를 갱신할 수 없다.
  * 다른 트랜잭션이 읽는 것은 가능하다.
* 배타 Lock
  * 특정 자료에 배타 Lock을 걸면 다른 트랜잭션이 자료를 갱신할 수 없고, 읽을 수도 없다.
    * 배타 Lock이 걸린 자료는 공유 Lock을 갖고 있는 트랜잭션들에 의해 읽혀질 수 없다. (Dirty Read 방지)

### Reference

[https://seunghyunson.tistory.com/12](https://seunghyunson.tistory.com/12)

[https://zzang9ha.tistory.com/381](https://zzang9ha.tistory.com/381)

[https://jeong-pro.tistory.com/241](https://jeong-pro.tistory.com/241)
