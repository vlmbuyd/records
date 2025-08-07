## 질문

> www.google.com를 주소창에 입력하면 무슨 일이 일어나는지 설명헤주세요.

<br>

## 용어 정리

- **웹 클라이언트 (Web Client)**

  사용자가 웹에 접근하는 프로그램을 말한다. <br>
  대부분의 웹은 브라우저에서 실행되기 때문에 `Chrome` 등 우리가 알고 있는 웹 브라우저들의 대부분을 일반적으로 웹 클라이언트라고 한다.

- **TCP / IP** (Trnasmission Control Protocol / Internet Protocol)

  Transmission Control Protocol (전송 제어 규약) 과 Internet Protocol (인터넷 규약) 은 인터넷에서 데이터를 주고받기 위한 표준 통신 규약이다.

  `TCP`는 서버와 클라이언트 간의 데이터를 **신뢰성** 있게 전달하기 위해 만들어진 프로토콜이다.

  `IP`는 `TCP/IP` 구조에서 컴퓨터를 식별하기 위해 사용되는 주소이다.

  즉, 송신사는 수신자의 `IP` 주소를 통해 데이터를 전송하고, `TCP`는 이 데이터가 오류 없이 도착했는지 확인하는 역할을 수행한다.

- **DNS** (Domain Name System Servers)

  실제 웹 주소는 `63.245.317.105`와 같은 `IP 주소` 형태이며, 인터넷에 있는 모든 url에는 각각의 고유한 IP 주소가 지정되어 있다. 이 IP 주소를 통해 해당 웹사이트를 호스팅하고 있는 서버 컴퓨터에 접근할 수 있다. <br>
  하지만 이러한 숫자 덩어리는 기억하기 쉽지 않으므로, 사용자가 기억하기 쉬운 도메인 네임(ex. www.google.com)을 입력하면, `DNS`는 이를 컴퓨터가 이해할 수 있는 `IP 주소` 형태로 변환해준다. 즉, DNS의 가장 큰 목적은 사람들에게 편리함을 주기 위해서인 것이다.<br>
  DNS 서버는 해당 도메인에 연결된 IP 주소를 찾아 클라이언트(브라우저)에 전달하며, 이를 통해 브라우저는 정확한 서버에 요청을 보낼 수 있다.

<br>

## 웹의 동작 원리 및 순서

웹의 동작 원리는 간단하게 한 문장으로 정리하면 다음과 같다.

**클라이언트가 서버에 요청을 보내면 서버는 해당 요청에 대해 응답한다.**

