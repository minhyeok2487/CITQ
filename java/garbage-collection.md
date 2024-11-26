# Garbage Collection

## **Q. Garbage Collection에 대해 아시는데로 말씀해주세요.**

프로그래밍에서 객체나 변수를 생성하면, 이들은 메모리를 점유하게 됩니다. 하지만 더 이상 사용되지 않거나 참조되지 않는 객체가 남아 있다면, 그들이 차지하는 메모리는 비효율적으로 낭비됩니다. 이러한 메모리 누수를 방지하기 위해 Garbage Collection이 필요합니다.

Garbage Collection은 프로그래밍에서 더 이상 사용되지 않는 메모리를 자동으로 회수하는 메모리 관리 기법입니다. 주요 방식에는 참조가 0인 객체를 회수하는 참조 카운팅(Reference Counting), 사용 중인 객체를 마크하고 나머지를 회수하는 마크 앤 스위프(Mark and Sweep), 활성 객체를 다른 메모리 영역으로 복사하는 복사 수집(Copying Collection)이 있습니다. 이를 통해 메모리 누수를 방지하고 메모리 관리를 자동화하여 개발자의 부담을 줄여줍니다.



## **Q. 마크 앤 스위프에 대해 조금 더 자세히 설명해주세요.**

마크 앤 스위프는 마크(Mark) 단계와 스위프(Sweep) 단계가 있습니다.

Mark 단계는 프로그램이 사용하는 모든 객체를 탐색하고, 사용 중인 객체에 "마크"를 표시합니다. 이 과정에서 참조 그래프를 따라가며 접근 가능한 객체들을 식별합니다.

Sweep 단계는 마크되지 않은 객체는 더 이상 참조되지 않거나 사용되지 않는 것으로 간주됩니다. 이 객체들의 메모리를 해제하여, 프로그램이 다른 목적으로 사용할 수 있도록 합니다.

이 방식의 장점은 순환 참조 문제를 해결할 수 있다는 것입니다. 왜냐하면 사용 중인 객체만을 식별하여 회수 대상에서 제외하기 때문입니다. 그러나 Mark 단계에서 모든 객체를 탐색해야 하므로 성능에 영향을 줄 수 있습니다.



## **Q. 그렇다면 자바에서는 어떻게 동작하나요?**

자바(Java)에서는 Garbage Collection이 자동으로 수행되며, 메모리 관리의 핵심 요소로 작동합니다. 자바 프로그램에서 객체를 생성하면, 그 객체는 힙(Heap) 메모리에 저장되며, 더 이상 사용되지 않거나 참조되지 않는 객체는 Garbage Collector에 의해 자동으로 회수됩니다.

자바의 가비지 콜렉션 종류 중 Major GC, Full GC의 경우 앞서 설명한 마크 앤 스위프 방식으로 메모리에서 사용 중인 객체를 탐색하여 마크한 후, 사용되지 않는 객체를 회수합니다.



## **Q. 힙 메모리는 어떻게 구성되어 있나요?**

힙 메모리는 Young Generation, Old Generation, Permanent Generation으로 나눕니다.

* Young Generation: 새로 생성된 객체가 할당되며, 주로 짧은 생명 주기를 가집니다. 여기에서 살아남은 객체는 Old Generation으로 이동합니다.
* Old Generation: Young Generation에서 살아남은 객체들이 오래 사용되는 객체로 간주되며, 여기로 이동합니다.
* Permanent Generation: 클래스와 메서드 메타데이터가 저장됩니다(현재는 메타스페이스로 대체됨)



## Q. Java의 Minor GC와 Major GC 무엇인가요?

Java의 모든 객체는 처음에는 Young Generation에 생성되게 됩니다.&#x20;

Young Generation의 공간은 Old Generation에 비해 상대적을 작기 때문에 메모리 상의 객체를 찾아 제거하는 데 적은 시간이 걸리고, 여기서 발생되는 GC를 Minor GC라고 불립니다.&#x20;

그다음 오래 살아남은 객체는 Old Generation으로 이동되며, 이 공간이 가득차면 발생되는 GC가 Major GC입니다. \
앞서 설명대로 Old Generation은 Young Generation에 비해 상대적으로 큰 공간을 가지고 있어, 이 공간에서 GC는 Thread가 멈추고 Mark and Sweep 작업으로 CPU에 부하를 주기 때문에, 프로그램이 멈추는 'Stop-the-World' 현상이 발생할 수 있습니다.&#x20;

Java는 이러한 문제를 해결하기 위해 여러 가비지 컬렉션 방식을 개발하였습니다.



## Q. 자바의 가비지 콜렉션 방식은 무엇이 있나요?

첫번째 `Serial GC`는 서버의 CPU 코어가 1개일 때 사용하기 위해 개발된 GC로 GC를 처리하는 쓰레드가 1개이기 때문에 가장 Stop-the-World 시간이 깁니다.&#x20;

두번째 `Parallel GC`는 Java 8 버전의 디폴트 GC로 Serial GC와 기본적인 알고리즘은 같지만, Young 영역의 Minor GC를 멀티 쓰레드로 수행하게 되어 Serial GC에 비해 Stop-the-World 시간을 감소하였습니다.&#x20;

세번째 `Parallel Old GC`는 앞서 Parallel GC에서 Old 영역에서도 멀티 쓰레드로 GC를 수행하도록 개선한 버전입니다. \
새로운 가비지 컬렉션 청소 방식은 `Mark-Summary-Compact` 방식을 사용하였습니다.&#x20;

네번째 `G1 GC(Garbage First)`는 Java 9+ 버전의 디폴트 GC로, 기존의 GC 알고리즘에서는 고정된 Heap 영역을 나누었지만 G1 GC는 Region이라는 영역으로 체스같이 분할하여 상황에 따라 동적으로 역할을 부여하는 새로운 방식을 사용했습니다. Garbage로 가득찬 영역을 빠르게 회수하여 빈 공간을 확보하므로, 결국 GC 빈도가 줄어드는 효과를 얻게 되는 원리입니다.\
&#x20;이러한 Region의 개념으로 `Shenandoah GC`, `ZGC`등이 계속해서 개발되었습니다.
