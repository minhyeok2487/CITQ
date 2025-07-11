# 05-01 Proxy Server

### Q. 프록시 서버란 무엇인가요?

`프록시 서버`는 한마디로 클라이언트하고 실제 서버 사이에 딱 중간에 위치해서, 모든 요청이랑 응답을 대신 처리해주는 서버입니다. **클라이언트가 서버에 직접 요청을 보내는 게 아니라, 프록시 서버한테 먼저 보내면 프록시 서버가 그 요청을 받아서 실제 서버에 전달해주고, 서버에서 온 응답도 다시 받아서 클라이언트한테 돌려주는 방식**이죠.

이런 중개자 역할을 통해서 얻을 수 있는 이점들이 있습니다.

우선, **익명성하고 보안을 강화**할 수 있습니다. 클라이언트의 실제 IP 주소를 외부에는 직접 노출하지 않고 프록시 서버의 IP를 통해서 통신하게 되니까, 클라이언트 입장에선 익명성이 보장되고 외부 공격으로부터 좀 더 안전해지는 거죠.

그리고 **성능 최적화나 캐싱 기능**도 있습니다. 예를 들어, 웹사이트에서 자주 요청하는 이미지나 CSS 파일 같은 것들, 이런 정적인 자원들을 프록시 서버 캐시에 미리 저장해두는 겁니다. 그러면 다음에 똑같은 요청이 들어왔을 때, 굳이 원본 서버까지 갈 필요 없이 캐시에서 바로 응답을 줄 수 있어서 네트워크 트래픽도 줄이고 응답 속도도 훨씬 빨라지는 효과를 볼 수 있습니다. 동시에 원본 서버의 부담도 줄여주고요.

`AWS` 서비스 중에서도 이런 프록시 서버 역할을 하는 것들이 있죠. 대표적으로 `Application Load Balancer`, 줄여서 `ALB`나 `CloudFront` 같은 서비스들이 제가 말씀드린 프록시 서버의 기능들을 수행하고 있습니다.



### Q. 만약 프록시 서버의 캐시 데이터랑 실제 원본 서버의 데이터가 서로 맞지 않는 '캐시 불일치' 문제가 발생하면 어떻게 관리하고 해결해야 할까요?

프록시 서버의 캐시와 원본 서버의 데이터 간에 불일치가 발생하는 문제를 해결하기 위해서는 두 가지 전략, `캐시 만료(Cache Expiration)`와 `캐시 무효화(Cache Invalidation)`를 사용할 수 있습니다.

먼저 `캐시 만료`는 가장 기본적인 방식으로, **데이터를 캐시에 저장할 때 `유효 기간(TTL, Time-To-Live)`을 설정하는 방법**입니다. 예를 들어, "이 이미지는 1시간 동안만 유효하다"라고 설정해 두면, 1시간이 지나면 캐시가 자동으로 만료되고, 이후에는 원본 서버에서 최신 데이터를 다시 가져오게 됩니다. 다만 이 방식은 TTL이 지나기 전까지는 데이터가 변경되더라도 캐시가 계속 유지되기 때문에, 그 기간 동안은 불일치 상태가 발생할 수 있다는 단점이 있습니다.

반면 `캐시 무효화`는 **원본 데이터가 변경되는 시점에 맞춰 캐시에 있는 해당 데이터를 강제로 삭제하거나 갱신하는 방식**입니다.\
이 방식에는 몇 가지 구현 방법이 있는데, 예를 들어 푸시 방식은 원본 서버에서 데이터 변경 이벤트가 발생했을 때, 프록시 서버에 이를 알리고 해당 캐시를 무효화하거나 갱신하도록 처리하는 방식입니다.\
또 다른 방법으로는 태그 기반 무효화가 있습니다. 데이터 캐싱 시 태그를 함께 저장해두고, 특정 태그에 해당하는 모든 캐시 항목을 한 번에 무효화할 수 있어 효율적인 관리가 가능합니다.

이러한 전략들을 상황에 맞게 조합해서 사용하면 캐시 불일치 문제를 효과적으로 줄일 수 있습니다.



### Q. 프록시 서버에는 크게 어떤 유형들이 있을까요?

프록시 서버는 크게 두 가지 유형으로 나눌 수 있습니다. 바로 `정방향 프록시(Forward Proxy)`와 `역방향 프록시(Reverse Proxy)`입니다. 이 둘은 위치하는 곳과 주된 목적에서 차이가 있습니다.

먼저 `정방향 프록시`는 **클라이언트 쪽에 위치하면서 클라이언트가 인터넷 상의 외부 서버에 접근할 때 중간에서 요청을 대신 처리해주는 프록시**입니다. 쉽게 말해, 클라이언트가 직접 웹사이트에 접속하는 대신 정방향 프록시 서버를 통해 접속하는 거죠.

이 `정방향 프록시`의 **주된 목적은 클라이언트의 익명성 보장과 보안 강화**에 있습니다. 클라이언트의 실제 IP 주소가 외부 웹 서버에 노출되지 않고 프록시 서버의 IP 주소만 보이게 되니까요. 또한, 사내 네트워크에서 특정 웹사이트 접속을 제어하거나, 유해 사이트를 차단하는 등의 접근 제어에도 활용됩니다.

