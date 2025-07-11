# 01-05. Java 버전과 JDK / JRE

### Q. 자바 JDK와 JRE의 차이점은 무엇인가요?

먼저, `JDK`는 자바 애플리케이션을 **'개발'하기 위한 모든 도구를 포함하는 포괄적인 키트**입니다. 이름에서도 알 수 있듯이 `'Development Kit'`, 즉 개발 도구 모음이죠. 여기에는 자바 소스 코드를 바이트 코드로 변환하는 컴파일러(javac), 프로그램의 오류를 찾고 수정하는 디버거, 그리고 다양한 기능을 제공하는 자바 API 등이 포함됩니다. 개발자들은 JDK를 설치해야만 자바 코드를 작성하고, 컴파일하고, 테스트하며, 최종적으로 실행 가능한 형태로 만들 수 있습니다.

반면에 `JRE`는 자바 애플리케이션을 **'실행'하는 데 필요한 환경**입니다. `'Runtime Environment'`, 즉 실행 환경이죠. JRE 안에는 자바 코드를 읽어 실행하는 핵심 엔진인 `JVM(Java Virtual Machine)`과 자바 프로그램이 동작하는 데 필요한 표준 라이브러리인 자바 클래스 라이브러리가 포함되어 있습니다. 개발된 자바 프로그램을 최종 사용자가 자신의 컴퓨터에서 실행하기 위해서는 JRE만 설치되어 있어도 충분합니다. 즉, 소스 코드를 컴파일하거나 개발하는 기능은 없지만, 이미 컴파일된 자바 프로그램을 구동하는 데 필요한 모든 것을 제공합니다.

따라서 관계를 정리하자면, `JRE`는 `JDK`의 부분 집합이라고 할 수 있습니다. `JDK`를 설치하면 그 안에 `JRE`가 포함되어 있기 때문에 개발과 실행을 모두 할 수 있고, `JRE`만 설치하면 개발은 불가능하지만 자바 프로그램을 실행할 수 있게 됩니다. 쉽게 비유하자면, `JDK`는 요리를 하는 데 필요한 모든 도구가 갖춰진 주방이라면, `JRE`는 만들어진 요리를 먹을 수 있는 식탁과 식기류 정도라고 비유할 수 있겠습니다.



### Q. JDK의 버전들이 있던데 각 버전들의 차이점을 설명해주세요.

주로 많이 쓰는 JDK 버전은 장기지원(LTS) 버전인 8, 11, 17 세 가지 버전이 있습니다.

먼저, `JDK 1.8`, 즉 `Java 8`은 자바 역사상 가장 큰 변화 중 하나로 평가받는 버전입니다. 핵심적으로는 `람다 표현식(Lambda Expressions)`과 `스트림 API(Stream API)`가 도입되어 함수형 프로그래밍 스타일을 자바에 적용할 수 있게 되었습니다. 이로 인해 컬렉션 데이터를 다루는 코드가 훨씬 간결하고 가독성 있게 변화했습니다. 또한, 기존 `java.util.Date`와 `Calendar`의 단점을 보완한 새로운 날짜 및 시간 API인 `java.time 패키지`가 추가되어 날짜/시간 처리가 훨씬 편리하고 안정적으로 바뀌었습니다.

다음으로 `JDK 11`은 Java 8 다음의 첫 번째 LTS 버전입니다. Java 9, 10에서 도입되었던 모듈 시스템이 안정화되고, `var` 키워드를 사용한 **지역 변수 타입 추론** 기능이 공식적으로 추가되어 코드 작성 시 편의성이 향상되었습니다. 주목할 만한 변화로는 비동기 처리가 가능한 **표준 HTTP 클라이언트 API**가 정식으로 포함되어 외부 서비스와의 통신이 더욱 용이해졌다는 점입니다.

마지막으로 `JDK 17`은 JDK 11의 후속으로, 대표적으로 `instanceof 연산자`와 함께 사용되는 `패턴 매칭(Pattern Matching)` 기능이 확장되어 **조건문 내에서 타입 체크와 변수 바인딩을 동시에 처리**할 수 있게 되었습니다. 또한, `Sealed Classes (봉인된 클래스)`가 도입되어 클래스 상속 및 인터페이스 구현을 명시적으로 제한할 수 있게 되었고, 이는 코드의 안정성과 예측 가능성을 높여줍니다. `향상된 switch 표현식` 또한 가독성과 에러 방지 측면에서 개선을 가져왔습니다. 성능 측면에서는 `가비지 컬렉터(GC)`에 대한 지속적인 개선이 이루어져 전반적인 애플리케이션 처리량이 향상되었습니다.



