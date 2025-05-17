# SecurityContextHolder

## **Q. SecurityContextHolder에 저장된 인증 정보는 어떻게 관리될까요?**

SecurityContextHolder에 저장된 인증 정보는 기본적으로 ThreadLocal 방식으로 관리됩니다. ThreadLocal을 사용하는 이유는 웹 애플리케이션의 특성상 동시에 여러 사용자가 요청을 보내게 되는데, 각 요청은 별도의 스레드에서 처리되기 때문에 ThreadLocal을 사용하면 각 스레드별로 독립적인 인증 정보를 안전하게 관리할 수 있기 때문입니다.\
인증 정보의 생명주기를 보면, 사용자 요청이 들어올 때 SecurityContextPersistenceFilter에서 SecurityContext를 로드하고 SecurityContextHolder에 저장합니다. 그리고 요청 처리가 끝나면 SecurityContextHolder를 정리하는 방식으로 동작합니다.\
추가로 SecurityContextHolder는 세 가지 전략을 제공하는데,

1. 기본값인 ThreadLocal 모드
2. 부모-자식 스레드 간 인증 정보를 공유할 수 있는 InheritableThreadLocal 모드
3. 모든 스레드가 동일한 SecurityContext를 공유하는 Global 모드가 있습니다.



## **Q. ThreadLocal을 사용하는 이유와 사용할 때의 장단점은 무엇인가요?**

ThreadLocal은 스레드 별로 독립적인 변수를 가질 수 있게 해주는 클래스입니다. 주로 사용자 인증 정보나 트랜잭션 컨텍스트 같이 스레드 별로 독립적으로 관리되어야 하는 데이터를 다룰 때 사용합니다.\
예를 들어 웹 애플리케이션에서 동시에 여러 사용자가 요청을 보낼 때, 각 요청은 별도의 스레드에서 처리됩니다. A라는 사용자와 B라는 사용자가 동시에 요청을 보냈을 때, 각각의 인증 정보가 섞이지 않고 독립적으로 유지되어야 하는데, ThreadLocal을 사용하면 synchronized 같은 명시적인 동기화 없이도 이런 동시성 문제를 해결 할 수 있습니다.\
반면 스레드 풀 환경에서는 ThreadLocal 변수를 제거해주지 않으면 메모리 누수가 발생할 수 있습니다. 스레드가 재사용될 때 이전 데이터가 남아있을 수 있기 때문입니다.



## **Q. JWT 인증 필터와 SecurityContextHolder는 어떻게 상호작용하나요?**

JWT 인증 필터와 SecurityContextHolder의 상호작용은 크게 인증 정보 생성, 저장, 활용 단계로 나눌 수 있습니다.\
먼저 HTTP 요청 헤더에서 JWT 토큰을 추출합니다. 보통 Authorization 헤더에 "Bearer" 접두사와 함께 토큰이 전달되는데, 이를 파싱해서 실제 JWT 토큰 값을 가져옵니다.\
그 다음, 추출한 토큰이 유효한지 검증하는 과정을 거칩니다. 토큰의 서명을 확인하고, 만료 시간 등을 체크해서 토큰이 유효하다고 판단되면, 토큰에서 사용자 정보를 추출합니다.\
추출된 사용자 정보를 바탕으로 객체가 생성되고 SecurityContexyHolder에 저장되는데, 이는 ThreadLocal을 사용하기 때문에 해당 요청을 처리하는 스레드 내에서 어디서든 안전하게 접근 할 수 있습니다.\
실제로 이 인증 정보는 컨트롤러나 서비스 계층에서 SecurityContextHolder.getContext().getAuthentication()을 통해 쉽게 조회할 수 있습니다.