다음으로 `역방향 프록시`는 정방향 프록시와는 반대로 **서버 쪽에 위치하면서, 외부 클라이언트의 요청을 받아 여러 내부 서버 중 하나로 전달해주는 프록시**입니다. 클라이언트는 역방향 프록시의 존재를 모른 채 그저 특정 도메인에 요청을 보냈다고만 생각하고, 실제 어떤 서버에서 응답이 왔는지는 알 수 없죠.

역방향 프록시의 주요 역할은 `로드 밸런싱(Load Balancing)`, 즉 **클라이언트 요청을 여러 백엔드 서버에 골고루 분산시켜서 특정 서버에 부하가 몰리는 것을 방지하고 서비스의 안정성과 성능을 높이는 데 있습니다.** 또한, 실제 서버들의 IP 주소를 외부에 노출하지 않기 때문에 보안을 강화하는 역할도 하고요. 보통 대규모 웹 서비스나 API 서버 앞에서 자주 사용되는 유형입니다.



### Q. 클라이언트의 IP 주소를 숨겨 익명성을 제공한다고 하셨는데, 프록시 서버를 경유했을 때 원본 서버는 클라이언트의 실제 IP 주소를 전혀 알 수 없게 되는 건가요? 만약 특정 상황에서 클라이언트의 실제 IP 주소가 필요하다면 어떻게 처리할 수 있을까요?

기본적인 `정방향 프록시(forward proxy)` 환경에서는 말씀하신 것처럼 원본 서버는 클라이언트의 실제 IP 주소를 직접 알기 어렵습니다. 하지만 특정 상황에서 클라이언트의 실제 IP가 필요하다면, **프록시 서버가 해당 정보를 HTTP 헤더에 담아서 전달하는 방식**을 사용합니다. 이때 가장 일반적으로 사용되는 헤더가 `X-Forwarded-For`입니다.

`X-Forwarded-For` 헤더는 클라이언트가 요청을 보내는 과정에서 거친 모든 프록시 서버들의 IP를 쉼표로 구분해 나열합니다. 예를 들어, `X-Forwarded-For: <Client IP>, <Proxy1 IP>, <Proxy2 IP>`\
와 같은 형태로 구성됩니다. 이 경우 가장 앞에 있는 IP 주소가 일반적으로 클라이언트의 실제 IP로 간주됩니다.

다만 주의할 점은, 이 헤더 값은 클라이언트가 임의로 조작할 수도 있다는 점입니다.\
따라서 보안이 중요한 상황에서는 신뢰할 수 있는 프록시 서버 또는 로드밸런서에서만 이 헤더를 처리하도록 제한하거나, `WAF(Web Application Firewall)` 같은 보안 솔루션과 함께 사용하는 것이 안전합니다.



### Q. 웹 서비스에서 CORS(Cross-Origin Resource Sharing) 문제를 해결하기 위해 프록시 서버를 활용하는 경우가 많습니다. 프록시 서버는 CORS 문제를 어떻게 해결해주는 건가요?

`CORS(Cross-Origin Resource Sharing)`는 웹 브라우저가 가지고 있는 보안 정책 중 하나인데요. 브라우저는 기본적으로 `'동일 출원 정책(Same-Origin Policy)'`이라는 것에 따라 다른 출원, 그러니까 다른 도메인이나 프로토콜, 또는 포트에 있는 리소스에는 직접 요청을 보내는 걸 보안상 막아버립니다.

이때 프록시 서버가 CORS 문제를 해결하는 핵심은 **브라우저의 동일 출원 정책을 우회**하는 데 있습니다.

클라이언트, 즉 브라우저에서 외부 API에 직접 요청을 보내는 대신, 자신과 동일한 출원에 있는 프록시 서버로 요청을 보냅니다.

그러면 프록시 서버는 이 요청을 받아서, 자신이 직접 실제 목표 서버로 요청을 보냅니다. 이때 프록시 서버는 브라우저가 아니기 때문에, 아무런 제약 없이 외부 API 서버와 통신할 수 있게 되는 겁니다.

실제 API 서버로부터 응답을 받은 프록시 서버는, 그 응답을 다시 클라이언트인 브라우저에게 돌려줍니다. 클라이언트 입장에서는 자신과 동일한 출원에 있는 프록시 서버로부터 응답을 받은 것이기 때문에, 브라우저는 그걸 CORS 정책 위반으로 판단하지 않고 정상적으로 데이터를 처리할 수 있게 되는 거죠.

간단히 말해서, **프록시 서버는 브라우저의 동일 출원 정책이 적용되지 않는 서버 간 통신이라는 맹점을 활용해서 클라이언트의 요청을 대신 목표 서버로 전달하고 결과를 받아오는 '대리인' 역할을 해줌으로써 CORS 문제를 해결한다고 보시면 됩니다.** 이 방법은 백엔드 API 서버에서 일일이 `Access-Control-Allow-Origin` 같은 CORS 관련 헤더를 설정하지 않아도 되는 장점도 있습니다.
