# HTTP 헤더

## HTTP 헤더 용도

- HTTP 전송에 필요한 모든 부가정보
- 예) 메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증, 요청 클라이언트, 서버 정보, 캐시 관리 정보..
- 표준 헤더가 너무 많음
- 필요시 임의의 헤더 추가 가능

### 분류 - RFC2616(과거)

- 헤더 분류
    - General 헤더: 메시지 전체에 적용되는 정보, 예) Connection: close
    - Request 헤더: 요청 정보, 예) User-Agent: Mozilla/5.0 (Macintosh; ..)
    - Response 헤더: 응답 정보, 예) Server: Apache
    - Entity 헤더: 엔티티 바디 정보, 예) Content-Type: text/html, Content-Length: 3423

- HTTP BODY
    - 메시지 본문(message body)은 엔티티 본문(entity body)을 전달하는데 사용
    - 엔티티 본문은 요청이나 응답에서 전달할 실제 데이터
    - 엔티티 헤더는 엔티티 본문의 데이터를 해석할 수 있는 정보 제공
        - 데이터 유형(html,json), 데이터 길이, 압축 정보 등등

### HTTP 표준의 변화

- 1999년 RFC2616 폐기
- 2014년 FRC7230 ~ 7235 등장

### RFC723x 변화

- 엔티티(Entity) -> 표현(Representation)
- Representation = representation Metadata + Representation Data
- 표현 = 표현 메타데이터 + 표현 데이터

### HTTP BODY

#### message body = RFC7230(최신)

- 메시지 본문(message body)을 통해 표현 데이터 전달
- 메시지 본문 = 페이로드(payload)
- 표현은 요청이나 응답에서 전달할 실제 데이터
- 표현 헤더는 표현 데이터를 해석할 수 있는 정보 제공
    - 데이터 유형(html,json), 데이터 길이, 압축 정보 등등
- 참고: 표현 헤더는 표현 메타데이터와, 페이로드 메시지를 구분해야 하지만, 여기서는 생략

```
표현 헤더
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Length: 3423

표현 데이터
<html>
  <body>...</body>
<html>
```

## HTTP 헤더 표현

- Content-Type: 표현 데이터의 형식
- Content-Encoding: 표현 데이터의 압축 방식
- Content-Language: 표현 데이터의 자연 언어
- Content-Length: 표현 데이터의 길이
- 표현 헤더는 전송, 응답 둘다 사용

### Content-Type

#### 표현 데이터의 형식 설명

- 미디어 타입, 문자 인코딩
- 예)
    - text/html; charset=utf-8
    - application/json
    - image/png

### Content-Encoding

#### 표현 데이터 인코딩

- 표현 데이터를 압축하기 위해 사용
- 데이터를 전달하는 곳에서 압축 후 인코딩 헤더 추가
- 데이터를 읽는 쪽에서 인코딩 헤더의 정보로 압축 해제
- 예)
    - gzip
    - deflate
    - identity

### Content-Language

#### 표현 데이터의 자연 언어

- 표현 데이터의 자연 언어를 표현
- 예)
    - ko
    - en
    - en-US

### Content-Length

#### 표현 데이터의 길이

- 바이트 단위
- Transfer-Encoding(전송 코딩)을 사용하면 Content-Length를 사용하면 안됨

## 협상(콘텐츠 네고시에이션)

#### 클라이언트가 선호하는 표현 요청

- Accept: 클라이언트가 선호하는 미디어 타입 전달
- Accept-Charset: 클라이언트가 선호하는 문자 인코딩
- Accept-Encoding: 클라이언트가 선호하는 압축 인코딩
- Accept-Language: 클라이언트가 선호하는 자연 언어
- 협상 헤더는 요청시에만 사용

### 협상과 우선순위1

#### Quality Values(q)

```
GET /event
Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8;en;q=0.7 
```

- Quality Values(q) 값 사용
- 0~1, 클수록 높은 우선순위
- 생략하면 1
- Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8;en;q=0.7
- 1 ko-KR;q=1 (q생략)
- 2 ko;q=0.9
- 3 en-US;q=0.8
- 4 en:q=0.7

다중 언어 지원 서버에서 우선순위를 지정해서 요청할 수 있다

### 협상과 우선순위2

#### Quality Values(q)

```
GET /event
Accept: text/*, text/plain, text/plain;format=flowed, */*
```

- 구체적인 것이 우선한다
- `Accept: text/*, text/plain, text/alain;format=flowed, */*`
    1. `text/plain;format=flowed`
    2. `text/plain`
    3. `text/*`
    4. `*/*`

