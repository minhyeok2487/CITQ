# 우선순위 큐 (Priority Queue)

## Q. 우선순위 큐(Priority Queue)란 무엇이며, 일반적인 큐와 어떤 차이점이 있나요?

우선순위 큐는 각 요소가 우선순위를 가지고 있으며, 큐에서 가장 높은 우선순위를 가진 요소가 먼저 제거되는 자료구조입니다. 일반적인 큐에서는 FIFO(First In, First Out) 원칙에 따라 요소가 처리 되지만, 우선순위 큐에서는 우선순위가 높은 요소가 먼저 처리됩니다.

***

## Q. 우선순위 큐를 구현하는 방법에는 어떤 것들이 있으며, 각각의 시간 복잡도는 어떻게 되나요?

우선순위 큐는 배열, 연결 리스트, 힙(Heap) 등을 사용해 구현할 수 있습니다. 배열을 사용하면 삽입은 O(1) 이지만, 삭제는 O(n)입니다. 정렬된 연결 리스트를 사용하면 삽입과 삭제가 모두 O(n)입니다. 가장 효율적인 방법은 힙을 사용하는 것이며, 이 경우 삽입과 삭제가 모두 O(log n)의 시간 복잡도를 가집니다.

***

## Q. 우선순위 큐에서 힙을 사용할 때, 최소 힙(Min-Heap)과 최대 힙(Max-Heap)의 차이점은 무엇인가요?

최소 힙에서는 부모 노드가 항상 자식 노드보다 작은 값을 가지며, 루트 노드가 가장 작은 값을 가집니다. 반대로 최대 힙에서는 부모 노드가 자식 노드보다 큰 값을 가지며, 루트 노드가 가장 큰 값을 가집니다. 우선순위 큐에서 최소 힙은 최소 값을 빠르게 추출할 때, 최대 힙은 최대 값을 빠르게 추출할 때 사용됩니다.

***

## Q. 우선순위 큐는 다익스트라 알고리즘 같은 그래프 알고리즘에서 어떻게 활용되나요?

다익스트라 알고리즘에서는 최단 경로를 찾기 위해 우선순위 큐를 사용합니다. 시작점에서 다른 모든 정점까지의 거리를 게산할 때, 우선순위 큐를 통해 현재까지 발견된 가장 짧은 경로를 가진 정점을 먼저 처리할 수 있습니다. 이를 통해 효율적으로 최단 경로를 계산할 수 있으며, 우선순위 큐의 힙을 사용하면 시간 복잡도를 O (n log n)으로 줄일 수 있습니다.
