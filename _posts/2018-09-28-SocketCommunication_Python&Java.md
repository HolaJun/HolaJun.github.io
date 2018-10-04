# Python Server & Java Client 



### Python Server

```python
#-*- coding: utf-8 -*-

## server.py
import socket

def run_server(port=5555):
  host = ''
  with socket.socket(socket.AF_INET, socket.SOCK_STREAM) as s:
    s.bind((host, port))
    s.listen(1)
    conn, addr = s.accept()
    msg = conn.recv(1024)
    print("type: ", type(msg))
    print(msg.decode(encoding='utf-8'))
    #print(f'{msg.decode()}')
    conn.sendall(msg)
    conn.close()

if __name__ == '__main__':
  run_server()

```



### Java Client

```java
package sending;

import java.io.BufferedReader;
import java.io.IOException;
import java.io.InputStreamReader;
import java.io.PrintWriter;
import java.net.Socket;

/**
 * 1 대 1 소켓 통신 클라이언트 예제
 */
public class SocketSendTest2 {

    private Socket mSocket;

    private BufferedReader mIn;
    private PrintWriter mOut;

    public SocketSendTest2(String ip, int port) {
        try {
            // 서버에 요청 보내기
            mSocket = new Socket(ip, port);
            System.out.println(ip + " 연결됨");

            // 통로 뚫기
            mIn = new BufferedReader(
                    new InputStreamReader(mSocket.getInputStream()));
            mOut = new PrintWriter(mSocket.getOutputStream());

            // 메세지 전달
            mOut.println("hello i'm client");
            mOut.flush();

            // 응답 출력
            System.out.println(mIn.readLine());

        } catch (IOException e) {
            System.out.println(e.getMessage());
        } finally {
            // 소켓 닫기 (연결 끊기)
            try {
                mSocket.close();
            } catch (IOException e) {
                System.out.println(e.getMessage());
            }
        }
    }

    public static void main(String[] args) {

        // 연결할 서버 IP
        String ip = "";
        int port = 5555;
        
        new SocketSendTest2(ip, port);
    }
}
```



### Android Client



##### MainActiity.java

```java
package com.example.holajun.sockettest;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;

import java.io.BufferedWriter;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.OutputStreamWriter;
import java.net.Socket;


public class MainActivity extends AppCompatActivity {
    EditText input01;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        input01 = (EditText) findViewById(R.id.input01);

        Button button01 = (Button) findViewById(R.id.button01);
        button01.setOnClickListener(new View.OnClickListener() {
            public void onClick(View v) {
                String addr = input01.getText().toString().trim();

                ConnectThread thread = new ConnectThread(addr);
                thread.start();
            }
        });

    }

    class ConnectThread extends Thread {
        String hostname;

        public ConnectThread(String addr) {
            hostname = addr;
        }
        public void run() {
            try {
                String str = "helloworld";
                int port = 5555;
                Socket sock = new Socket(hostname, port);

                BufferedWriter bw = new BufferedWriter(new OutputStreamWriter(sock.getOutputStream(), "UTF-8"));
                bw.write(str + "\n");
                bw.flush();

                /*
                ObjectOutputStream outstream = new ObjectOutputStream(sock.getOutputStream());
                outstream.writeObject("Hello AndroidTown on Android");
                outstream.flush();

                ObjectInputStream instream = new ObjectInputStream(sock.getInputStream());
                String obj = (String) instream.readObject();

                Log.d("MAinActivity", "서버에서 받은 메시지: " + obj);
*/
                sock.close();

            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }
    }
}
```



##### Manifest.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<manifest xmlns:android="http://schemas.android.com/apk/res/android"
    package="com.example.holajun.sockettest">

    <uses-permission android:name="android.permission.INTERNET"/>

    <application
        android:allowBackup="true"
        android:icon="@mipmap/ic_launcher"
        android:label="@string/app_name"
        android:roundIcon="@mipmap/ic_launcher_round"
        android:supportsRtl="true"
        android:theme="@style/AppTheme">
        <activity android:name=".MainActivity">
            <intent-filter>
                <action android:name="android.intent.action.MAIN" />

                <category android:name="android.intent.category.LAUNCHER" />
            </intent-filter>
        </activity>
    </application>

</manifest>
```



##### activity_main.xml

```xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:app="http://schemas.android.com/apk/res-auto"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context="com.example.holajun.sockettest.MainActivity">

    <TextView
        android:id="@+id/text01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true"
        android:layout_centerVertical="true"
        android:text="Click the button to connect the socket. Please check the log."
        tools:context=".MainActivity"/>

    <Button
        android:id="@+id/button01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true"
        android:layout_marginTop="20dp"
        android:layout_below="@+id/text01"
        android:text="Socket connection"
        android:textSize="20dp"
        android:textStyle="bold"
        tools:context=".MainActivity" />

    <EditText
        android:id="@+id/input01"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true"
        android:layout_marginTop="20dp"
        android:layout_below="@+id/button01"
        android:hint="연결할 소켓 서버 IP"
        android:text="000.000.000.00"
        android:textSize="16dp"
        android:textStyle="bold"
        tools:context=".MainActivity" />

</LinearLayout>
```



## 기타 TIP

- 어제까지 잘 되던 소켓통신이 오늘 갑자기 안될 때, 공유기 포트포워딩 IP주소 확인(변경되었을 가능성 높음)
- 환경설정은 역시 윈도우보다 리눅스가 좋다