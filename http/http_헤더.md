# HTTP 헤더

## 용도

- HTTP 전송에 필요한 모든 부가정보
- 예) 메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증, 요청 클라이언트, 서버 정보, 캐시 관리 정보..
- 표준 헤더가 너무 많음
- 필요시 임의의 헤더 추가 가능

## 분류 - RFC2616(과거)

- 헤더 분류
    - General 헤더: 메시지 전체에 적용되는 정보, 예) Connection: close
    - Request 헤더: 요청 정보, 예) User-Agent: Mozilla/5.0 (Macintosh; ..)
    - Response 헤더: 응답 정보, 예) Server: Apache
    - Entity 헤더: 엔티티 바디 정보, 예) Content-Type: text/html, Content-Length: 3423