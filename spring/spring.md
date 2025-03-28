# Spring 동작 방식

## **Q. 스프링서버에 api 요청이 오면 어떻게 동작하나요?**

먼저 웹 브라우저 같은 클라이언트가 서버로 HTTP 요청을 보냅니다.

스프링 웹 애플리케이션은 `DispatcherServlet`이 가장 먼저 요청을 받습니다. 이 서블릿은 스프링 MVC의 프론트 컨트롤러 역할을 하며, 들어오는 요청을 적절한 행들러로 라우팅하는 역할을 합니다.

`DispatcherServlet`은 요청된 URL과 매핑된 컨트롤러 메서드를 찾기 위해 여러 `HandlerMapping` 인터페이스 구현체를 순차적으로 조회합니다. 이 과정에서 요청 경로, HTTP 메소드 등에 따라 어떤 컨트롤러의 어떤 메소드가 처리할지 결정됩니다.

그 뒤 `HandlerAdapter`가 실제로 요청을 처리하는 컨트롤러 메소드를 호출 후, 클라이언트로부터 받은 요청 데이터를 처리하고, `DispatcherServlet`이 생성된 결과를 클라이언트에게 반환합니다.

{% tabs %}
{% tab title="추가 질문 1" %}
### **Q. 그렇다면 HandlerMapping이 컨트롤러를 못찾으면 어떻게 되나요?**

`HandlerMapping`이 요청 URL에 매핑된 컨트롤러 메서드를 찾지 못하면, 스프링은 기본적으로 `404 Not Found` 상태 코드를 반환합니다. `DispatcherServlet`은 더 이상 처리할 핸들러가 없다고 판단하고, 예외 처리를 위해 설정된 `ExceptionResolver`로 요청을 넘기거나, 기본적으로 클라이언트에 에러 응답을 보냅니다. 예를 들어, `@ControllerAdvice`로 전역 예외 처리를 설정해 커스텀 에러 메시지를 반환할 수도 있습니다.
{% endtab %}
{% endtabs %}



## **Q. 그러면 중간에 필터(Filter)와 인터셉터(Interceptor)는 어떻게 사용하는게 좋을까요?**

필터는 서블릿 스펙에 정의된 기능으로, 주로 요청이 `DispatcherServlet`에 도달하기 전에 요청을 가로채거나 변경할 때 사용됩니다. 필터는 스프링 컨텍스트 외부에서 동작하며, 스프링 프레임워크에 의존하지 않습니다. 예를 들어 로깅/모니터링 등 요청과 응답에 대한 로그를 남기거나, 요청에 대한 인증 또는 인과와 관련된 작업을 사전 처리할 때 필터를 사용합니다.

인터셉터는 스프링 MVC가 제공하는 기능으로, `HandlerMapping`이 적절한 컨트롤러를 찾은 후 컨트롤러 메서드가 실행되기 전에, 또는 응답을 생성한 후에 특정 작업을 수행할 때 사용됩니다. 인터셉터는 스프링 컨텍스트 내에서 동작하며, 스프링 빈에 접근할 수 있습니다. 예를들어, 여러 컨트롤러 메서드에서 공통적으로 필요한 전처리 또는 후처리 로직을 구현하거나, 컨트롤러 메서드 실행 전후로 필요한 데이터 가공이나 로직을 수행할 때 인터셉터를 사용합니다.

{% tabs %}
{% tab title="추가 질문 1" %}
\##Qa
{% endtab %}
{% endtabs %}





## **Q. 그럼 스프링에서 AOP란 무엇인가요?**

`AOP`(Aspect-Oriented Programming, 관점 지향 프로그래밍) 는 애플리케이션의 핵심 비즈니스 로직과는 별개의 횡단 관심사(cross-cutting concerns)를 모듈화하여 관리할 수 있는 프로그래밍 패러다임입니다. 스프링에서 AOP는 주로 다음과 같은 개념들로 구성됩니다.

* `Aspect`: 횡단 관심사를 모듈화한 것, 즉 AOP의 주요 단위입니다.
* `JoinPoint`: 어드바이스(Advice)가 적용될 수 있는 지점, 즉 메서드 호출, 객체 생성 등입니다.
* `Advice`: 특정 JoinPoint에서 실행되는 코드, 로깅, 트랜잭션 관리 등의 구체적인 작업을 수행합니다.
* `Pointcut`: JoinPoint를 필터링하여 특정 조건에 따라 Advice를 적용할 위치를 정의합니다.

{% tabs %}
{% tab title="추가 질문 1" %}
### Q. AOP는 어떻게 구현되나요?

스프링 AOP는 기본적으로 프록시 패턴을 사용합니다. 대상 객체(빈)에 프록시를 씌어 Advice를 삽입합니다.

```java
// 예시
@Aspect
@Component
public class LoggingAspect {
    @Before("execution(* com.example.service.*.*(..))")
    public void logBefore(JoinPoint joinPoint) {
        System.out.println("메서드 실행 전: " + joinPoint.getSignature().getName());
    }
}
```


{% endtab %}
{% endtabs %}

