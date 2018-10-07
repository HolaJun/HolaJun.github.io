# Python Server & Java Client 



## Python Server

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



## Java Client

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



## Android Client



### MainActiity.java

```java
package com.example.holajun.sockettest;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;

import com.google.gson.JsonArray;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.InputStreamReader;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.OutputStreamWriter;
import java.net.Socket;



public class MainActivity extends AppCompatActivity {
    EditText input01;
    TextView resultView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        input01 = (EditText) findViewById(R.id.input01);
        Button button01 = (Button) findViewById(R.id.button01);

        button01.setOnClickListener(new View.OnClickListener() {
            public void onClick(View v) {

                // addr = ip주소
                String addr = input01.getText().toString().trim();
                System.out.println("▣ ip값: " + addr);

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
                String str = "[Client Send] helloworld";
                int port = 5555;
                Socket sock = new Socket(hostname, port);

                // 서버로 데이터 보내기
                BufferedWriter bw = new BufferedWriter(
                        new OutputStreamWriter(sock.getOutputStream(), "UTF-8")
                );
                bw.write(str + "\n");
                bw.flush();

                // 서버로부터 데이터 받기
                BufferedReader mIn = new BufferedReader(
                        new InputStreamReader(sock.getInputStream(), "UTF-8")
                );
                String jsonVar = mIn.readLine();
                jsonVar = "[" + jsonVar + "]";

                System.out.println("▣ 서버로부터 받은 메세지: " + jsonVar);

                System.out.println("jsonVar: " + jsonVar);

                JsonParser jsonParser = new JsonParser();
                JsonArray jsonArray = (JsonArray) jsonParser.parse(jsonVar);

                for(int i=0; i<jsonArray.size(); i++) {
                    JsonObject object = (JsonObject) jsonArray.get(i);
                    String id = object.get("id").getAsString();
                    String name = object.get("name").getAsString();

                    System.out.println("[JSON]id: " + id);
                    System.out.println("[JSON]name: " + name);
                }

//
//                ObjectInputStream inputStream = new ObjectInputStream(sock.getInputStream());
//                Object input = inputStream.readObject();
//                System.out.println("▣ 서버로부터 받은 메세지(ObjectInputStream): " + input);

//                inputStream.close();


                sock.close();
            } catch (Exception ex) {
                ex.printStackTrace();
            }
        }
    }
}
```



### Manifest.xml

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



### activity_main.xml

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



## Gradle

```
dependencies {
    implementation fileTree(include: ['*.jar'], dir: 'libs')
    implementation 'com.android.support:appcompat-v7:28.0.0'
    implementation 'com.android.support.constraint:constraint-layout:1.1.3'
    testImplementation 'junit:junit:4.12'
    androidTestImplementation 'com.android.support.test:runner:1.0.2'
    androidTestImplementation 'com.android.support.test.espresso:espresso-core:3.0.2'
    implementation group: 'com.google.code.gson', name: 'gson', version: '2.8.0'
}

```



## 기타 TIP

- 어제까지 잘 되던 소켓통신이 오늘 갑자기 안될 때, 공유기 포트포워딩 IP주소 확인(변경되었을 가능성 높음)
- 환경설정은 역시 윈도우보다 리눅스가 좋다