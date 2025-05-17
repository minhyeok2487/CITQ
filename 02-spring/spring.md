# 02-01. Spring 동작 방식

## Q. 스프링 서버에 API 요청이 오면 어떻게 동작하나요? 그리고 요청을 처리할 컨트롤러를 못 찾으면 어떻게 되나요?

사용자가 브라우저에서 서버로 HTTP 요청을 보내면, 이 요청은 가장 먼저 스프링의 `DispatcherServlet`이 받습니다. `DispatcherServlet`은 마치 택배 물류 허브처럼 클라이언트로부터 수신된 요청을 분류해 어떤 컨트롤러가 처리할지 결정하고, 전체 요청 흐름을 제어하는 **프론트 컨트롤러** 역할을 수행합니다.

이후 `HandlerMapping`이 요청된 URL과 HTTP 메서드(GET, POST 등)를 기반으로 해당 요청을 처리할 컨트롤러 메서드를 탐색합니다. 적절한 컨트롤러가 결정되면, `HandlerAdapter`가 해당 메서드를 실행할 수 있도록 호출합니다.



API 서버를 개발할 때는 일반적으로 컨트롤러 클래스에 `@RestController` 애노테이션을 사용합니다. 이는 `@Controller`와 `@ResponseBody`를 결합한 형태로, 반환되는 값이 뷰 이름이 아닌 **HTTP 응답 본문 자체로 처리**되도록 해줍니다.

컨트롤러 메서드는 요청을 처리한 뒤, 보통 `ResponseEntity`를 사용하여 응답 본문뿐 아니라 **HTTP 상태 코드와 헤더 정보까지 명확하게 설정**하여 반환합니다.\
이러한 반환 객체는 `HttpMessageConverter`를 통해 JSON 등의 형식으로 자동 직렬화되어 클라이언트에게 전달됩니다.



만약 `HandlerMapping`이 요청에 해당하는 컨트롤러를 찾지 못한다면, `DispatcherServlet`은 더 이상 요청을 처리할 수 없다고 판단하고 기본적으로 **404 Not Found** 상태 코드를 응답합니다.

또한, 전역 예외 처리기를 `@ControllerAdvice`나 `@ExceptionHandler`로 등록해두었다면, 이러한 상황에서도 보다 사용자 친화적인 메시지로 에러 응답을 커스터마이징할 수 있습니다. 예를 들어 `"요청하신 페이지를 찾을 수 없습니다"`와 같은 메시지를 반환하도록 구성할 수 있습니다.



## Q. 그러면 중간에 필터(Filter)와 인터셉터(Interceptor)는 어떻게 사용하는게 좋을까요?

먼저 **필터**는 `서블릿 스펙`에 정의된 기능으로, 요청이 `DispatcherServlet`에 도달하기 전에 실행됩니다. 서블릿 컨테이너 레벨에서 동작하고 스프링 컨텍스트와는 별개로 작동하기 때문에, 스프링 빈을 직접 사용할 수는 없습니다.\
실무에서는 주로 요청/응답 로깅, CORS 처리, 인증 토큰 파싱 같은 작업을 필터에서 처리합니다. 참고로 **스프링 시큐리티도 이 필터 기반 구조 위에 구현**되어 있으며, 다수의 필터로 구성된 보안 필터 체인을 통해 인증과 인가를 처리합니다.



인터셉터는 `스프링 MVC`가 제공하는 기능으로, `HandlerMapping`이 적절한 컨트롤러를 찾은 후 컨트롤러 메서드가 실행되기 전에, 또는 응답을 생성한 후에 특정 작업을 수행할 때 사용됩니다. 인터셉터는 스프링 컨텍스트 내에서 동작하며, 스프링 빈에 접근할 수 있습니다. 예를들어, 여러 컨트롤러 메서드에서 공통적으로 필요한 전처리 또는 후처리 로직을 구현하거나, 컨트롤러 메서드 실행 전후로 필요한 데이터 가공이나 로직을 수행할 때 인터셉터를 사용합니다.



그리고 만약 컨트롤러뿐만 아니라 서비스 계층이나 DAO 계층에도 공통적인 로직을 삽입하고 싶다면, 이때는 `AOP(Aspect Oriented Programming)` 를 사용하는 게 적합합니다.

인터셉터는 요청/응답 관점에서 컨트롤러 전후에만 개입할 수 있지만, AOP는 애플리케이션의 전 계층에 걸쳐 세밀한 지점`(JoinPoint)`에 공통 로직을 삽입할 수 있다는 점에서 더 유연하게 활용됩니다.



## Q. AOP에 대해 조금 더 자세히 설명해주세요.

**AOP**는 `Aspect-Oriented Programming`, 우리말로 **관점 지향 프로그래밍**입니다.

