## 🎙️ 면접답변
1. 브라우저에서 URL을 입력하면 URL을 파싱하여 프로토콜(https)과 도메인명(www.google.com), 경로(/)를 분리합니다.
2. 이후 로컬 캐시 → OS → 라우터 → DNS 순으로 도메인명과 매칭되는 IP주소를 반환합니다.
3. 그리고 해당 IP주소를 통해 클라이언트와 서버가 TCP연결을 시도하며 이 때 3-way-handshake가 일어납니다.
    HTTPS의 경우 TLS 핸드셰이크를 통해 보안 세션을 설정합니다.
4. TCP 연결이 끝나고 나면 일반적으로 GET/ HTTP/1.1 요청을 보냅니다. 이 요청에는 헤더 정보도 포함됩니다.
5. 이후 서버는 요청에 따라 HTML 문서를 생성하여 응답합니다.
6. 브라우저는 HTML을 파싱하고 CSS/JS를 별도로 요청한 뒤, 
    DOM트리, CSSOM → Render Tree → Layout → Paint → Composite 순으로 화면을 그립니다.

---

## 🔗 DNS조회 후, 왜 TCP연결을 하나요?

> TCP 연결은 클라이언트와 서버가 안전하게 데이터 통신을 하기 위한 연결 설정 과정으로 3-way 핸드셰이크라고 합니다.

### 왜 필요한가?

웹에서 서버와 클라이언트가 통신하려면, 둘 사이에 연결이 먼저 열려야함.

이걸 마치 전화연결하듯이 준비됐는지 확인하는 절차라고 보면됨.

## TCP 연결 흐름: 3-Way Handshake

브라우저가 DNS를 통해 IP를 얻은 다음, 그 IP 주소로 접속하려면 아래 3단계를 거친다.

### 1단계: 클라이언트 → 서버 (SYN)

클라이언트에서 ISN을 담아 SYN를 서버에 보낸다.

### 2단계: 서버 → 클라이언트 (SYN - ACK)

서버에서 SYN을 수신하고 서버의 ISN을 보내며 승인번호로 클라이언트의 ISN + 1을 보낸다.

### 3단계: 클라이언트 → 서버 (ACK)

클라이언트는 다시 서버에 ISN + 1한 값이 승인번호를 담아 ACK를 서버에 보낸다.

### 연결 완료

- SYN: Synchronize
- ACK: Acknowledge
- INS: Initial Sequence Number

## HTTPS 일 땐 한가지 더있음

TLS(SSL) Handshake

- HTTPS의 경우, TCP 연결 후 바로 TLS 핸드셰이크도 함
- 인증서 확인, 대칭키 공유 등 처리 → 이후부터는 암호화된 HTTP 요청 주고 받기 가능

**💬 면접에서 꼬리 질문 포인트**

| **질문** | **포인트** |
| --- | --- |
| TCP 시퀀스 번호는 왜 필요하죠? | 패킷 순서 보장 목적 |
| ISN은 어떻게 결정되나요? | 랜덤 / 타임베이스 방식 |
| TCP 연결 초기 흐름을 시퀀스 번호로 설명해보세요 | 3-way handshake에 대한 정확한 이해 확인 |
| 만약 ISN이 항상 0이라면? | TCP 세션 하이재킹 공격 가능성 있음 |

---

## HTTP 요청이란?

> HTTP 요청은 브라우저가 서버에게 자원을 요청하기 위해 보내는 메세지.
메서드, 경로, 헤더, 바디로 구성됨.
서버는 이 요청을 분석해서 알맞은 HTML/JSON/리소스를 응답하게됨

## 왜 HTTP 요청이 필요한가?

웹은 클라이언트-서버 구조임

> 사용자(클라이언트)가 요청을 해야
서버는 응답을 하고 HTML이나 JSON 같은 걸 보내줄 수 있음.
> 

이걸 하기 위한 약속된 메시지 형식이 바로 HTTP 요청이다.

## HTTP 요청의 구조

HTTP 요청은 아래와 같이 구성됨.

```java
GET /index.html HTTP/1.1
Host: www.example.com
User-Agent: Mozilla/5.0
Accept: text/html
Cookie: sessionId=abc123
```

- 구성요소
    
    
    | 항목 | 설명 |
    | --- | --- |
    | 요청 라인(Request Line) | GET /index.html HTTP/1.1 ← 요청의 핵심 |
    | 헤더 (Headers) | 요청에 대한 부가 정보(브라우저, 인증, 언어 등) |
    | 본문(Body) | POST/PUT일 때 요청 데이터 포함(예: JSON, 폼 데이터 등) |

**🔑 주요 HTTP 메서드**

| **메서드** | **의미** |
| --- | --- |
| **GET** | 데이터 조회 (읽기 전용) |
| **POST** | 데이터 생성 또는 전송 |
| **PUT/PATCH** | 데이터 수정 |
| **DELETE** | 데이터 삭제 |

**💬 면접에서 자주 나오는 꼬리 질문**

| **질문** | **포인트** |
| --- | --- |
| GET과 POST의 차이점은? | GET은 URL 쿼리스트링 사용, POST는 body 사용 |
| 멱등성(idempotent)이란? | GET, PUT, DELETE는 멱등, POST는 아님 |
| RESTful 설계란? | HTTP 메서드와 URI를 자원 중심으로 잘 활용하는 방식 |
| 쿠키는 어디에 들어가나요? | Cookie 헤더로 클라이언트에서 서버로 전송됨 |

---

## HTTP 요청 이후 서버 처리부터 렌더링까지

HTTP 요청이 들어오면 DispatcherServlet(프론트 컨트롤러)이 이를 받아,

요청 URL과 HTTP 메서드를 분석해서 해당 요청에 매핑되는 컨트롤러로 메서드를 전달한다.

이후 컨트롤러는 비즈니스 로직을 처리하거나 Service/Repository 계층과 협력해 데이터를 가공한 뒤
ViewResolver를 통해 HTML(View)를 반환하거나,

REST API의 경우 JSON 형태로 직렬화된 데이터를 응답한다.

최종적으로 브라우저는 이 HTML 또는 JSON 응답을 받아 렌더링 하거나, 자바스크립트에서 활용하게 된다.

**💬 면접에서 꼬리 질문 포인트**

| **질문** | **의도** |
| --- | --- |
| ViewResolver는 어떤 역할을 하나요? | HTML 이름 매핑 처리 |
| RestController와 Controller의 차이는? | JSON vs View |
| @ResponseBody는 언제 쓰나요? | JSON 응답 명시 |
| DispatcherServlet은 왜 프론트 컨트롤러인가요? | 모든 요청을 단일 진입점으로 수집 |
