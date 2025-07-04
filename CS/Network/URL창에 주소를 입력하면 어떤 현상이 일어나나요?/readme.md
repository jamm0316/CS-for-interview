## 🎙️ 면접답변
1. 브라우저에서 URL을 입력하면 URL을 파싱하여 프로토콜(https)과 도메인명(www.google.com), 경로(/)를 분리합니다.
2. 이후 로컬 캐시 → OS → 라우터 → DNS 순으로 도메인명과 매칭되는 IP주소를 반환합니다.
3. 그리고 해당 IP주소를 통해 클라이언트와 서버가 TCP연결을 시도하며 이 때 3-way-handshake가 일어납니다.
    HTTPS의 경우 TLS 핸드셰이크를 통해 보안 세션을 설정합니다.
4. TCP 연결이 끝나고 나면 일반적으로 GET/ HTTP/1.1 요청을 보냅니다. 이 요청에는 헤더 정보도 포함됩니다.
5. 이후 서버는 요청에 따라 HTML 문서를 생성하여 응답합니다.
6. 브라우저는 HTML을 파싱하고 CSS/JS를 별도로 요청한 뒤, 
    DOM트리, CSSOM → Render Tree → Layout → Paint → Composite 순으로 화면을 그립니다.
