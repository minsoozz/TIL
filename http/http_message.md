# HTTP 메시지

## HTTP 메시지 구조

1. start-line 시작라인
2. header 헤더
3. empty line 공백 라인 (CRLF)
4. message body

### HTTP 요청 메시지

`GET /search?q=hello&hl=ko HTTP/1.1`

`Host: www.google.com`

요청 메시지도 body 본문을 가질 수 있음

### HTTP 응답 메시지

`HTTP/1.1 200 OK`

`Content-Type: text/html;charset=UTF-8`

`Content-Length: 3423`

`공백 라인`

`<html><body>...</body></html>`

## 시작 라인

- start-line = request-line / status-line
- request-line = method SP(공백) request-target SP HTTP-version CRLF(엔터)
- HTTP 메서드 (GET: 조회)
- 요청대상 (/search?q=hello&hl=ko)
- HTTP Version

### HTTP 메서드

- 종류: GET, POST, PUT, DELETE...
- 서버가 수행해야 할 동작 지정
- GET: 리소스 조회
- POST: 요청 내역 처리

### 요청 대상

- `absolute-path[?query] (절대경로[?쿼리])`
- 절대경로 = "/"로 시작하는 경로
- 참고: *, http://...?x=y 와 같이 다른 유형의 경로지정 방법도 있다

### 요청 메시지

- HTTP Version

### 응답 메시지

- HTTP 버전
- HTTP 상태 코드: 요청 성공, 실패를 나타냄
    - 200: 성공
    - 400: 클라이언트 요청 오류
    - 500: 서버 내부 오류
- 이유 문구: 사람이 이해할 수 있는 짧은 상태 코드 설명 글

## HTTP 헤더

- header-field = filed-name ":" OWS field-value OWS (OWS:띄어쓰기 허용)
- `field-name` 은 대소문자 구문 없음

### 용도

- HTTP 전송에 필요한 모든 부가정보
- 예) 메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증, 요청 클라이언트 정보, 서버 애플리케이션 정보, 캐시 관리 정보...
- 표준 헤더가 너무 많음
- 필요시 임의의 헤더 추가 가능
    - helloworld: hihi

## HTTP 메시지 바디

### 용도

- 실제 전송할 데이터
- HTML 문서, 이미지, 영상, JSON 등등 byte로 표현할 수 있는 모든 데이터 전송 가능

## 단순함 확장 가능

- `HTTP` 는 단순하다. 스펙도 읽어볼만 하다
- `HTTP` 메시지도 매우 단순
- 크게 성공하는 표준 기술은 단순하지만 확장 가능한 기술

## HTTP 정리

- HTTP 메시지에 모든 것을 전송
- HTTP 역사 HTTP/1.1을 기준으로 학습
- 클라이언트 서버 구조
- 무상태 프로토콜(stateless)
- HTTP 메시지
- 단순함, 확장가능
- `지금은 HTTP 시대`