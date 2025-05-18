# API
## API란 무엇인가?
API는 Application Programming Interface의 줄임말로, 두 소프트웨어가 **서로 정보를 주고받을 수 있게 해주는 통신 규칙(인터페이스)입니다.

## API는 어떻게 작동하는가?
- 요청을 보내는 쪽: 클라이언트
- 요청을 처리하고 응답을 주는 쪽: 서버

## API는 방식에 따라 4가지로 나눌 수 있다.
### SOAP API(Simple Object Access Protocol)
보안성과 정형화된 통신이 중요한 시스템에서 사용한다.
- XML 형식으로 데이터를 주고받는다. (딱딱하고 규칙이 엄격함)
- 장점: 규칙이 명확하고 보안이 뛰어남
- 단점: 무겁고 복잡함

### RPC API(Remote Procedure Call)
클라이언트가 서버의 함수를 직접 호출하듯 동작한다.

보통 JSON-RPC 또는 XML-RPC 형식으로 요청을 보냄

마치 함수 호출하듯 addUser(‘홍길동’) 같은 요청을 날림

단순하고 빠르지만, REST처럼 유연하진 않음

### 3. WebSocket API
"실시간 통신이 필요할 때 최고!"

클라이언트와 서버 간에 연결을 유지한 채 실시간으로 양방향 통신 가능

서버도 클라이언트에게 먼저 메시지를 보낼 수 있음 (ex. 채팅, 알림, 주식 시세)

보통 JSON 형식으로 메시지를 주고받음

REST보다 효율적일 수 있음 (요청 없이도 데이터 수신 가능)

### 4. REST API
"가장 널리 쓰이는 현대적인 방식!"

정식 명칭: Representational State Transfer

주로 HTTP 메서드 (GET, POST, PUT, DELETE) 를 사용함

자원을 URL로 표현 (예: /users/1)

가볍고 단순해서 웹, 모바일 앱 개발에서 가장 흔히 사용

보통 JSON 형식으로 데이터를 주고받음
