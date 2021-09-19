[TOC]



# 03 HTTP 메시지

- 메시지가 어떻게 흘러가는가
- HTTP 메시지의 세 부분(시작줄, 헤더, 개체 본문)
- 요청과 응답 메시지의 차이
- 요청 메시지가 지원하는 여러 기능(메소드)들
- 응답 메시지가 반환하는 여러 상태 코드들
- 여러 HTTP 헤더들은 무슨 일을 하는가.



## 3.1 메시지의 흐름

- HTTP 메시지(데이터들의 블록)는 클라이언트, 서버, 프록시 사이를 흐른다.
- '인바운드' '아웃바운드' '업스트림' '다운스트림'은 메시지의 방향을 의미하는 용어다.







### 3.1.1 메시지는 원 서버 방향을 인바운드로 하여 송신된다.

- 인바운드와 아웃바운드는 트랜잭션 방향을 표현하기 위한 용어
  - 인바운드 :  클라이언트 -> 서버
  - 아웃바운드 : 모든 처리가 끝난뒤 메시지가 사용자 에이전트로 돌아오는 것



### 3.1.2 다운스트림으로 흐르는 메시지

- HTTP 메시지는 강물과 같이 흐른다.
- 요청메시지냐 응답 메시지냐에 관계없이 모든 메시지는 다운스트림으로 흐른다.

- 업스트림이냐 다운스트림이냐는 발송자와 수신자에 관한 이야기 이다.
  - 발송자 입장에서는 업스트림, 수신자 입장에서는 다운스트림이다.

```text
Networking is all about the flow of data which is generally referred as network traffic. Large amounts of data flow in the network fragmented in small pieces called packets. Just like a river, a network also has “Upstream” and “Downstream” which mean the data would be following in a similar way. However, a direct comparison would incorrect because when applying these terms to networks, it is important to understand that it is all about perspective.

Understanding the intended perspective may be the most challenging aspect to understand the intent of the entity delivering the message.

The term “Upstream” and “Downstream” are very relative in its roots. Consider yourself throwing a ball to your friend. For you the ball is moving away from you, however, for your friend the ball is moving towards him. You can consider the ball moving upstream, whereas for your friend the ball is moving downstream. Similarly, in a network, upstream data for one device could be downstream for another device. The data packets are bidirectional in nature. The packets keep “flowing” in the network between two or more points. The packets sent by one network are the packets received by another. Consider yourself downloading a file from a device. The packets you download will be travelling downstream in relation to you. But with respect to the device it would be sending data upstream.
    
출처 : https://www.psychz.net/client/question/ko/what-is-the-difference-between-upstream-and-downstream-in-networking.html
```





## 3.2 메시지의 각 부분

- HTTP 메시지는 단순한, 데이터의 구조화된 블록이다.
  - 시작줄
  - 헤더 (+ CRLF)
  - 본문

- 헤더에 생각보다 많은 정보가 담아져 있다.





### 메시지 문법

- 요청메시지 (request)

```
<메소드> <요청 URL> <버전>
<헤더>
CRLF
<엔티티 본문>
```



- 응답메시지(response)

```
<버전> <상태코드> <사유 구절>
<헤더>
CRLF
<엔티티 본문
```



- 메소드 
  - 클라이언트 측에서 서버가 리소스에 대해 수행해주길 바라는 동작
  - GET
    - 가장 흔히 쓰이는 메소드
    - 서버에게 리소스를 달라고 요청하기 위해 사용
  - HEAD
    - GET처럼 행동하지만, 서버는 응답으로 헤더만을 돌려줌
    - 엔티티 본문은 반환되지 않음
    - 리소스를 가져오지 않은 채 정보를 알 수 있음
    - 상태코드를 통해 개체의 존재 여부 확인 가능
    - 헤더를 통해 리소스 변경 검사 가능
  - PUT
    - 서버가 요청의 본문을 가지고 요청 ULR의 이름대로 새 문서를 만들거나 이미 존재한다면 교체하는 작업을 한다.
  - POST
    - 서버에 입력 데이터를 전송하기 위해 설계됨
    - PUT은 리소스에 데이터를 입력하는 것이다 (차이점)
  - TRACE
    - 메시지가 프록시를 거쳐 서버에 도착하는 과정을 추적함
    - 본인의 요청이 어떻게 전송되는지 확인 가능 )진단을 위해 사용)
  - OPTIONS
    - 어떤 메소드가 지원되는지 물어보는 요청
  - DELETE
    - 리소스 삭제 요청
  - 확장 메소드들
    - LOCK : 리소스 잠금
    - MKCOL : 문서 생성
    - COPY : 서버의 리소스 복사
    - MOVE : 서버의 리소스 이동



