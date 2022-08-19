# HTTP 헤더1 - 일반 헤더

#### HTTP 헤더 개요

**HTTP 헤더**

- header-field = field-name ":" OWS field-value OWS (OWS: 띄어쓰기 허용)
- field-name은 대소문자 구분 없음

```
GET /search?q=hello&hl=ko HTTP/1.1
Host: www.google.com
```

```
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Length: 3423

<html>
 <body>...</body>
</html>
```

**HTTP 헤더
용도**

- HTTP 전송에 필요한 모든 부가정보
- 예) 메시지 바디의 내용, 메시지 바디의 크기, 압축, 인증, 요청 클라이언트, 서버 정보, 캐시 관리 정보...
- 표준 헤더가 너무 많음
- 필요시 임의의 헤더 추가 가능
  - helloworld: hihi

**HTTP 헤더
분류 - RFC2616(과거)**

- 헤더 분류
  - General 헤더: 메시지 전체에 적용되는 정보, 예) Connection: close
  - Request 헤더: 요청 정보, 예) User-Agent: Mozilla/5.0 (Macintosh; ..)
  - Response 헤더: 응답 정보, 예) Server: Apache
  - Entity 헤더: 엔티티 바디 정보, 예) Content-Type: text/html, Content-Length: 3423

**HTTP BODY
message body - RFC2616(과거)**

- 메시지 본문(message body)은 엔티티 본문(entity body)을 전달하는데 사용
- 엔티티 본문은 요청이나 응답에서 전달할 실제 데이터
- 엔티티 헤더는 엔티티 본문의 데이터를 해석할 수 있는 정보 제공
  - 데이터 유형(html, json), 데이터, 길이 압축 정보 등등

HTTP 표준
1999년 FRC2616 -> 폐기됨
2014년 RFC7230~7235 등장

**RFC723x 변화**

- 엔티티(Entity) -> 표현(Representation)
- Representation = representation Metadata + Representation Data
- 표현 = 표현 메타데이터 + 표현 데이터

**HTTP BODY
message body - RFC7230**

- 메시지 본문(message body)을 통해 표현 데이터 전달
- 페이지 본문 = 페이로드(payload)
- 표현은 요청이나 응답에서 전달할 실제 데이터
- 표현 헤더는 표현 데이터를 해석할 수 있는 정보 제공
  - 데이터 유형(html, json), 데이터 길이, 압축 정보 등등
- 참고: 표현 헤더는 표현 메타데이터와 페이로드 메시지를 구분해야 하지만, 여기서는 표현헤더라고 말함

#### 표현

- Content-Type: 표현 데이터의 형식
- Content-Encoding: 표현 데이터의 압축 방식
- Content-Language: 표현 데이터의 자연 언어
- Content-Length: 표현 데이터의 길이
- 표현 헤더는 전송, 응답 둘다 사용

**Content-Type
표현 데이터의 형식 설명**

- 미디어 타입, 문자 인코딩
- 예)
  - text/html; charset=utf-8
  - application/json
  - image/png

**Content-Encoding
표현 데이터 인코딩**

- 표현 데이터를 압축하기 위해 사용
- 데이터를 전달하는 곳에서 압축 후 인코딩 헤더 추가
- 데이터를 읽는 쪽에서 인코딩 헤더의 정보로 압축 해제
- 예)
  - gzip (압축)
  - deflate
  - identity (원본)

**Content-Language
표현 데이터의 자연 언어**

- 표현 데이터의 자연 언어를 표현
- 예)
  - ko
  - en
  - en-US

**Content-Length
표현 데이터의 길이**

- 바이트 단위
- Transfer-Encoding(전송 코딩)을 사용하면 Content-Length를 사용하면 안됨

#### 협상(콘텐츠 네고시에이션)

**클라이언트가 선호하는 표현 요청**

- Accept: 클라이언트가 선호하는 미디어 타입 전달
- Accept-Charset: 클라이언트가 선호하는 문자 인코딩
- Accept-Encoding: 클라이언트가 선호하는 압축 인코딩
- ACcept-Language: 클라이언트가 선호하는 자연 언어
- 협상 헤더는 요청시에만 사용

