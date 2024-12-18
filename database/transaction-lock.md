# 트랜잭션(Transaction)과 락(Lock)

## **Q. 쇼핑몰에서 재고가 1개 남은 상품을 두 고객이 동시에 주문하는 상황이 발생하면 어떻게 처리해야 할까요?**

재고가 1개 남은 상품을 두 고객이 동시에 주문하는 상황은 데이터 정합성이 중요한 케이스로 이를 해결하기 위해서는 트랜잭션의 격리성을 보장하면서 동시성을 제어해야 합니다.\
제가 생각한 해결 방안은 비관적 롹(Pessimistic Lock)을 사용하는 것입니다. SELECT FOR UPDATE 구문을 사용하여 재고 조회 시점에 해당 레코드에 락을 걸고, 재고 확인과 주문 처리를 하나의 트랜잭션으로 묶어서 처리합니다.\
이렇게 처리하면 먼저 락을 회득한 트랜잭션이 완료될 때까지 다른 트랜잭션은 대기하게 되므로, 동시 주문에 의한 데이터 정합성 문제를 방지할 수 있습니다.\
다만, 이 방식은 동시성이 떨어질 수 있기 때문에, 대용량 트래픽이 예상되는 경우에는 Redis와 같은 인메모리 데이터베이스를 활용한 분산 락 방식을 고려해볼 수 있습니다.



## **Q. 그렇다면 트랜잭션(Transaction)은 무엇인가요?**

데이터베이스의 상태를 변환시키는 하나의 논리적 작업 단위입니다. 여러 개의 작업을 하나로 묶어 모두 성공하거나 모두 실패하도록 보장합니다. 이러한 트랜잭션은 ACID라는 4가지 주요 특성이 있습니다.

* Atomicity(원자성): 트랜잭션은 모두 실행되거나 전혀 실행되거나 전혀 실행되지 않아야 함
* Consistency(일관성): 트랜잭션 실행 전후의 데이터베이스는 일관된 상태를 유지해야함
* Isolation(격리성): 동시에 실행되는 트랜잭션들이 서로 영향을 미치지 않아야함
* Durability(지속성): 성공적으로 완료된 트랜잭션이 결과는 영구적으로 반영되어야함



## **Q. 비관적 락과 낙관적 락의 차이점은 무엇인가요?**

비관적 락과 낙관적 락은 동시성 제어를 위한 서로 다른 접근 방식입니다.\
비관적 락(Pessimistic Lock)은 데이터를 사용하기 전에 먼저 락을 걸고 시작하는 방식입니다. 이름처럼 '충돌이 발생할 것이라고 비관적으로 가정'하는 방식입니다. 아까 한 예시처럼 SELECT FOR UPDATE 구문을 사용해서 다른 트랜잭션이 해당 데이터를 수정하지 못하도록 막습니다.\
반면 낙관적 락(Optimisic Lock)은 데이터 수정 시에 충돌이 없을 것이라고 낙관적으로 가정하고 진행하는 방식입니다. 보통 버전 정보를 이용해서 구현하는데, 데이터를 읽을 때 버전도 같이 읽고, 데이터 수정 시에 버전이 여전히 같은지 확인합니다. 만약 버전이 변경되어 있다면 다른 트랜잭션이 중간에 데이터를 수정했다는 의미이므로 충돌로 판단하고 재시도하거나 에러를 반환합니다.\
실제 사용할 때는 비즈니스 상황에 맞게 선택해야 합니다. 예를 들어 충돌이 빈번하게 일어나는 재고 시스템에는 비관적 락이, 충돌이 거의 없는 게시판 같은 경우에는 낙관적 락이 적합할 수 있습니다.
