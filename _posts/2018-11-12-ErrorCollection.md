# 에러모음

> 필자가 겪은 오류들과 해결방법을 정리해보았다.

<br>

[TOC]

### java.net.BindException: Address already in use: JVM_Bind

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

#### 해결 방법ㅇ

- cmd → netstat -ano
- taskkill /f /pid "해당 포트번호의 PID"