어플리케이션에는 핵심 비즈니스 로직 외에도 공통적으로 필요한 부가 기능들이 존재합니다. 예를 들어 로깅, 트랜잭션 처리, 보안, 예외 처리 같은 기능이 그런 부분인데, 이런 걸 일일이 각 계층마다 구현하면 코드 중복도 많아지고 관리도 어려워지게 됩니다.

그래서 AOP는 이런 `횡단 관심사(cross-cutting concern)`를 분리해서, 하나의 모듈로 따로 관리할 수 있도록 도와주는 프로그래밍 패러다임입니다.

스프링에서 AOP를 사용할 땐 몇 가지 주요 개념이 있습니다.

* 먼저 `Aspect`는 공통 기능 자체를 묶어놓은 단위이고,
* `JoinPoint`는 그 기능이 삽입될 수 있는 지점, 보통은 메서드 실행 시점입니다.
* 그리고 `Advice`는 실제로 실행되는 공통 로직, 즉 `“언제 무엇을 실행할지”`에 해당하고요,
* `Pointcut`은 어떤 `JoinPoint`에 `Advice`를 적용할지를 정해주는 조건 역할을 합니다.

스프링에서 AOP는 **기본적으로 프록시 패턴으로 구현**됩니다.

어플리케이션 실행 시 스프링이 대상 객체 앞에 프록시 객체를 하나 만들어서, 메서드 호출 전이나 후에 우리가 정의한 Advice를 끼워넣는 방식으로 동작하죠.

인터페이스 기반이면 JDK 동적 프록시, 클래스 기반이면 CGLIB을 사용하는 식으로 동작 방식도 자동으로 결정됩니다.



예를 들어 어떤 서비스 메서드가 실행되기 전에 로그를 출력하고 싶다면, 다음과 같이 구현할 수 있습니다:

```java
@Aspect
@Component
public class LoggingAspect {

    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("메서드 실행 전: " + joinPoint.getSignature().getName());
    }
}
```

AOP의 장점은 단순히 전후처리만 가능한 게 아니라, 비즈니스 로직과 완전히 분리된 형태로 공통 기능을 유지·관리할 수 있다는 점입니다.

그리고 인터셉터는 컨트롤러 전후까지만 개입할 수 있지만, AOP는 서비스, 레포지토리 등 전 계층에 걸쳐 적용이 가능해서 훨씬 유연하게 사용됩니다.



## Q. AOP 예시 한 가지만 설명해주세요.

네, 대표적인 AOP 적용 예로는 **스프링의 `@Transactional` 어노테이션을 통한 트랜잭션 처리**를 들 수 있습니다.

이 기능은 내부적으로 **스프링 AOP를 기반으로 동작**하는데요,

우선 AOP에서 말하는 **Aspect**는 공통 기능을 담고 있는 단위로, 트랜잭션 처리 자체가 하나의 Aspect라고 볼 수 있습니다.

그 다음, 우리가 `@Transactional`을 붙인 메서드는 바로 **JoinPoint**, 즉 공통 기능이 삽입될 수 있는 지점이 됩니다.

스프링은 이 JoinPoint 중에서도 어떤 지점에 Advice를 적용할지 판단하기 위해 **Pointcut**이라는 조건을 사용하고요,

이 조건은 "어노테이션이 붙은 public 메서드"라든가, "특정 패키지 하위의 모든 메서드" 같은 식으로 정의됩니다.

마지막으로, 트랜잭션을 시작하고, 커밋하거나 롤백하는 **Advice**가 해당 JoinPoint 전후에 실행됩니다.

예를 들어 메서드 실행 전에 트랜잭션을 시작하고, 실행 후 결과에 따라 커밋 또는 롤백하는 식으로요.

```java
@Service
public class OrderService {

    @Transactional
    public void placeOrder(OrderRequest request) {
        orderRepository.save(request.toEntity());
        paymentService.process(request.getPaymentInfo());
        // 예외가 발생하면 자동으로 롤백
    }
}
```

위 코드에서 `placeOrder`는 JoinPoint가 되고, 트랜잭션 Aspect가 여기에 적용됩니다.

실제로 이 메서드는 프록시 객체를 통해 실행되며, Advice는 트랜잭션 시작/종료 로직을 자동으로 삽입합니다.

이처럼 `@Transactional`은 AOP의 **Aspect, JoinPoint, Pointcut, Advice** 개념이 모두 적용된 아주 전형적인 예시이고, 핵심 비즈니스 로직과는 분리된 공통 처리 로직을 깔끔하게 관리할 수 있도록 도와줍니다.



***

### 다음 내용 추천

{% content-ref url="transaction.md" %}
[transaction.md](transaction.md)
{% endcontent-ref %}

{% content-ref url="authentication-authorization.md" %}
[authentication-authorization.md](authentication-authorization.md)
{% endcontent-ref %}