한국어 브라우저를 사용하는 클라이언트가 다중 언어 지원하는 서버(1. 기본 영어, 2. 한국어 지원)에 요청을 할때 Accept-Language를 적용하지 않으면 기본 영어로 응답 받지만, 적용하면 한국어로 응답 받는다. 하지만 한국어 지원을 안할 경우에 우선순위를 지정할 수 있다.

\*\*협상과 우선순위1
Quality Values(q)

```
GET /event
ACcept-language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7
```

- Quality Values(q) 값 사용
- 0~1, 클수록 높은 우선순위
- 생략하면 1
- Accept-Language: ko-KR,ko;q=0.9,en-US;q=0.8,en;q=0.7
  - 1.  ko-KR;q=1 (q=1이면 생략)
  - 2.  ko;q=0.9
  - 3.  en-US;q=0.8
  - 4.  en;q=0.7

\*\*협상과 우선순위2
Quality Values(q)

```
GET /event
Accept: text/*, text/plain, text/plain;format=flowed, */*
```

- 구체적인 것이 우선한다.
- Accept: text/_, text/plain, text/plain;format=flowed, _/\*
  1.  text/plain;format=flowed
  2.  text/plain
  3.  text/\*
  4.  _/_

\*\*협상과 우선순위3
Quality Values(q)

- 구체적인 것을 기준으로 미디어 타입을 맞춘다.
- Accept: text/_;q=0.3, text/html;q=0.7, text/html;level=1, text/html;level=2;q=0.4, _/\*;q=0.5

#### 전송 방식

- Transfer-Encoding
- Range, Content-Range

**전송 방식 설명**

- 단순 전송 : Content-Length

```
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Length: 3423
```

- 압축 전송 : Content-Encoding

```
HTTP/1.1 200 OK
Content-Type: text/html;charset=UTF-8
Content-Encoding: gzip
Content-Length: 3423
```

- 분할 전송 : Transfer-Encoding

```
HTTP/1.1 200 OK
Content-Type: text/plain
Transfer-Encoding: chunked

5
hEllo
5
World
0
\r\n
```

- 범위 전송 : Range, Content-Range

```
GET /event
Range: bytes=1001-2000
```

```
HTTP/1.1 200 OK
Content-Type: text/plain
Content-Range: bytes 1001-2000 / 2000

afdadfdsfass
```

#### 일반 정보

- From: 유저 에이전트의 이메일 정보
- Referer: 이전 웹 페이지 주소
- User-Agent: 유저 에이전트 애플리케이션 정보
- Server: 요청을 처리하는 오리진 서버의 소프트웨어 정보
- DatE: 메시지가 생성된 날짜

**From
유저 에이전트의 이메일 정보**

- 일반적으로 잘 사용되지 않음
- 검색 엔진 같은 곳에서, 주로 사용
- 요청에서 사용

  **Referer
  이전 웹 페이지 주소**

- 현재 요청된 페이지의 이전 웹 페이지 주소
- A->B로 이동하는 경우 B를 요청할 때 Referer: A를 포함해서 요청
- Referer를 사용해서 유입 경로 분석 가능
- 요청에서 사용
- 참고: referer는 단어 referrer의 오타

**User-Agent
유저 에이전트 애플리케이션 정보**

- user-agent: Mozilla/5.0 (Macintosh; Intel Mac OS X 10_15_7) AppleWebKit/537.36(KHTML, like Gecko) Chrome/86.0.4240.183 Safari/537.36
- 클라이언트의 애플리케이션 정보(웹 브라우저 정보, 등등)
- 통계 정보
- 어떤 종류의 브라우저에서 장애가 발생하는지 파악 가능
- 요청에서 사용

**Server
요청을 처리하는 ORIGIN 서버의 소프트웨어 정보**

- Server: Apache/2.2.22 (Debian)
- server: nginx
- 응답에서 사용

**Date
메시지가 발생한 날짜와 시간**

- Date: Tue, 15 Nov 1994 08:12:31 GMT
- 응답에서 사용

#### 특별한 정보