- 요청 URL
  - 요청 대상이 되는 리소스를 지칭하는 완전한 URL 혹은 URL 경로 구성



- 버전
  - HTTP 버전
  - HTTP/<메이저>.<마이너>



#### **상태코드**

- 상태코드
  - 요청이 어떻게 이루어졌는지 알려주는 정보
  - 100 - 199 : 정보성 상태코드
    - 100 Continue : 요청의 시작 부분 일부가 받아들여졌으며, 클라이언트는 나머지를 계속해							서 보내야 한다는 의미
    - 101 Switching Protocols : 클라이언트가 Upgrade 헤더에 나열한 것 중 하나로 서버가 												프로토콜을 바꾸었음을 의미
  - 200 - 299 : 성공 상태 코드
    - **200** OK : 요청 정상, 엔티티 본문은 요청된 리소스를 포함함
    - **201** Created : 무언가 생성하라는 요청에 대한 응답 (ex POST)
    - **202** Accepted : 요청은 받아들여졌으나 서버가 아직 동작 수행을 하지 않음
    - 203 Non-Authoritative Information
    - **204** No Content : 반환할 값이 없는 경우에 사용 (ex) save 같은 경우)
    - 205 Reset Content
    - 206 Partial Content
  - 300 - 399 : 리다이렉션 상태 코드
    - 300 Multiple Choices :클라이언트가 여러 리소스를 요청했을 때 리소스 목록과 함께 반환
    - 301 Moved Permanently : 요청한 URL이 옮겨 졌을 때 사용, URL이 어디있는지도 반환
    - 302 Found : 301과 같음
    - 303 See Other
    - **304** Not Modified : 수정과 관련된 조건 부 요청이 이루어졌을 때, 수정되지 않은 경우에 사용
    - 305 Use Proxy
    - 307 Temporary Redirect
  - 400 - 499 : 클라이언트 에러 상태 코드
    - **400** Bad Request : 클라이언트가 잘못된 요청을 보냈음을 알려줌
    - **401** Unauthorized : 인증이 필요하다고 알려줌
    - 402 Payment Required
    - **403** Forbidden : 요청이 서버에 의해 거부되었음
    - **404** Not Found : 요청한 URL을 찾을 수 없음
    - 405 Method Not Allowed : 지원하지 않는 메소드로 요청 했을 때
    - 406 Not Acceptable
    - 407 Proxy Authentication Required
    - 408 Request Timeout  : 작업시간이 너무 오래걸릴 경우
    - 409 Conflict
    - 410 Gone
    - 411 Length Required
    - 412 Precondition Failed
    - 413 Request Entity Too Large
    - 414 Request URI Too Long
    - 415 Unsupported Media Type
    - 415 Requested Range Not Satisfiable
    - 417 Expectation Failed
    - 422 Unprocessable Entity
  - 500 - 599 : 서버 에러 상태 코드
    - **500 Internal Server Error** : 서버 내부의 문제로 오류발생
    - 501 Not Implemented
    - 502 Bad Gateway
    - **503 Service Unavailable** : 서버가 잠시동안 요청을 처리할 수 없음
    - 504 Gateway Timeout
    - 505 HTTP Version Not Supported



- 사유 구절
  - 숫자로 된 상태코드의 의미를 사람이 이해할 수 있게 설명해주는 짦은 문구



#### **헤더**

- 헤더들

  - 요청이나 응답에 대한 정보들
  - CRLF로 끝나는 것이 포인트

  

- 일반 헤더(General Headers) : 메시지 전체에 적용되는 정보
  - Date : 메시지 생성 날짜 및 시간
  - Via : 메시지가 거쳐간 중개자(프록시, 게이트 웨이)



