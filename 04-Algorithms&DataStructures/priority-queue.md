# 04-03. 우선순위 큐 (Priority Queue)

{% embed url="https://youtu.be/PGjqwmYG1ow" %}

### Q. 우선순위 큐(Priority Queue)를 써보셨나요? 우선순위 큐란 무엇이며, 일반적인 큐와 어떤 차이점이 있나요?

우선순위 큐를 직접 구현해서 사용해 본 경험은 없지만, 주로 **가장 중요한 데이터를 먼저 처리해야 하는 상황**에서 우선순위 큐가 효과적으로 활용된다고 알고 있습니다. 예를 들어, 운영체제의 **프로세스 스케줄링**, 네트워크 라우팅에서 **최단 경로를 찾는 알고리즘(다익스트라)**, 혹은 시뮬레이션에서 **이벤트 발생 순서를 관리**할 때 사용될 수 있습니다.

우선순위 큐는 이름 그대로 각 요소가 **우선순위**를 가지고 있으며, 큐에서 데이터를 꺼낼 때 항상 **가장 높은(또는 낮은) 우선순위를 가진 요소**가 먼저 추출되는 자료구조입니다. 반면, 일반적인 큐는 **FIFO(First In, First Out)** 원칙에 따라 먼저 들어온 요소가 먼저 나가는 선입선출 구조를 따릅니다. 따라서, 일반 큐는 데이터의 시간적 순서가 중요할 때, 우선순위 큐는 데이터의 중요도나 가중치가 중요할 때 사용됩니다.



### Q. 방금 말씀하신 상황에서, 만약 우선순위가 계속해서 변경되는 작업들이 발생한다면 어떻게 처리하시겠어요?

우선순위 큐에 이미 들어가 있는 작업의 우선순위가 동적으로 변경되는 상황이라면, 몇 가지 접근 방식을 고려할 수 있습니다.

가장 단순한 방법은 해당 작업을 우선순위 큐에서 **제거(Delete)한 후, 변경된 우선순위로 다시 삽입(Insert)** 하는 것입니다. 이때, 만약 특정 요소를 힙 내에서 직접 찾아 제거해야 한다면 O(N)의 시간 복잡도가 소요될 수 있어 비효율적입니다.

더 효율적인 방법으로는 **'인덱스 기반 힙(Indexed Heap)'** 이나 **'피보나치 힙(Fibonacci Heap)'** 과 같은 자료구조를 사용하는 것을 고려해 볼 수 있습니다. 이러한 자료구조들은 힙 내의 특정 요소 위치를 빠르게 찾을 수 있도록 **추가적인 매핑 정보**를 유지합니다. 즉, 이들은 **내부적으로 작업의 위치를 미리 기억**해 둬서, 우선순위가 바뀌었을 때도 해당 작업을 아주 빠르게 찾아서 큐의 순서를 조정할 수 있습니다. 예를 들어, 어떤 일이 갑자기 '매우 급한 일'이 되었다면, 빠르게 그 일을 맨 앞으로 옮겨주는 방식이죠. 이때 요소의 우선순위 변경 및 힙 재구성은 O(logN) 또는 피보나치 힙의 경우 O(1)의 상환 시간 복잡도로 가능합니다.

실제 시스템에서는 우선순위가 빈번하게 변경되는 작업이라면, 우선순위 큐를 사용할지, 아니면 **별도의 스케줄러가 특정 주기마다 전체 작업의 우선순위를 재평가하여 큐에 다시 넣는 방식**을 사용할지 등을 추가적으로 고려해서 처리할 것 같습니다.



### Q. 힙에 대해 얘기하셨는데, 우선순위 큐의 내부 구현으로 힙(Heap)을 가장 많이 선호하는 구체적인 이유는 무엇인가요?

힙은 우선순위 큐의 핵심 기능에 가장 잘 맞는 특징들을 가지고 있기 때문에 가장 널리 사용됩니다.

우선, 힙은 **완전 이진 트리** 형태를 유지하면서 부모-자식 간의 우선순위 규칙을 따릅니다. 이런 구조 덕분에 새로운 요소를 넣거나 가장 우선순위가 높은 요소를 꺼내는 **모든 작업이 일관되게 O(logN)이라는 효율적인 시간 복잡도로 처리**됩니다. 정렬된 연결 리스트처럼 삽입이 느려지거나, 균형 이진 탐색 트리처럼 복잡하게 구현할 필요 없이 동일한 효율을 낼 수 있다는 점이 큰 강점이죠.

게다가 힙은 주로 **배열을 기반으로 구현**됩니다. 배열은 메모리상에 연속적으로 저장되기 때문에 컴퓨터의 **캐시 메모리를 효율적으로 사용할 수 있어 실제 성능이 아주 좋습니다.** 구현도 균형 이진 탐색 트리에 비해 훨씬 간단하고, 필요한 메모리도 적은 편이에요.

결론적으로, 힙은 **빠른 연산 속도, 적은 메모리 사용, 그리고 비교적 간단한 구현**이라는 실용적인 장점들을 고루 갖추고 있어서, 우선순위 큐를 구현하는 데 가장 효율적이고 실용적인 자료구조로 선호됩니다.



### Q. 그렇다면 우선순위 큐를 구현할 때 힙(Heap)이 아닌 다른 자료구조, 예를 들어 정렬된 연결 리스트나 균형 이진 탐색 트리 같은 것을 사용한다면 어떤 장단점이 있을까요?

우선순위 큐를 만들 때 힙 말고도 정렬된 연결 리스트나 균형 이진 탐색 트리 같은 다른 자료구조를 쓸 수도 있습니다.

**정렬된 연결 리스트**로 우선순위 큐를 구현한다면, 가장 우선순위가 높은 요소를 꺼낼 때는 리스트의 맨 앞에서 바로 가져올 수 있어서 정말 빠릅니다. O(1)의 시간 복잡도죠. 하지만 새로운 요소를 추가할 때는 그 요소가 들어갈 올바른 위치를 찾기 위해 리스트를 처음부터 끝까지 다 살펴봐야 해요. 그래서 O(N)의 시간 복잡도가 발생하고, 데이터가 많아질수록 삽입이 잦은 환경에서는 효율이 아주 떨어질 수 있습니다.

**균형 이진 탐색 트리**를 사용하면 요소를 넣거나 빼는 연산 모두 O(logN)의 시간 복잡도를 보장합니다. 특정 요소를 찾거나, 가장 작거나 큰 값을 찾는 등 다양한 작업을 효율적으로 처리할 수도 있어요. 하지만 힙에 비하면 구현이 훨씬 복잡하고 어렵다는 단점이 있습니다. 또한, 각 노드가 추가적인 정보를 가지고 있어야 해서 메모리를 더 많이 쓸 수도 있고, 메모리에 연속적으로 저장되지 않아 캐시 효율성이 힙보다 떨어질 수 있다는 점도 고려해야 합니다.