- Host: 요청한 호스트 정보(도메인)
- Location: 페이지 리다이렉션
- Allow: 허용 가능한 HTTP 메서드
- Retry-After: 유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간

**Host
요청한 호스트 정보(도메인) **

- 요청에서 사용
- 필수
- 하나의 서버가 여러 도메인을 처리해야 할 때
- 하나의 IP 주소에 여러 도메인이 적용되어 있을 때
  가상 호스트를 통해 여러 도메인을 한번에 처리할 수 있는 서버, 실제 애플리케이션이 여러개 구동될 수 있다.

```
GET /hello HTTP/1.1
Host: aaa.com
```

**Location
페이지 리다이렉션**

- 웹 브라우저는 3xx 응답의 결과에 Location 헤더가 있으면, Location위치로 자동 이동(리다이렉트)
- 응답코드 3xx에서 설명
- 201 (Created): Location 값은 요청에 의해 생성된 리소스 URI
- 3xx (Redirection): Location 값은 요청을 자동으로 리다이렉션하기 위한 대상 리소스를 가리킴

**Allow
허용 가능한 HTTP 메서드**

- 405(Method Not Allowed) 에서 응답에 포함해야함
- Allow: GET, HEAD, PUT

**Retry-After
유저 에이전트가 다음 요청을 하기까지 기다려야 하는 시간**

- 503 (Service Unavailable): 서비스가 언제까지 불능인지 알려줄 수 있음
- Retry-After: Fri, 31 Dec 1999 23:59:59 GMT (날짜 표기)
- Retry-After: 120 (초 단위 표기)

**인증**

- Authorization: 클라이언트 인증 정보를 서버에 전달
- WWW-Authenticate: 리소스 접근시 필요한 인증 방법 정의

#### 인증

**Authorization (인가, 권한부여)
클라이언트 인증 정보를 서버에 전달**

- Authorization: Basic xxxxxxxxxxxxxx

**WWW-Authenticate (인증)
리소스 접근시 필요한 인증 방법 정의**

- 리소스 접근시 필요한 인증 방법 정의
- 401 Unauthorized 응답과 함께 사용
- WWW-Authenticate: Newauth realm= "apps", type=1, title="Login to \"apps\
  "", Basic realm="simple"

#### 쿠키

- Set-Cookie: 서버에서 클라이언트로 쿠키 전달(응답)
- Cookie: 클라이언트가 서버에서 받은 쿠키를 저장하고, HTTP 요청시 서버로 전달

**Stateless**

- HTTP는 무상태(Stateless) 프로토콜이다.
- 클라이언트와 서버가 요청과 응답을 주고 받으면 연결이 끊어진다.
- 클라이언트가 다시 요청하면 서버는 이전 요청을 기억하지 못한다.
- 클라이언트와 서버는 서로 상태를 유지하지 않는다.

쿠키를 사용하지 않으면 서버는 클라이언트가 누군지 모른다.
모든 요청에 사용자정보를 포함해도 되지만, 번거롭고 데이터 양이 많아진다.

**쿠키
로그인**

1.

```
POST /login HTTP/1.1
user=홍길동
```

2.

```
HTTP/1.1 200 OK
Set-Cookie: user=홍길동

홍길동님이 로그인했습니다.
```

3. 쿠키 저장소에 저장
4. 로그인 이후 welcome 페이지 접근

```
GET /welcome HTTP/1.1
Cookie: user=홍길동
```

5.

```
HTTP/1.1 200 OK

안녕하세요. 홍길동님
```

**쿠키**

- 예) set-cookie: **sessionId=abcde1234**; **expires**=Sat, 26-Dec-2020 00:00:00 GMT; **path**=/; **domain**= google.com; **Secure**
- 사용처
  - 사용자 로그인 세션 관리
  - 광고 정보 트래킹
- 쿠키 정보는 항상 서버에 전송됨
  - 네트워크 트래픽 추가 유발
  - 최소한의 정보만 사용(세션 id, 인증 토큰)
  - 서버에 전송하지 않고, 웹 브라우저 내부에 데이터를 저장하고 싶으면 웹 스토리지(localStorage, sessionStorage) 참고
