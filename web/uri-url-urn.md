# URI, URL, URN

## **Q. URI, URL, URN의 차이점에 대해서 설명해주세요.**

URI는 Uniform Resource Identifier의 약자로 인터넷에서 자원을 식별하기 위한 문자열입니다.\
쉽게 말해, 어떤 리소스를 가리키는 "이름포"라고 볼 수 있습니다. URI는 URL과 URN을 포함하는 상위 개념입니다.\
URL은 Uniform Resource Locator의 약자로 우리가 흔히 알고 있는 웹 주소를 말합니다. 특정 자원이 어디에 있는지를 나타내며, 그 자원에 접근하는 방법(예: HTTP, FTP 등)도 포함합니다. 예를 들어\
`https://www.loatodo.com/todo/resource`\
이 URL은 `www.loatodo.com` 서버의 /todo/resource 경로에 있는 자원에 HTTPS 프토토콜을 이용해 접근한다는 것을 의미합니다.\
반면에, Uniform Resource Name인 URN은 자원의 위치와 상관없이 자체적으로 유일한 이름을 부여하는 방식입니다. 즉, 특정 리소스가 어디에 있든 변하지 않는 고유한 식별자입니다. 예를 들어,\
`urn:isbn:978-3-16-148410-0`\
이 URN은 특정 책의 ISBN을 나타내는 것으로, 책의 실제 위치(온라인 서점이든 도서관이든)와 관계없이 항상 같은 값을 유지합니다.



## **Q. URL에는 쿼리 문자열이 붙을 수 있는데, 서로 다른 쿼리 문자열이 붙은 두 URL은 같은 URL일까요?**

두 URL, `https://loatodo.com/todo?id=123`과 `https://loatodo.com/todo?id=111`은 같은 기본 경로(`https://loatodo.com/todo`)를 가지지만, 서로 다른 쿼리 문자열(`id=123`, `id=111`)을 포함하고 있습니다.

URL(Uniform Resource Locator)은 자원의 위치를 나타내므로, 쿼리 문자열이 달라지면 서로 다른 URL로 간주됩니다. 또한, URI(Uniform Resource Identifier)의 일부로서 쿼리 문자열은 자원을 구별하는 역할을 하므로, 쿼리 문자열이 다르면 URI도 달라지며, 결국 URL도 달라진다고 볼 수 있습니다.

다만, 문맥에 따라 해석이 달라질 수 있습니다.\
웹 브라우저나 서버 입장에서는 쿼리 문자열이 다르면 서로 다른 리소스를 요청하는 것으로 인식될 수 있지만, 예를 들어 `?sort=price`처럼 정렬 방식만 변경하는 경우, 이는 동일한 리소스의 다른 표현일 뿐, 본질적으로 다른 리소스를 요청하는 것은 아닙니다. 따라서, 특정한 맥락에서는 쿼리 문자열이 다르더라도 동일한 리소스로 볼 수도 있습니다.