### 협상과 우선순위3

#### Quality Values(q)

- 구체적인 것을 기준으로 미디어 타입을 맞춘다.
- `Accept: text/*;q=0.3, text/html;q=0.7; text/html;level=1,text/html;level=2;q=0.4, */*,q=0.5`

## 전송 방식

### 단순 전송

```
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Length: 3423

<html>
  <body>...</body>
</html>
```

길이 값을 아는 경우

### 압축 전송

```
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Encoding: gzip
Content-Length: 521

lsdlfkj234823098a0sdflj
```

압축 전송의 경우에는 Encoding 방식을 명시해야 한다

### 분할 전송

```
HTTP/1.1 200 OK
Content-Type: text/plain
Transfer-Encoding: chunked

5
Hello
5
World
0
\r\n
```

분할 전송시에는 `Content-Length` 를 보내면 안된다

### 범위 전송

```
HTTP/1.1 200 OK
Content-Type: text/plain
Content-Range: bytes 1001-2000 / 2000

w0398023980as8f0sa
```

이미지를 절반정도 받다가 끊겼는데 처음부터 다시 받는게 아니라 범위를 지정해서 요청할 수 있다

## 일반 정보

### From

#### 유저 에이전트의 이메일 정보

- 일반적으로 잘 사용되지 않음
- 검색 엔진 같은 곳에서, 주로 사용
- 요청에서 사용

### Referer

#### 이전 웹 페이지 주소

- 현재 요청된 페이지의 이전 웹 페이지 주소
- A -> B로 이동하는 경우 B를 요청할 때 Referer: A를 포함해서 요청
- `Referer` 를 사용해서 유입 경로 분석 가능
- 요청에서 사용
- 참고: `referer` 는 단어 `referrer` 의 오타

### User-Agent

#### 유저 에이전트 애플리케이션 정보

- user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36 (KHTML, like Gecko)
  Chrome/86.0.4240.183 Safari/537.36
- 클라이언트의 애플리케이션 정보(웹 브라우저 정보, 등등)
- 통계 정보
- 어떤 종류의 브라우저에서 장애가 발생하는지 파악 가능
- 요청에서 사용

### Server

#### 요청을 처리하는 ORIGIN 서버의 소프트웨어 정보

- Server: Apache/2.2.22 (Debian)
- server: nginx
- 응답에서 사용

### Date

#### 메시지가 발생한 날짜와 시간

- Date: Tue, 15 Nov 1994 08:12L31 GMT
- 응답에서 사용

## 특별한 정보

### Host

#### 요청한 호스트 정보(도메인)

- 요청에서 사용
- 필수
- 하나의 서버가 여러 도메인을 처리해야 할 때
- 하나의 IP 주소에 여러 도메인이 적용되어 있을 때

**가상호스트를 통해 여러 도메인을 한번에 처리할 수 있는 서버 실제 애플리케이션이 여러개 구동될 수 있다**

### Location

#### 페이지 리다이렉션

- 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location 위치로 자동 이동(리다이렉트)
- 응답코드 3xx에서 설명
- 201 (Created): Location 값은 요청에 의해 생성된 리소스 URI
- 3xx (Redirection): Location 값은 요청을 자동으로 리디렉션하기 위한 대상 리소스를 가리킴

### Allow

#### 허용 가능한 HTTP 메서드

- 405 (Method Not Allowed) 에서 응답에 포함해야함
- Allow: GET, HEAD, PUT

### Retry-After

#### 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간

- 503 (Service Unavailable): 서비스가 언제까지 불능인지 알려줄 수 있음
- Retry-After: Fri, 31 Dec 1999 23:59:59 GMT (날짜 표기)
- Retry-After: 120 (초단위 표기)

## 인증

### Authorization

#### 클라이언트 인증 정보를 서버에 전달

- Authorization: Basic xxxxxxxxxxxxx

### WWW-Authenticate

#### 리소스 접근시 필요한 인증 방법 정의

- 리소스 접근시 필요한 인증 방법 정의
- 401 Unauthorized 응답과 함께 사용
- WWW-Authenticate: Newauth realm="apps", type=1, title="Login to \"apps\"", Basic realm="simple"

## 쿠키

### 모든 요청에 정보를 넘기는 문제

- 모든 요청에 사용자 정보가 포함되도록 개발 해야함
- 브라우저를 완전히 종료하고 다시 열면?