- 주의!
  - 보안에 민감한 데이터는 저장하면 안됨(주민번호, 신용카드 번호 등등)

**쿠키 - 생명주기
Expires, max-age**

- Set-Cookie: expires=Sat, 26-Dec-2020 04:39:21 GMT
  - 만료일이 되면 쿠키 삭제
- Set-Cookie: max-age=3600 (3600초)
  - 0이나 음수를 지정하면 쿠키 삭제
- 세션 쿠키: 만료 날짜를 생략하면 브라우저 종료시 까지만 유지
- 영속 쿠키: 만료 날짜를 입력하면 해당 날짜까지 유지

**쿠키 - 도메인
Domain**

- 예) domain=example.org
- 명시: 명시한 문서 기준 도메인 + 서브 도메인 포함
  - domain=example.org를 지정해서 쿠키 생성
    - example.org는 물론이고
    - dev.example.org도 쿠키 접근
- 생략: 현재 문서 기준 도메인만 적용
  - example.org 에서 쿠키를 생성하고 domain 지정을 생략 - example.org에서만 쿠키 접근 - dev.example.org는 쿠키 미접근
    **쿠키 - 경로
    Path**
- 예) path=/home
- 이 경로를 포함한 하위 경로 페이지만 쿠키 접근
- 일반적으로 path=/ 루트로 지정
- 예)
  - path=/home 지정
  - /home -> 가능
  - /home/level1 -> 가능
  - /home/level1/level2 -> 가능
  - /hello -> 불가능

**쿠키 - 보안
Secure, HttpOnly, SameSite**

- Secure
  - 쿠키는 http, https를 구분하지 않고 전송
  - Secure를 적용하면 https인 경우에만 전송
- HttpOnly
  - XSS 공격 방지
  - 자바스크립트에서 접근 불가 (document.cookie)
  - HTTP 전송에만 사용
- SameSite
  - XSRF 공격 방지
  - 요청 도메인과 쿠키에 설정된 도메인이 같은 경우만 쿠키 전송

# HTTP 헤더2 - 캐시와 조건부 요청

#### 캐시 기본 동작

**캐시가 없을 때**

- 데이터가 변경되지 않아도 계쏙 네트워크를 통해서 데이터를 다운로드 받아야 한다.
- 인터넷 네트워크는 매우 느리고 비싸다
- 브라우저 로딩 속도가 느리다.
- 느린 사용자 경험

**캐시 적용
첫 번째 요청**

```
HTTP/1.1 200 OK
Content-Type: image/jpeg
cache-control: max-age=60
Content-Length: 34012

dafdsfasfsad
```

**캐시 적용**

- 캐시 덕분에 캐시 가능 시간동안 네트워크를 사용하지 않아도 된다.
- 비싼 네트워크 사용량을 줄일 수 있다.
- 브라우저 로딩 속도가 매우 빠르다.
- 빠른 사용자 경험

**캐시 적용
세 번재 요청 - 캐시 시간 초과**
요청할때 브라우저캐시에서 캐시 유효 시간을 검증한다.
시간이 지나있으면 첫번째요청처럼 다시받아서 캐시에 저장한다.

- 캐시 유효시간이 초과하면, 서버를 통해 데이터를 다시 조회하고, 캐시를 갱신한다.
- 이 때 다시 네트워크 다운로드가 발생한다.

#### 검증 헤더와 조건부 요청1

- 캐시 유효 시간이 초과해서 서버에 다시 요청하면 다음 두가지 상황이 나타난다.
  1.  서버에서 기존 데이터를 변경함
  2.  서버에서 기존 데이터를 변경하지 않음
- 캐시 만료후에도 서버에서 데이터를 변경하지 않음
- 생각해보면 데이터를 전송하는 대신에 저장해 두었던 캐시를 재사용 할 수 있다.
- 단 클라이언트의 데이터와 서버의 데이터가 같다는 사실을 확인할 수 있는 방법 필요

**검증 헤더 추가
첫번째 요청**

```
HTTP/1.1 200 OK
Content-Type: image/jpeg
cache-control: max-age=60
Last-Modified: 2020년 11월 10일 10:00:00 // 데이터가 마지막에 수정된 시간
Content-Length: 34012

adfas
```