- 요청 헤더(Request Headers) : 요청 정보

  - From : 클라이언트 사용자의 메일 주소

  - Host : 요청의 대상이 되는 서버의 호스트 명과 포트

  - User-Agent : 요청을 보낸 어플리케이션의 이름(웹브라우저 정보 등)

  - **Accpet** 관련 요청 헤더 : 클라이언트가 선호하는 표현 요청

    - Accept : 클라이언트가 선호하는 미디어 타입 전달
    - Accept-Charset : 클라이언트가 선호하는 문자 인코딩
    - Accept-Encoding : 클라이언트가 선호하는 압축 인코딩
    - Accept-Language : 클라이언트가 선호하는 자연 언어

    

  - **조건부 요청 헤더**

    - Expect : 클라이언트가 요청에 필요한 서버의 행동을 열거할 수 있게 해준다.
    - If-Match : 문서의 엔티티 태그가 주어진 엔티티 태그와 일치하는 경우에만 문서를 가져온다.
    - If-None-Match : 문서의 엔티티 태그가 주어진 엔티티 태그와 일치하지 않는 경우에만 문서를 가져온다.
    - If-Modified-Since : 주어진 날짜 이후에 리소스가 변경 된 경우에만 리소스를 가져온다.
    - If-Unmodified-Since : 주어진 날짜 이후에 리소스가 변경되지 않은 경우에만 리소스를 가져온다.
    - If-Range : 문서의 특정 범위에 대한 요청을 할 수 있게 해준다.
    - Range : 서버가 범위 요청을 지원한다면, 리소스에 대한 특정 범위를 요청한다.

    

  - **요청 보안 헤더**

    - Authorization : 클라이언트가 서버에게 제공하는 인증 그 자체에 대한 정보
    - Cookie : 클라이언트가 서버에게 토큰을 전달할 때 사용한다.
    - Cookie2 : 요청자가 지원하는 쿠키의 버전을 알려줄 때 사용한다.

    

  - **프록시 요청 헤더**

    - Max-Forwards
    - Proxy-Authorization
    - Proxy-Connection



- 응답 헤더(Response Headers) : 응답 정보

  - Age : 응답에 걸린 시간

  - Retry-After : 현재 리소스가 사용 불가, 언제 다시 사용 가능한지에 대한 정보

  - Server : 서버 어플리케이션의 이름과 버전

  - Warning : 사유 구절에 잇는 것보다 더 자세한 경고 메시지

    

  - **협상 헤더**

    - Accept-Ranges : 서버가 자원에 대해 받아들일 수 있는 범위의 형태
    - Vary : 서버가 확인해봐야 할 헤더들의 목록

    

  - **응답 보안 헤더**

    - Proxy-Authenticate : Proxy에서 클라이언트로 보낸 인증요구의 목록
    - Set-Cookie : 서버가 클라이언트를 인증할 수 있도록 클라이언트 측에 토큰을 설정하기 위해 사용
    - WWW-Authenticate : 서버에서 클라이언트로 보낸 인증요구 목록



- 엔티티 헤더 (Entity Headers) :엔티티 본문(Body)에 대한 정보

  - Allow : 이 엔티티에 대해 수행될 수 있는 요청 메소드

  - Location : 클라이언트에게 엔티티가 어디에 위치하고 있는지 말해줌, 새로운 URL

    

  - **콘텐츠 헤더** : 엔티티 콘텐츠에 대한 구체적인 정보

    - Content-Base : 본문에서 사용 된 상대 URL을 계산하기 위한 Base URL
    - **Content-Encoding **: 본문에 적용 된 인코딩
    - **Content-Language** : 본문을 이해하기 위한 언어
    - **Content-Length** : 본문의 길이나 크기
    - Content-Location : 리소스의 실제 위치
    - Content-MD5 : 본문의 MD5 체크섬
    - Content-Range : 전체 리소스에서 이 엔티티가 해당하는 범위를 바이트 단위로 표현
    - **Content-Type** : 본문의 객체 종류

    

  - **엔티티 캐싱 헤더** : 언제 어떻게 캐시가 되어야 하는지에 대한 지시자 제공

    - ETag : 이 엔티티에 대한 엔티티 태그
    - Expires : 엔티티의 만료 일시
    - Last-Modified : 가장 최근 엔티티가 변경된 일시



- 확장 헤더(Extension Headers) : 비표준 헤더



- 엔티티 본문
  - 임의의 데이터 블록

