# 소켓(Socket)

## **Q. 소켓(Socket)이 뭔가요?**

소켓은 전송계층과 응용계층 사이에 있는 인터페이스로 마치 전화 통화에서 수화기가 두 사람을 연결해주는 것처럼, 네트워크 상에서 두 장치 간의 데이터 흐름을 가능하게 합니다. 응용계층에 속하는 프로세스들은 데이터 송수신을 위해 반드시 소켓을 거쳐 전송계층으로 데이터를 전달해야합니다.&#x20;

소켓의 구성요소로는 프로토콜, IP 주소, 포트번호가 있습니다.\
프로토콜은 데이터 전송을 위한 표준 집합 규칙으로 주로 TCP/IP, UDP/IP 프로토콜을 사용합니다.\
IP 주소는 서버 장비 또는 네트워크 장비를 주소로 표현한 것으로, 호스트마다 하나씩 가지고 있습니다.\
포트번호는 통신을 사용하는 애플리케이션을 식별하는 번호로 약 6만개의 포트번호가 있습니다.



## **Q. 소켓통신과 Http통신의 차이점은 무엇인가요?**

소켓 통신과 HTTP 통신의 차이점은 크게 연결방식과 프로토콜로 나눌 수 있습니다.&#x20;

첫번째, 연결방식에서 소켓 통신은 지속적인 연결을 유지합니다. \
클라이언트와 서버가 한번 연결되면, 그 연결을 통해 계속해서 데이터를 주고받을 수 있습니다. 그래서 실시간 양방향 통신이 필요한 게임 서버나 채팅 프로그램에 많이 쓰입니다. \
반면 HTTP 통신은 비연결형으로 요청할 때만 잠시 연결이 이루어지고, 서버가 응답을 보내면 바로 연결이 종료됩니다. 주로 웹 브라우저와 웹 서버가 데이터를 주고받을 때 사용됩니다.&#x20;

두번째, 프로토콜에선 소켓 통신은 TCP나 UDP 같은 전송 게층 프로토콜을 사용합니다. \
TCP는 신뢰성을 보장하는 연결 지향형 프로토콜이고, UDP는 빠른 전송을 우선시하는 비연결형 프로토콜입니다. \
반면, HTTP 통신은 HTTP라는 애플리케이션 계층의 프로토콜을 사용합니다.&#x20;

정리하자면, 소켓 통신은 실시간으로 데이터를 주고받아야 할 때, HTTP 통신은 간단한 요청-응답 모델이 필요할 때 사용합니다.
