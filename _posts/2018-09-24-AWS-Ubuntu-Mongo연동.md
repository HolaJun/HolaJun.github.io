## 1. AWS(ec2 ubuntu)에서의 MongoDB Setting

#### 1.1 MongoDB 설치

- sudo apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv  0C49F3730359A14518585931BC711F9BA15703C6
- echo "deb <http://repo.mongodb.org/apt/ubuntu> "$(lsb_release -sc)"/mongodb-org/3.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-3.0.list
- sudo apt-get update
- sudo apt-get install -y mongodb-org

#### 1.2 MongoDB 시작, 종료, 재시작

- sudo service mongod start
- sudo service mongod stop
- sudo service mongod restart

#### 1.3 MongoDB 제거

- sudo service mongod stop
- sudo apt-get purge mongodb-org*
- sudo rm -r /var/log/mongodb
- sudo rm -r /var/lib/mongodb

#### 1.4 MongoDB ROOT계정 생성

- use admin
- db.createUser( 
                                { user: "ROOT_ID", 
                                  pwd: "ROOT_PWD", 
                                  roles: ["root"] 
                                  } 
                               )
- db.createUser(
                                { user: "USER_NAME",
                                   pwd: "PASSWORD",
                                   roles: ["userAdminAnyDatabase",
                                               "dbAdminAnyDatabase",
                                               "dbAdminAnyDatabase"
                                              ]
                                  }
                               )

#### 1.5 MongoDB 일반 사용자 계정 생성

- use 데이터베이스명

- db.createUser(

  ​                          { user: "USER_NAME",

  ​			     pwd: "PASSWORD",

  ​			     roles: [ "dbAdmin",

  ​					 "readWrite" ]

  ​			   }

  ​			  )

#### 1.6 관리자&사용자 계정 삭제

- use admin 또는 use 데이터베이스명

- db.dropUser("USER_NAME")

  

## 2. AWS(ec ubuntu) Secure Setting

1.  AWS Management Console -> EC2 -> Security Groups -> EC2's Security Group -> Add Custom TCP Rule, 27017, AnyWhere 

2. 외부 접속 허용을 위해, 

   2-1. sudo /etc/mongod.conf

   2-2. bindIp: 0.0.0.0 으로 변경

   2-3. sudo service mongod restart

3. 관리자 계정 생성

4. sudo vi /etc/mongod.conf -> security의 authorization: enabled로 변경

5. sudo service mongod restart



## 3. Python3에서 AWS MongoDB 데이터 가져오기

```
import pymongo

username = 'rootid'
password = 'rootpw'

# Connect AWS-Mongo
conn = pymongo.MongoClient('mongodb://%s:%s@13.209.66.162' % (username, password))

# Select Database
db = conn.get_database('admin')

# Select Collection
collection = db.get_collection('testcoll')

docs = collection.find() # find()에 인자가 없으면 해당 컬렉션의 전체 데이터 조회. return type = cursor
print('전체 조회')
for result in docs:
    print(result)

docs = collection.find(
                        { "name" : "su" }
                      )
print("name이 su인 document")
for result in docs:
    print(result)
```

- environment: Pycharm 2018.1.14(Community), Pymongo, AWS ec2 Ubuntu



## 4. JAVA에서 AWS MongoDB 데이터 가져오기

```
package mongoConnTest;

import org.bson.Document;

import com.mongodb.MongoClient;
import com.mongodb.MongoClientURI;
import com.mongodb.client.FindIterable;
import com.mongodb.client.MongoDatabase;

public class mongoConn {
	public static void main(String args[]) {
		try {		
			MongoClient mongoClient = new MongoClient(
					  new MongoClientURI("mongodb://rootid:rootpw@13.209.66.162/admin")
					  // mongodb://ID:PASSWORD@IP/DATABASE
					);
			MongoDatabase db = mongoClient.getDatabase("admin");
//			Document course = new Document();
			FindIterable<Document> docs = db.getCollection("testcoll").find();
			for(Document d: docs){
				System.out.println(d);
			}
		} catch(Exception e) {
			System.out.println(e);
		}
	}
}
```
- environment: Eclipse Mars.2 Release (4.5.2), mongo-java-driver-3.8.0.jar