![](https://img1.daumcdn.net/thumb/R1280x0/?scode=mtistory2&fname=https%3A%2F%2Fblog.kakaocdn.net%2Fdna%2FbB27VM%2FbtqInsHdO5P%2FAAAAAAAAAAAAAAAAAAAAAL_OU37Oa5yLrfnltZjeUc9FzVTtFIqJf3HGpToLKIPi%2Fimg.png%3Fcredential%3DyqXZFxpELC7KVnFOS48ylbz2pIh7yKj8%26expires%3D1756652399%26allow_ip%3D%26allow_referer%3D%26signature%3DUU9qkFRA%252FSN9xdd0QX174lKMwwc%253D)

### 1. 사용자가 웹 브라우저 검색 창에 `www.google.com`를 입력한다.

### 2. 브라우저는 캐싱된 DNS 기록들을 통해 `www.google.com`에 대응되는 `IP` 주소가 있는지 확인한다.

웹사이트 이름을 검색하면 브라우저는 DNS 기록을 4가지 캐시에서 확인한다.

1. 브라우저 캐시

2. OS 캐시

3. router 캐시

4. ISP 캐시

네트워크 트래픽을 조절하고 데이터 전송 시간을 줄이기 위해 이렇게 다양한 캐시를 활용하여 저장해놓는다.

- 요청한 캐시가 있다면 해당 캐시된 IP 주소를 사용하여 DNS 요청을 생략하고 바로 서버에 요청을 보낸다. (=> 4단계로 이동) <br>

- 요청한 캐시가 없다면 다음 단계(3단계)로 넘어간다.

### 3. 요청한 URL이 캐시에 없으면, ISP의 DNS 서버가 `www.google.com`의 IP 주소를 찾기 위한 DNS query를 날린다.

특정 사이트에 접속하기 위해서는 해당 IP 주소를 알아야 한다. DNS query의 목적은 **여러 다른 DNS 서버들을 검색하여 해당 사이트의 IP 주소를 찾는 것**이다. 이러한 검색을 `recursive search`라고 부른다. IP 주소를 찾을 때까지 DNS 서버에서 다른 DNS 서버를 오가며 반복적으로 검색한다.

DNS recursor(DNS 서버)는 웹사이트의 **도메인 이름의 구조에 기반하여 검색을 수행**한다.

![](https://devjin-blog.com/static/df40acaf929fb371a270155e2ef49a36/72aae/browser_work_dns_2.png)

**도메인 이름 구조 기반 검색**

```
1. DNS recursor가 root name server에 연락
2. .com 도메인 name server로 리다이렉트
3. google.com name server로 리다이렉트
4. 최종적으로 DNS기록에서 'www.google.com' 에 매칭되는 IP주소 찾기
5. 찾은 주소를 DNS recursor로 보내기
```

이 모든 요청들은 작은 데이터 패킷들을 통하여 보내지고, 이 패킷 안에는 요청의 내용과 DNS recorsor의 IP 주소가 포함되어 있다.

### 4. 받은 IP 주소를 기반으로 브라우저와 서버가 TCP connection을 수행한다.

![](https://tcpschool.com/lectures/img_webbasic_10.png)

클라이언트와 서버 간의 데이터 패킷들이 오가기 위해서는 **TCP connection**이 되어야 한다. `TCP/IP three-way handshake`라는 프로세스를 통해서 클라이언트와 서버간의 연결이 이루어지게 된다.

### 5. 브라우저가 웹 서버에 HTTP 요청을 보낸다.

TCP 연결이 완료 되었다면, 클라이언트의 브라우저는 `GET`요청을 통해 서버에게 www.google.com 웹페이지(html 문서)를 요구한다.

### 6. WAS(웹 애플리케이션 서버)와 데이터베이스에서 웹페이지 관련 작업을 처리한다.

웹 서버 혼자서 모든 로직을 수행하고 데이터를 관리한다면 간단하겠지만, 서버에서 모든 일을 처리하게 되면 과부하가 일어날 수 있다. 따라서 `WAS`가 서버를 도와 일을 수행한다.

**WAS란?**

사용자 컴퓨터나 장치에 웹 애플리케이션을 수행해주는 미들웨어이다. 클라이언트에서 요청을 받으면 서버는 요청에 필요한 페이지의 로직이나 데이터베이스 연동을 위해 `WAS`에 이들의 처리를 요청한다. 그러면 `WAS`는 이 요청을 받아와 **동적인 페이지 처리를 담당(JSP, ASP, PHP)** 하고 DB에서 관련 데이터 정보를 받아온다. (웹서버는 주로 정적인 컨텐츠 html, css, image등을 요청받아 처리한다.) <br>
이렇게 **`WAS`는 DB와 연동하여 데이터를 처리한 뒤, 생성한 파일을 다시 서버에 반환**한다.

주방을 예로 들면, 웹서버는 조리실 안으로 들어오는 주문을 확인하고 역할을 분배하고, 그 아래에서 실제 요리를 하는 요리사들을 WAS라고 볼수 있다. WAS의 등장으로 인해 웹 서버의 할 일을 분배하여 서버의 부담을 줄일 수 있고, 빠르게 동적 컨텐츠를 처리할 수 있다.

### 7. 위의 작업 처리 결과를 웹 서버로 전송한다.

### 8. 웹 서버는 브라우저에 HTPP response를 전송한다.

`status code` 드 같은 다양한 정보를 담아 보낸다.

### 9. 웹 브라우저는 화면에 웹 페이지를 출력한다.

브라우저는 HTML content를 단계적으로 렌더링한다.

처음 HTML의 스켈레톤(기본 틀)을 렌더링한 후, HTML 태그들을 체크하고 나서 추가적으로 필요한 정적 파일들(이미지, CSS StyleSheet, Javascript 파일)을 GET 요청한다. 이 정적인 파일들은 브라우저에 의해 캐싱되어 이후 해당 페이지 방문 시에 서버 요청을 보내지 않도록 한다. 이후 www.google.com의 모습이 보이게 된다.

<br>
 
 ---

#### 래퍼런스

- https://devjin-blog.com/what-happen-browser-search/
- https://velog.io/@eassy/www.google.com%EC%9D%84-%EC%A3%BC%EC%86%8C%EC%B0%BD%EC%97%90%EC%84%9C-%EC%9E%85%EB%A0%A5%ED%95%98%EB%A9%B4-%EC%9D%BC%EC%96%B4%EB%82%98%EB%8A%94-%EC%9D%BC
- https://brunch.co.kr/@swimjiy/2
- https://all-young.tistory.com/21
- https://tcpschool.com/webbasic/works
- https://developer.mozilla.org/ko/docs/Learn_web_development/Getting_started/Web_standards/How_the_web_works
- https://mininkorea.tistory.com/38