### Q. 실제 프로젝트에서 JDK8의 람다 표현식과 스트림 API를 썻을 때 어떠한 효과가 있었나요?

보통 프로젝트는 데이터들이 서로 복합적으로 연결되어 있습니다. 클라이언트로부터 API 요청이 들어오면, 이 복잡한 데이터들을 처리하기 위해 주로 `for 루프`와 `if 조건문`을 조합해서 로직을 구현합니다. 그런데 **요구사항이 점점 복잡해져 데이터 필터링이나 가공이 필요할 때 이중, 삼중 for문이 발생하고, 조건문이 길어지면서 코드가 매우 복잡하고 길어집니다.**

여기서 문제는 나중에 추가적인 개선사항이 생겨서 코드를 수정하려고 할 때, 본인이 직접 구현한 코드여도 이해하는데 시간이 걸립니다.

이러한 문제를 해결하기 위해 `JDK 8`의 `람다 표현식`과 `스트림 API`를 도입하면, **이전의 복잡했던 코드를 훨씬 간결하고, 무엇보다 '의도를 명확하게' 드러내도록 개선할 수 있습니다.**

예를 들어, 데이터 처리 로직들을 `filter`, `collect`, `map`, `count` 등의 스트림 메소드를 활용한 파이프라인 형태로 바뀌면 데이터가 어떤 단계를 거쳐 어떻게 변환되고 최종적으로 어떤 결과를 도출하는지, 그 처리 흐름과 목적이 마치 자연어처럼 한눈에 읽을 수 있습니다.

또한, 유지보수 측면에서도 만약 새로운 필터링 조건이 추가되어야 한다면, 과거에는 기존 for 루프 안의 if 문을 수정하거나 새로운 루프를 추가해야 했습니다. 하지만 스트림 API에서는 filter 연산을 파이프라인에 하나 더 추가하는 식으로 매우 쉽게 확장할 수 있습니다.



### Q. 만약 JDK 17의 패턴매칭을 쓴다면 어떤 상황에서 쓰시겠어요?

제가 만약 프로젝트에서 JDK 17의 패턴 매칭을 활용하게 된다면, 주로 **다양한 타입의 객체를 받아서 그 타입별로 다른 로직을 처리해야 하는 상황**에서 매우 유용하게 사용할 것 같습니다. 특히, 기존의 타입 확인과 명시적인 캐스팅을 반복해야 했던 번거로움을 크게 줄여주는 점에 주목하고 있습니다.

`JDK 17` 이전에는 이런 타입 확인 상황에서 주로 `if-else if` 문을 사용하여 타입을 확인했었습니다. 가령, message가 '주문 생성' 메시지인지 확인하려면 `if (message instanceof OrderCreationMessage)` 라고 타입을 검사한 뒤, 그 다음 줄에서는 `OrderCreationMessage ocm = (OrderCreationMessage) message;` 처럼 수동으로 해당 타입으로 캐스팅을 해줘야만 비로소 그 객체의 특정 메서드를 호출할 수 있었습니다. 메시지 타입이 많아질수록 이러한 `instanceof` 확인과 불필요한 캐스팅 코드가 계속 반복되어 작성되는 불편함이 있었습니다.

여기서 패턴 매칭을 적용하면 코드가 간결해집니다. 이제는 `if (message instanceof OrderCreationMessage ocm)` 처럼 한 줄로 표현할 수 있게 되었는데요. 이렇게 작성하면 `instanceof` 조건이 `true`일 경우, `ocm`이라는 변수에 해당 메시지가 자동으로 캐스팅되어 바로 사용 가능한 상태가 됩니다. 즉, 타입 확인과 동시에 변수 선언 및 할당까지 한 번에 처리되는 것이죠.

이러한 변화는 코드의 가독성을 비약적으로 향상시켰습니다. 불필요한 반복 코드가 사라지면서 코드가 더 깔끔하고 명확해졌고, 개발 시 캐스팅을 누락하거나 잘못해서 발생할 수 있는 런타임 에러의 가능성까지 줄어들어 코드의 안정성도 높아졌습니다.
