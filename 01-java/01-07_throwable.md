# 01-07. 예외(Throwable)

## **Q. 자바의 Throwable 클래스에 대해서 설명해주세요.**

`Throwable` 클래스는 `Java`에서 예외처리를 위한 최상위 클래스입니다. 이 클래스는 크게 `Error`와 `Exception`이라는 두 가지 하위 클래스로 나뉩니다.

먼저 `Error`는 **시스템 레벨에서 발생하는 심각한 문제**를 나타냅니다. 예를 들어 `OutOfMemoryError`나 `StackOverflowError` 같은 것들인데, 이러한 에러들은 애플리케이션 코드에서 처리하기 어렵고, 보통 복구가 불가능한 상황을 의미합니다.

\
반면 `Exception`은 **프로그램 실행 중에 발생할 수 있는 예외적인 상황**을 나타내며, 적절한 처리가 가능합니다. 이러한 Exception은 다시 두 가지로 구분됩니다.

첫 번째는 `Checked Exception`입니다. 이는 `RuntimeException`을 제외한 `Exception`의 하위 클래스들을 말하는데, **컴파일 시점에서 예외 처리를 강제**합니다. `IOException`이나 `SQLException` 같은 예외들이 여기에 속합니다. 이러한 예외들은 반드시 `try-catch`로 처리하거나 `throws` 키워드로 예외를 선언해야 합니다.

두 번째는 `Unchecked Exception`으로, `RuntimeException`과 그 하위 클래스들을 말합니다. `NullPointerException`이나 `ArrayIndexOutOfBoundsException` 같은 예외들이 대표적이죠. 이러한 예외들은 컴파일러가 예외 처리를 강제하지 않습니다. **주로 프로그래밍 오류**를 나타내며, 미리 방지하는 것이 좋습니다.&#x20;

실제 개발할 때는 `Checked Exception`은 비즈니스 로직상 발생할 수 있는 예외 상황에 사용하고, `Unchecked Exception`은 프로그래밍 오류와 같이 예방이 가능한 상황에 사용합니다. 그리고 `Error`는 시스템 레벨의 문제이므로 애플리케이션 코드에서 직접 처리하지 않는 것이 일반적입니다.



## **Q. try-catch와 try-with-resource는 어떠한 차이점이 있나요?**

`try-catch`와 `try-with-resource`의 **가장 큰 차이점은 자원 관리 방식**에 있습니다.

기존의 `try-catch` 구문은 **자원을 수동으로 닫아주어야 했습니다**. 예를 들어 파일을 읽거나 데이터베이스 연결을 할 때, `finally` 블록에서 직접 `close()` 메소드를 호출해야 했습니다. 이 방식은 코드가 길어지고, 실수로 자원을 닫지 않을 수 있는 위험이 있었습니다.

반면, `Java 7`에서 도입된 `try-with-resources`는 `AutoCloseable` 인터페이스를 구현한 **자원을 자동으로 닫아줍니다**. `try` 구문의 괄호 안에서 자원을 선언하면, `try` 블록이 끝날 때 자동으로 `close()` 메소드가 호출됩니다.

추가로, `try-with-resources`는 **자원을 닫을 때 발생하는 예외도 더 잘 처리합니다**. 만약 `try` 블록에서 예외가 발생하고, `close()` 메소드에서도 예외가 발생한다면, `try` 블록의 예외가 기본적으로 전달되고 `close()`의 예외는 `suppressed` 예외로 처리됩니다. 이러한 에외들은 `getSuppressed()` 메소드로 확인 할 수 있습니다.



## **Q. 아래 코드의 문제점은 무엇인가요?**

```java
static class CheckedRunnable implements Runnable {  
    @Override  
    public void run() throws InterruptedException {  
        Thread.sleep(3000); 
    }  
}

@FunctionalInterface  
public interface Runnable {  
   void run();  
}
```

핵심적인 문제는 `Runnable` 인터페이스의 `run()` 메서드를 재정의할 때 `InterruptedException`과 같은 체크 예외를 `throws` 하는 부분입니다.

`Java`의 메서드 재정의 규칙에 따르면, **부모 메서드(이 경우 Runnable 인터페이스의 run())가 어떤 체크 예외도 던지지 않는 경우, 자식 메서드 또한 체크 예외를 던질 수 없습니다.**

따라서 이 코드에서는 `run()` 메서드 내부에서 `InterruptedException`을 직접 처리해야 합니다. 구체적으로는 `try-catch` 블록을 사용하여 예외를 적절히 처리하거나, 런타임 예외로 변환해야 합니다.

예를 들어, 다음과 같이 수정할 수 있습니다:

```java
static class CheckedRunnable implements Runnable {  
    @Override  
    public void run() {  
        try {
            Thread.sleep(3000);
        } catch (InterruptedException e) {
            // 예외 처리 또는 런타임 예외로 변환
            Thread.currentThread().interrupt();
        }
    }  
}
```

이렇게 하면 체크 예외 처리 규칙을 준수하면서, 예외 상황에 대해 안전하게 대응할 수 있습니다.

이 접근 방식은 개발자가 예외 상황을 명시적으로 처리하도록 강제함으로써, 예기치 못한 프로그램 종료를 방지하고 더 견고한 코드를 작성할 수 있게 해줍니다.