데이터 최종 수정일 2020년 11월 10일 10:00:00
응답 결과를 캐시에 저장

**두번째 요청 - 캐시 시간 초과**

```
GET /star.jpg
if-modified-since: 2020년 11월 10일 10:00:00
```

서버와 캐시의 데이터 최종 수정일이 같다. = 데이터가 아직 수정되지 않았다.

```
HTTP/1.1 304 Not Modified
Content-Type: image/jpeg
cache-control: max-age=60
Last-Modified: 2020년 11월 10일 10:00:00
Content-Length: 34012
```

HTTP Body가 없음
헤더만 전송
-> 응답 결과를 재사용, 헤더 데이터 갱신

**검증 헤더와 조건부 요청
정리 **

- 캐시 유효 시간이 초과해도, 서버의 데이터가 갱신되지 않으면 304 Not Modified + 헤더 메타 정보만 응답(바디X)
- 클라이언트는 서버가 보낸 응답 헤더 정보로 캐시의 메타 정보를 갱신
- 클라이언트는 캐시에 저장되어 있는 데이터 재활용
- 결과적으로 네트워크 다운로드가 발생하지만 용량이 적은 헤더 정보만 다운로드
- 매우 실용적인 해결책

#### 검증 헤더와 조건부 요청2

- 검증 헤더
  - 캐시 데이터와 서버 데이터가 같은지 검증하는 데이터
  - Last-Modified, ETag
- 조건부 요청 헤더
  - 검증 헤더로 조건에 따른 분기
  - If-Modified-Since: Last-Modified 사용
  - If-None-Math: ETag 사용
  - 조건이 만족하면 200 OK
  - 조건이 만족하지 않으면 304 Not Modified
    **예시**
- If-Modified-Since: 이후에 데이터가 수정되었으면?
  - 데이터 미변경 예시
    - 캐시: 2020년 11월 10일 10:00:00 vs 서버: 2020년 11월 10일 10:00:00
    - 304 Not Modified, 헤더 데이터만 전송(Body 미포함)
    - 전송 용량 0.1M (헤더 0.1M, 바디 1.0M)
  - 데이터 변경 예시 - 캐시: 2020년 11월 10일 10:00:00 vs 서버: 2020년 11월 10일 11:00:00 - 200 OK, 모든 데이터 전송(Body 포함) - 전송 용량 1.1M (헤더 0.1M, 바디 1.0M)
    **검증 헤더와 조건부 요청
    Last-Modified, If-Modified-Since 단점**
- 1초 미만(0.x초) 단위로 캐시 조정이 불가능
- 날짜 기반의 로직 사용
- 데이터를 수정해서 날짜가 다르지만, 같은 데이터를 수정해서 데이터 결과가 똑같은 경우
- 서버에서 별도의 캐시 로직을 관리하고 싶은 경우
  - 예) 스페이스나 주석처럼 크게 영향이 없는 변경에서 캐시를 유지하고 싶은 경우

**ETag, If-None-Match**

- ETag(Entity Tag)
- 캐시용 데이터에 임의의 고유한 버전 이름을 달아둠
  - 예) ETag: "v1.0", ETag: "aafadsfasd"
- 데이터가 변경되면 이 이름을 바꾸어서 변경함(Hash를 다시 생성)
  - 예) ETag: "aaaaa" -> ETag: "bbbbb"
- 진짜 단순하게 ETag만 보내서 같으면 유지, 다르면 다시 받기!

**첫번째 요청**

```
HTTP/1.1 200 OK
Content-Type: image/jpeg
cache-control: max-age=60
ETag: "aaaaa"
Content-Length: 34012

adfas
```

**두번째 요청 - 캐시 시간 초과**

```
GET /star.jpg
If-None-Match: "aaaaa"
```

```
HTTP/1.1 304 Not Modified
Content-Type: image/jpeg
cache-control: max-age=60
ETag: "aaaaa"
Content-Length: 34012
```

HTTP body가 없음
ETag가 같으면 데이터가 수정되지 않았기 때문

**ETag, If-None-Match 정리**

