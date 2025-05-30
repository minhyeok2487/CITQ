# 채팅 아키텍처 설계

## **Q. 만약 실시간 채팅 앱을 만든다고 했을 때, 백엔드 아키텍처는 어떻게 설계하시겠습니까?**

실시간 채팅 서비스를 만든다고 하면, 가장 먼저 고려해야 할 것은 낮은 지연율과 메모리 사용량입니다. 실시간으로 데이터를 주고받아야 하기 때문에 빠르고 효율적인 처리가 필수적입니다. 이러한 요구사항을 충족하기 위해 저는 Spring보다는 단일 스레드 기반으로 동작하는 NestJS를 선택할 것입니다.

실시간 메시지 처리는 WebSocket을 통해 구현할 것입니다. 클라이언트와 서버가 실시간으로 연결되는 구조이며, 단순한 WebSocket만 사용하는 것보다는 STOMP 프로토콜을 추가하는 것이 더 효과적입니다. STOMP 프로토콜을 사용하면 메시지 구독, 브로드캐스트 등과 같은 기능을 보다 안정적이고 체계적으로 처리할 수 있습니다.\
서버 간 메시지를 실시간으로 동기화하는 것도 중요한데, 이를 위해 메시지 브로커를 사용할 것입니다. 메시지 브로커로는 Redis Pub/Sub와 RabbitMQ 두 가지를 고려할 수 있습니다.

* Redis Pub/Sub는 설정이 간단하고 처리 속도가 빠르다는 장점이 있어 실시간성이 중요한 서비스에 적합합니다.
* 반면 RabbitMQ는 메시지를 안전하게 보관하고 복잡한 라우팅을 지원하는 데 강점이 있어 메시지 손실이 치명적인 경우 더 나은 선택이 될 수 있습니다.

보안 측면도 매우 중요합니다. 실시간 채팅 서비스인 만큼 토큰 기반 인증, 특히 JWT(Json Web Token)를 사용할 것입니다. 클라이언트가 WebSocket을 연결할 때 토큰을 함께 전달하도록 하고, 서버에서는 해당 토큰을 검증하여 인증된 사용자만 연결할 수 있도록 처리합니다. 이를 통해 효율적인 인증과 보안성을 동시에 확보할 수 있습니다.

또한, 메시지 저장 방식도 중요한 요소입니다. 실시간 채팅에서는 비정형 데이터를 다루는 경우가 많기 때문에 채팅 메시지는 MongoDB와 같은 NoSQL 데이터베이스에 저장할 것입니다. MongoDB는 유연한 스키마와 빠른 읽기/쓰기 성능을 제공하여 실시간 메시지 저장에 적합합니다. 사용자 정보나 채팅방 메타데이터처럼 관계형 데이터가 필요한 부분은 MySQL이나 PostgreSQL과 같은 RDS에 저장할 것입니다.

확장성과 장애 대응도 필수적으로 고려해야 합니다. 사용자 수가 증가할수록 단일 서버로는 감당할 수 없기 때문에 Redis 기반 세션 관리를 통해 여러 서버가 동시에 사용자의 연결 상태를 공유할 수 있도록 구성할 것입니다. 만약 AWS ECS로 배포를 한다면 ALB를 사용해서 트래픽이 많을 때 서버를 자동으로 확장할 수 있습니다.

마지막으로, 성능 최적화를 위해 메시지 저장 작업을 비동기 처리하여 실시간 메시지 전달 속도에 영향을 주지 않도록 하고, 자주 사용되는 데이터는 Redis 캐싱을 통해 빠르게 접근할 수 있도록 할 것입니다.

결론적으로, NestJS로 서버를 구성하고 WebSocket과 STOMP를 통해 실시간 연결을 구현합니다. 메시지 브로커로는 Redis Pub/Sub나 RabbitMQ를 사용하고, MongoDB와 RDS의 조합으로 데이터를 저장합니다. 보안은 JWT로 처리하며, 확장성과 안정성을 위해 AWS ECS와 Redis 세션 관리를 적용하는 방식으로 아키텍처를 설계할 것입니다.



## **Q. 왜 Spring보다 NestJS가 더 효율적이라고 생각하는지 구체적으로 알려주세요.**

Spring과 NestJS를 비교할 때, NestJS가 실시간 채팅 서비스에 더 효율적이라고 판단하는 이유는 메모리 사용량과 지연 시간 측면에서의 차이 때문입니다.

Spring은 멀티스레드 기반으로 동작하여 각 스레드마다 별도의 메모리 공간이 필요합니다. 요청이 늘어날수록 스레드 간 전환 과정에서 컨텍스트 스위칭(Context Switching)이 발생하는데, 이 과정은 CPU 자원을 소모하고 응답 지연을 유발합니다. 특히 실시간 서비스에서는 이러한 오버헤드가 성능 저하로 직결될 수 있습니다.

반면 NestJS는 싱글 스레드 기반 이벤트 루프 모델을 사용합니다. 이 모델은 하나의 스레드에서 비동기적으로 I/O 작업을 처리하므로 스레드 전환에 따른 오버헤드가 거의 발생하지 않습니다. 따라서 짧은 지연 시간과 낮은 메모리 사용량을 유지할 수 있어 실시간성이 중요한 채팅 서비스에 적합합니다.

또한 멀티스레드 환경에서는 동시성 문제가 발생할 수 있어 별도의 동기화 작업이 필요합니다. 반면 NestJS의 싱글 스레드 모델은 이러한 문제 발생 가능성을 최소화하여 안정적인 실시간 데이터 처리가 가능합니다.
