# 데코레이터 패턴 (Decorator pattern)

## Q. 데코레이터 패턴(Decorator Pattern)에 대해 설명해주세요.

데코레이터 패턴은 GoF 디자인 패턴 중 하나로, 객체의 기능을 확장하는 방법입니다. 이 패턴은 상속을 사용하는 대신, 객체를 감싸는 방식으로 추가 기능을 구현합니다. 즉, 기존 코드를 수정하지 않고도 새로운 기능을 동적으로 추가하거나 변경할 수 있게 해줍니다.

예를 들어, 도로의 교통량을 표시하는 클래스가 있고, 여기에 차선 표시 기능을 추가한다고 가정해봅시다. 상속을 사용하면 차선을 표시하는 클래스 외에도 다른 기능들을 추가하는 여러 파생 클래스를 만들어야 할 수 있습니다. 하지만 데코레이터 패턴을 사용하면, 객체를 감싸는 방식으로 쉽게 새로운 기능을 추가할 수 있습니다. 이를 통해 코드의 유연성과 재사용성을 높일 수 있습니다.

***

## Q. 그렇다면 데코레이터 패턴을 사용했을 때 단점은 없나요?

물론 데코레이터 패턴에도 단점이 있습니다. 작은 객체들이 많이 생성될 수 있으며, 여러 데코레이터가 겹겹이 쌓이면 시스템이 복잡해질 수 있습니다. 특히 디버깅이나 유지보수가 어려워질 수 있습니다. 데코레이터 패턴을 사용할 때는 객체가 어떻게 감싸졌는지, 어느 데코레이터가 실제로 어떤 기능을 추가하고 있는지를 파악하기가 복잡해질 수 있기 때문입니다. 이는 코드의 가독성 저하와 관리의 어려움을 초래할 수 있습니다.

***

## Q. 데코레이터 패턴과 프록시 패턴(Proxy Pattern)은 유사해 보이는데, 두 패턴의 차이점은 무엇인가요?

데코레이터 패턴과 프록시 패턴은 구조적으로 비슷하지만, 목적과 용도가 다릅니다.

* `데코레이터 패턴`은 객체의 기능을 확장하거나 수정하는 데 중점을 둡니다. 객체의 행동을 동적으로 추가하거나 변경하는 데 사용됩니다. 예를 들어, GUI 컴포넌트에 스크롤바나 테두리를 추가하는 경우 데코레이터 패턴을 사용합니다.
* `프록시 패턴`은 객체에 대한 접근을 제어하거나 대리 역할을 수행합니다. 실제 객체를 대신해 접근을 제어하거나 지연 로딩(Lazy Loading) 등 리소스 최적화를 위해 사용됩니다. 예를 들어, 데이터베이스 접근을 프록시 객체가 중간에서 가로채어 필요할 때만 데이터를 불러오는 방식으로 사용할 수 있습니다.

따라서 데코레이터는 기능 확장, 프록시는 접근 제어에 초점이 있습니다.

***

## Q. 말씀하신 프록시 패턴의 대표적인 사용 사례 중 하나인 지연 로딩에 대해 설명해주시겠어요?

프록시 패턴은 `지연 로딩(Lazy Loading)`에 자주 사용됩니다. 예를 들어, 데이터베이스에서 대량의 데이터를 불러오는 경우, 모든 데이터를 한 번에 불러오는 대신, 실제로 필요한 시점에서만 데이터를 불러오는 방식이 있습니다. 프록시 객체가 이를 관리해, 불필요한 데이터 로딩을 방지함으로써 시스템의 성능을 최적화할 수 있습니다. 이를 통해 리소스를 절약하고, 애플리케이션의 응답성을 개선할 수 있습니다. 이러한 지연 로딩은 Spring 라이브러리 중 하나인 JPA에서 많이 활용됩니다.