- 진짜 단순하게 ETag만 서버에 보내서 같으면 유지, 다르면 다시 받기!
- 캐시 제어 로직을 서버에서 완전히 관리
- 클라이언트는 단순히 이 값을 서벙 ㅔ제공( 클라이언트는 캐시 매커니즘을 모름)
- 예)
  - 서버는 베타 오픈 기간인 3일 동안 파일이 변경되어도 ETag를 동일하게 유지
  - 애플리케이션 배포 주기에 맞추어 ETag 모두 갱신

#### 캐시와 조건부 요청 헤더

- Cache-Control: 캐시 제어
- Pragma: 캐시 제어(하위 호환)
- Expires: 캐시 유효 기간(하위 호환)

**Cache-Control
캐시 지시어(directives)**

- Cache-Control: max-age
  - 캐시 유효 시간, 초 단위
- Cache-Control: no-cache
  - 데이터는 캐시해도 되지만, 항상 원(origin) 서버에 검증하고 사용
- Cache-Control: no-store
  - 데이터에 민감한 정보가 있으므로 저장하면 안됨
    (메모리에서 사용하고 최대한 빨리 삭제)

**Pragma
캐시 제어(하위호환)**

- Pragma: no-cache
- HTTP 1.0 하위 호환

\*\*Expires
캐시 만료일 지정(하위 호환)

- expires: Mon, 01 Jan 1990 00:00:00 GMT
- 캐시 만료일을 정확한 날짜로 지정
- HTTP 1.0 부터 사용
- 지금은 더 유연한 Cache-Control: max-age 권장
- Cache-Control: max-age와 함께 사용하면 Expires는 무시

**검증 헤더와 조건부 요청 헤더**

- 검증 헤더(Validator)
  - ETag: "v1.0", ETag: "dafsdfsad"
  - Last-Modified: Thu, 04 Jun 2020 07:19:24 GMT
- 조건부 요청 헤더
  - If-Match, If-None-Match: ETag 값 사용
  - If-Modified-Since, If-Unmodified-Since: Last-Modified 값 사용

#### 프록시 캐시

\*\*Cache-Control
캐시 지시어(directives) - 기타

- Cache-Control: public
  - 응답이 public 캐시에 저장되어도 됨
- Cache-Control: private
  - 응답이 해당 사용자만을 위한 것임, private 캐시에 저장해야 함(기본값)
- Cache-Control: s-maxage
  - 프록시 캐시에만 적용되는 max-age
- Age: 60 (HTTP 헤더)
  - 오리진 서버에서 응답 후 프록시 캐시 내에 머문 시간(초)

#### 캐시 무효화

**Cache-Control
확실한 캐시 무효화 응답**

- Cache-Control: no-cache, no-store, must-revalidate
- Pragma: no-cache
  - HTTP 1.0 하위 호환

\*\*캐시 지시어(directives) - 확실한 캐시 무효화

- Cache-Control: no-cache
  - 데이터는 캐시해도 되지만, 항상 원 서버에 검증하고 사용(이름에 주의!)
- Cache-Control: no-store
  - 데이터에 민감한 정보가 있으므로 저장하면 안됨
    (메모리에서 사용하고 최대한 빨리 삭제)
- Cache-Control: must-revalidate
  - 캐시 만료후 최초 조회시 원 서버에 검증해야함
  - 원 서버 접근 실패시 반드시 오류가 발생해야함 - 504(Gateway Timeout)
  - must-revalidate는 캐시 유효 시간이라면 캐시를 사용함
- Pragma: no-cache
  - HTTP 1.0 하위 호환

no-cache에서 원서버에 접근할 수 없는 경우 캐시 서버 설정에 따라서 프록시에서 캐시 데이터를 반환할 수 있음 Error or 200 OK (오류 보다는 오래된 데이터라도 보여주자)
must-revalidate의 경우에 원 서버에 접근할 수 없는 경우 항상 오류가 발생해야 한다 504Gateway Timeout(매우 중요한 돈과 관련된 결과로 생각해보자)

#### 출처

- https://www.inflearn.com/course/http-%EC%9B%B9-%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC
