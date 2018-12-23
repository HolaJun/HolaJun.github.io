# 에러모음

> 필자가 겪은 오류들과 해결방법을 정리해보았다.

<br>

[TOC]

<br>

## java.net.BindException: Address already in use: JVM_Bind

```c
java.net.ConnectException: Connection timed out: connect
	at java.net.DualStackPlainSocketImpl.connect0(Native Method)
	at java.net.DualStackPlainSocketImpl.socketConnect(Unknown Source)
	at java.net.AbstractPlainSocketImpl.doConnect(Unknown Source)
	at java.net.AbstractPlainSocketImpl.connectToAddress(Unknown Source)
	at java.net.AbstractPlainSocketImpl.connect(Unknown Source)
	at java.net.PlainSocketImpl.connect(Unknown Source)
	at java.net.SocksSocketImpl.connect(Unknown Source)
	at java.net.Socket.connect(Unknown Source)
	at java.net.Socket.connect(Unknown Source)
	at java.net.Socket.<init>(Unknown Source)
	at java.net.Socket.<init>(Unknown Source)
	at client.SocketClient.ClientRun(SocketClient.java:32)
	at client.SocketClient.main(SocketClient.java:17)
java.lang.NullPointerException
	at client.SocketClient.ClientRun(SocketClient.java:52)
	at client.SocketClient.main(SocketClient.java:17)
```

#### 원인

- 포트번호 충돌

#### 해결 방법

- cmd → netstat -ano
- taskkill /f /pid "해당 포트번호의 PID"

<br>

## Java compiler level does not match the version of the installed Java project facet.	JHboard	Unknown	Faceted Project Problem (Java Version Mismatch)

#### 원인

- Project Facet 설정과 해당 프로젝트의 JDK 버전이 맞지 않음

#### 해결 방법

- 프로젝트 Preferences → Java 버전 수정



## Java Exception : Unsupported major.minor version 52.0 

#### 상황

- 클라이언트(JDK1.8)에서 작업후 서버(JDK1.7)에 배포하여 실행했는데 위와 같은 오류가 뜨면서 안됌.

#### 원인

- Java Version 52.0(JDK1.8) 버전에서 컴파일하고 이전 버전에서 실행할 때 발생

#### 해결 방법

- JDK 1.8 버전으로 설치해 실행

- J2SE 8   = Version 52

  J2SE 7   = Version 51

  J2SE 6.0 = Version 50

  J2SE 5.0 = Version 49

  JDK  1.4 = Version 48

  JDK  1.3 = Version 47

  JDK  1.2 = Version 46

  JDK  1.1 = Version 45

#### 기타

- 서버에 여러 버전의 JDK와 Tomcat 존재. 다양한 JDK와 Tomcat 사용을 위해 작업물을 배포할 Tomcat(8.5)의 service.bat 파일에서 JDK 경로를 직접 설정해주었으나, 기존 환경변수인 JAVA_HOME(JDK1.7) 버전만 참고함. 결국 JAVA_HOME 버전을 1.8로 변경 하였음.

<br>

## Jdbc-8026 :Invalid identifier

#### 상황

- SQL insert 작업중 오류

#### 원인

- Database Table 컬럼명이 일치하지 않음

#### 해결 방법

- 컬럼명 수정

<br>

## ASM ClassReader failed to parse class file - probably due to a new Java class file version that isn't supported yet

#### 상황

- Spring Framework로 웹게시판 만드는 도중 발생된 오류

#### 원인

- 사용중인 스프링 버전(3.2.4)이 자바(1.8) 클래스 파일의 구문 분석을 지원하지 않는 버전이었음.

#### 해결 방법

- Spring 버전을 3.2.9 이상으로 갱신함.



## Could not delete C:/spring/.metadata/.plugins/org.eclipse.wst.server.core/tmp0/wtpwebapps/HolaJun/WEB-INF/lib. May be locked by another process.

#### 상황

- 톰캣 Start가 안됨

#### 해결 방법

- Project Clean 이후 Tomcat Clean. 그리고 Start

<br>

## Cannot change version of project facet Dynamic Web Module to 2.5

#### 해결 방법

- pom.xml과 web.xml의 module version을 똑같이 맞춰줘야함

<br>

## Missing artifact javax.servlet:servlet-api:jar:4.0.0

#### 상황

- servlet-api의 3버전대를 4버전대로 마이그레이션하는 중 오류 발생.

#### 원인

- 기존의 servlet-api는 3버전대와 4버전대의 artifactId가 맞지 않아서 발생한 오류

#### 해결 방법

- 3버전대 artifactId의 servlet-api를 4버전대의 맞게 javax.servlet-api로 변경