# Python Server & Java Client 



## Python Server

```python
#-*- coding: utf-8 -*-#

import socket
import pymongo
import json

sendMsgClient = ''
result = ''

'''
    MongoDB Connect
'''
def mongoConn(fromClientMsg):
    # MongoDB 정보
    username = 'hoseo_io'
    password = 'hoseo93'

    # Connect AWS-Mongo
    # 도경서버
    #conn = pymongo.MongoClient('mongodb://%s:%s@222.118.242.165:1993' % (username, password))
    # 아산 자취방 서버
    #conn = pymongo.MongoClient('mongodb://%s:%s@121.127.181.101:27017' % (username, password))
    # 정훈서버
    #conn = pymongo.MongoClient('mongodb://%s:%s@124.28.41.13:27017' % (username, password))
    conn = pymongo.MongoClient('mongodb://%s:%s@127.0.0.1:27017' % (username, password))
    print('[Log] conn: ', conn)

    # Show Database
    db = conn.list_database_names()
    print('[Log] DB List: ', db)

    # Select Database
    db = conn.get_database('stock')
    print('[Log] Selected DB: ', db)

    # Show Colletction
    showColl = db.list_collection_names()
    print('[Log] Collection List: ', showColl)

    # Select Collection
    collection = db.get_collection('stock_list')
    print('[Log] Selected Collection: ', collection)

    # find()에 인자가 없으면 해당 컬렉션의 전체 데이터 조회. return type = cursor
    search = fromClientMsg

    docs = list(collection.find({"item" : search }))

    global result
    print('\n MongoDB 검색 결과')
    for result in docs:
        print(result)
        print('reuslt type: ', type(result))

    if not docs:  # 결과값이 없다면
        print("[Log] {0}에 대한 검색 결과가 없습니다.".format(search))
        result = 'noValue'
        return result
    else:
        del result['_id']

    print('[Log] docs: {0}, type of docs: {1} '.format(docs, type(docs)))

    #endMsgClient = '종목명: {0}\n 시작가: {1}\n 최저가: {2}\n 종가: {3}\n 거래량: {4}'.format(result['item'], result['open'], result['low'], result['close'], result['volume'])
    sendMsgClient = result
    print('sendMsgClient: ', sendMsgClient)

    return result

    #docs = collection.find(
    #                        { "name" : "su" }
    #                      )
    #print("name이 su인 document")
    #for result in docs:
    #    print(result)

def toJson(content):
    result = json.dumps(content)
    result = bytes(result, encoding='utf-8')
    return result

####################################
####   Socket Communication
####################################

def runServer():
    host = ''  # 지정하지 않으면 가능한 모든 인터페이스를 뜻함
    port = 5555
    #msg3 = bytes(jsonString, encoding='utf-8')
    #msgClientFinal = bytes(sendMsgClient, encoding='utf-8')
    conn = None

    # AF_INET=IPv4, AF_INET6=IPv6, SOCK_STREAM=TCP
    sock = socket.socket(socket.AF_INET, socket.SOCK_STREAM)
    sock.bind((host, port))
    print("[Log] server host={0}, server port={1}".format(host, port))
    sock.listen(1) # 접속이 있을 때까지 기다림

    while True:
        # if conn is None:
        # 클라이언트측으로부터 소켓 정보 받아옴
        print('[Log] [연결을 기다리는 중...]')
        # 접속 승인
        conn, addr = sock.accept()
        print('[Log] [연결 완료]')
        print('[Log] [Conn 정보] ')
        print('[Log] [클라이언트 정보] ', addr)
        print('[Log] 데이터 수신 대기중 ....')

        # 클라이언트로부터 받은 메세지
        # 안드로이드에서 데이터 전송받은 경우 맨 뒤에 \n이 붙어서 옴.
        fromClientMsg = conn.recv(1024).decode(encoding='utf-8').replace('\n', '')

        # 전송받은 메세지 출력
        print('[Log] 클라이언트로부터 받은 메세지:', fromClientMsg)
        print('[Log] 메세지크기: ', len(fromClientMsg))
        print('[Log] 메세지타입:', type(fromClientMsg))

        # mongoConn 함수에 명령어 넣고 결과값 bytes 파일을 return 받음
        resultValue = mongoConn(fromClientMsg)
        if resultValue == 'noValue':
            toClientMsg = '데이터가 존재하지 않습니다.'
            conn.sendall(bytes(toClientMsg, encoding='utf-8'))
            continue
        print('[Log] resultVluae: ', resultValue)

        # 클라이언트측으로 메세지 전송
        # msg = {'item': '우진', 'open': 5350.0, 'low': 5350.0, 'close': 5700.0, 'volume': 612028.0}
        # print('msg의 타입: ', type(resultValue))
        print('toJson() 이전의 resultValue 값: {0}, type: {1} '.format(resultValue, type(resultValue)))
        toClientMsg = toJson(resultValue)
        conn.sendall(toClientMsg)
        print('[Log] 클라이언트로 보낸 메세지: ', toClientMsg)
        print('[Log] toClient Type: ', type(toClientMsg))
        # if not fromClientMsg: break

    conn.close()

if __name__ == '__main__':
    runServer()
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
package com.example.holajun.projstock;

import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.EditText;
import android.widget.TextView;

import com.google.gson.JsonArray;
import com.google.gson.JsonElement;
import com.google.gson.JsonObject;
import com.google.gson.JsonParser;

import java.io.BufferedReader;
import java.io.BufferedWriter;
import java.io.InputStreamReader;
import java.io.ObjectInputStream;
import java.io.ObjectOutputStream;
import java.io.OutputStreamWriter;
import java.net.Socket;
import java.net.URLDecoder;
import java.nio.charset.Charset;

public class MainActivity extends AppCompatActivity {
    final String severIp = "121.127.181.101";
    final int serverPort = 5555;
    EditText txtId;
    TextView resultView;

    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);

        txtId = (EditText) findViewById(R.id.txtId);
        resultView = (TextView) findViewById(R.id.resultView);
        Button btnSearch = (Button) findViewById(R.id.btnSearch);

        btnSearch.setOnClickListener(new View.OnClickListener() {
            public void onClick(View v) {
                // addr = ip주소
                //String addr = severIp.getText().toString().trim();
                System.out.println("▣ ip값: " + severIp);
                System.out.println("▣ txtId값: " + txtId.getText().toString());

                ConnectThread thread = new ConnectThread(severIp, txtId.getText().toString());
                thread.start();
            }
        });
    }

    class ConnectThread extends Thread {
        String hostname, message;

        public ConnectThread(String severIp, String txtId) {
            hostname = severIp;
            message = txtId;
        }

        public void run() {
            try {
                String str = message.trim();
                Socket sock = new Socket(hostname, serverPort);

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
                String result = URLDecoder.decode(jsonVar, "utf-8");

//                jsonVar = "[" + jsonVar + "]";
                System.out.println("▣ jsonVar Decode: " + result);
                System.out.println("▣ 서버로부터 받은 메세지: " + jsonVar);
                System.out.println("jsonVar: " + jsonVar);

                JsonParser jsonParser = new JsonParser();
                JsonObject jsonObject = (JsonObject) jsonParser.parse(jsonVar);

                JsonElement stockDate = jsonObject.get("date");
                JsonElement stockItem = jsonObject.get("item");
                JsonElement stockOpen = jsonObject.get("open");
                JsonElement stockLow = jsonObject.get("low");
                JsonElement stockClose = jsonObject.get("close");
                JsonElement stockVolume = jsonObject.get("volume");

                String strStockDate = jsonObject.get("date").getAsString();
                String strStockItem = jsonObject.get("item").getAsString();
                String strStockOpen = jsonObject.get("open").getAsString();
                String strStockLow = jsonObject.get("low").getAsString();
                String strStockClose = jsonObject.get("close").getAsString();
                String strStockVolume = jsonObject.get("volume").getAsString();


                System.out.println("▣ Json date : " + stockDate);
                System.out.println("▣ Json item : " + stockItem);
                System.out.println("▣ Json open : " + stockOpen);
                System.out.println("▣ Json low : " + stockLow);
                System.out.println("▣ Json close : " + stockClose);
                System.out.println("▣ Json volume : " + stockVolume);

                resultView.setText(
                        strStockDate + "\n" +
                        strStockItem + "\n" +
                        strStockOpen + "\n" +
                        strStockLow + "\n" +
                        strStockClose + "\n" +
                        strStockVolume + "\n"
                );

//
//                JsonArray jsonArray = (JsonArray) jsonParser.parse(jsonVar);
//
//                for (int i = 0; i < jsonArray.size(); i++) {
//                    JsonObject object = (JsonObject) jsonArray.get(i);
//                    String id = object.get("id").getAsString();
//                    String name = object.get("name").getAsString();
//
//                    System.out.println("[JSON]id: " + id);
//                    System.out.println("[JSON]name: " + name);
//                }

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