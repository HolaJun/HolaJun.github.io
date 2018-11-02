# WAS(Web Application Server)

### 정의

- HTTP를 통해 컴퓨터나 장치에 애플리케이션을 수행해주는 미들웨어(소프트웨어 엔진).
- 동적 서버 콘텐츠(jsp, php...)를 제공하기 위해 만들어진 애플리케이션 서비스. 
- 주로 데이터베이스 서버와 같이 서비스 수행.
- JSP, Servlet 구동 환경 제공
- 컨테이너(JSP, Servlet을 실행시킬 수 있는 소프트웨어), 웹컨테이너, 서블릿컨테이너라고도 칭함.
- html 만으로는 할 수 없는 데이터베이스 조회나, 다양한 로직처리같은 동적인 컨텐츠를 제공하기 위해 만들어진 어플리케이션 서버.
- 웹서버와 웹컨테이너의 결합.
- 다양한 기능을 컨테이너에 구현하여 다양한 역할을 수행할 수 있는 서버
- 웹 컨테이너
  - 클라이언트 요청이 있을 때 내부 프로그램을 통해 결과를 만들어내고, 이것을 다시 클라이언트에 전달하는 역할을 함
- WAS 역시 웹 서버의 기능을 수행할 수 있음.

### 주요 기능

1. 프로그램 실행 환경과 데이터베이스 접속 기능 제공
2. 여러 개의 트랜잭션 관리
3. 업무를 처리하는 비즈니스 로직 수행

### 종류

- Tomcat
- Web Logic
- Jeus
- JBoss

### 동작 과정

1. 웹서버로부터 요청이 오면 컨테이너가 받아서 처리

2. 컨테이너는 web.xml을 참조 

   → 해당 서블릿에 대한 쓰레드 생성 

   → httpServletRequest, httpServletResponse 객체 생성

3. 컨테이너는 서블릿 호출

4. 호출된 서블릿의 작업을 담당하는 쓰레드(2번에서 생성된 쓰레드)는 doPost() or doGet() 호출

5. 호출된 doPost() or doGet() 메소드는 생성된 동적 페이지를 Response 객체에 담아 컨테이너에 전달

6. 컨테이너는 전달받은 Response객체를 HTTPResponse형태로 바꿔 웹서버에 전달 

   → 생성되었던 쓰레드 종료 

   → httpServletRequest, httpServletResponse 객체 소멸

<br>

# Web Server

### 정의
- 웹브라우저와 같은 클라이언트로부터 HTTP 요청을 받아들이고, 정적 페이지(html, css, js, img 등)를 처리해 반환하는 프로그램.
- 클라이언트에서 요청이 올 때 가장 앞에서 요청에 대한 처리 수행.
- 클라이언트의 요청을 기다리고, 요청에 대한 데이터를 만들어서 응답하는 역할(정적 데이터)
- 인증, 정적 컨텐츠 관리, HTTPS 지원, 컨텐츠 압축, 가상 호스팅, 대용량 파일지원, 대역폭 쓰로들링 등의 기능지원

### 종류

- Apache
  - 소프트웨어 단체 이름
  - 오픈소스 프로젝트 커뮤니티에서 만든 http웹서버
    - http요청을 처리하는 웹서버
    - 정적 데이터(html, css, img 등) 처리
  - 클라이언트가 GET, POST, DELETE 등의 메소드를 이용해 요청시, 이 프로그램이 어떤 결과는 돌려주는 기능을 함.
- 인터넷 정보 서버(IIS)
- 엔터프라이즈 서버

### 장점

- www 뿐만 아니라 프린터, 라우터, 웹캠과 같은 임베디드 장치, 그 외 근거리 통신망에서도 사용
- 시스템의 모니터링 또는 장치 관리를 위한 목적으로도 사용.
- 클라이언트에 추가적인 소프트웨어의 설치 없이 대부분의 OS에 포함된 웹브라우저만으로 서비스 제공 가능.



# WAS와 Web Server 차이

> WAS(Web Application)은 정적 데이터를, Web Server는 동적 데이터를 처리함.

![img](https://t1.daumcdn.net/cfile/tistory/2649EF4358C5054514)

(출처: http://cybersecuritynews.co.uk/popular-web-application-attacks-and-recommendations/)

- WAS는 정적, 동적 처리 둘 다 가능하지만, 정적처리시 부하가 많이 걸려 비효율적.
- 톰캣(WAS)에는 아파치(WebServer)의 기능(웹서비스데몬, Httpd)를 포함.
- Web Server에 웹 문서를 처리하는 기능을 분배하여 서버의 부담을 줄일 수 있고, 이로인해 웹 애플리케이션에서 정적 데이터 처리를 위해 지연되는 시간이 줄어들어 동적 컨텐츠의 처리 속도가 빨라짐.
- Web 서버를 반드시 구축해야하는 것은 아님. 하지만, WAS는 동적 컨텐츠를 제공하기 위해 존재하는 서버이기에, 정적 컨텐츠 요청이 들어온다면 Web 서버보다 수행속도가 느릴 수 밖에 없다. 이 때문에 Web 서버를 앞에 두고, 필요한 WAS들을 Web 서버에 플러그인 형태로 설정하여 효율적으로 처리함.



### Apache와 Tomcat의 차이?

- WAS인 Tomcat만 쓰면 되지 왜 Webserver인 Apache를 따로 쓸까? → **두 서버의 목적이 다름** 
- Web Server는 정적인 데이터를 처리하는 서버.
- img, css, html와 같은 정적 리소스를 제공하는 서버는 WAS보다 Web Server를 이용하는 것이 훨씬 빠르고 안정적.
- WAS는 동적인 데이터를 처리하는 서버
  - DB와 연결되어 데이터를 주고받거나 프로그램으로 데이터 조작이 필요한 경우 WAS 활용.