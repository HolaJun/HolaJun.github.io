# N-Tier Architecture

## 1-Tier Architecture

![infrastructure-01 (5K)](https://www.tonymarston.net/php-mysql/infrastructure-01.png)

- 클라이언트 컴퓨터에 3가지 로직을 전부 구현한 것
- 한 클라이언트 서버에서 모든 것을 지원함
- 새로운 컴퓨터를 사용하고자 할 경우 모두 새로 변경해야하는 단점 존재(한 가지 로직을 바꾸면 다른 로직의 변경도 필요)

<br>

## 2-Tier Architecture

![infrastructure-02 (8K)](https://www.tonymarston.net/php-mysql/infrastructure-02.png)

- Client Tier와 Data Tier로 2개의 물리적 컴퓨터로 구분
- 클라이언트와 서버를 분리하여 어플리케이션과 데이터베이스가 분리되어 있기 때문에 데이터베이스의 변경이 편리한 장점 가지고 있음.

<br>

## 3-Tier Architecture

![infrastructure-03 (8K)](https://www.tonymarston.net/php-mysql/infrastructure-03.png)

- 2-Tier 구조의 제한을 극복하기 위해 탄생한 구조
- 각 계층은 물리적으로도 독립적이며 각 계층의 변경이 다른 계층에 의존되지 않음.
- 비즈니스 로직을 완전히 분리하여 데이터베이스 시스템과 클라이언트 시스템 사이에 배치한 클라이언트 서버 시스템의 일종
  - 사용자와 데이터베이스간의 데이터 요구 서비스에 미들웨어를 이용하는 것을 예로 들 수 있음.
- 사용자 인터페이스 환경과 데이터베이스 관리서버 환경 사이의 중간층이 추가된 구조.
  - 중간층은 트랜잭션 처리 모니터, 메시지 서버, 응용 서버 등 여러가지 방법으로 구축 가능
  - 중간층이 큐로써 역할을 한다면 클라이언트는 자신의 요청 중간층에 전달만 하고 중간층이 서버에 접속해서 클라이언트가 남기고 간 요청에 대한 응답을 받아 클라이언트로 리턴함.
  - 스케줄링을 가능하게 하고, 다수 사용자 요구 처리에 대한 우선 순위를 정하룻 있게 해주어 서버 부하 줄여줌.
- 3개의 계층
  - **Presentation Tier**
    - 응용프로그램 최상위에 위치하며, 서로 다른 Tier에 있는 데이터 등과 커뮤니케이션을 함
    - 사용자 인터페이스 지원(브라우저의 정적데이터 처리)
    - GUI, Frontend로 불림
    - 비즈니스로직이나 데이터관리 코드를 포함해서는 안된다.
    - 주로 웹서버(WebServer)를 뜻함(HTML, JS, CSS, Image 등)
  - **Application Tier**
    - 비즈니스 로직 또는 트랜잭션 계층
    - 워크스테이션으로부터의 클라이언트 요청에 대해 마치 서버처럼 행동
    - 차례로 어떤 데이터가 필요한지를 결정하고, 메인프레임 컴퓨터상에 위치하고 있을 세번째 계층의 프로그램에 대해서는 마치 클라이언트처럼 작동
    - 정보처리의 규칙을 가지고 있음(동적 데이터 처리)
    - Middleware or Backend로 불림
    - 주로 어플리케이션 서버(WAS)를 뜻함(Java EE, ASP.NET, PHP 등)
  - **Data Tier**
    - 데이터베이스와 그것에 액세스해서 읽거나 쓰는 것을 관리하는 프로그램
    - 대규모 프로그램에서 일부분에 관해 생각하기에 편리한 방법
    - 주로 데이터베이스를 뜻함
    - DB or File System 접근
    - DB서버(Mysql, Oracle 등)을 뜻함.