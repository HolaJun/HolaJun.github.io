# Amazon EC2 Ubuntu SSH 연결(Windows)

<br>

### 필요한 것

1. EC2 Ubuntu 인스턴트
2. Putty
3. Puttygen

<br>

### 1. PuttyGen 사용하여 Private Key 변환

> EC2에서 생성된 프라이빗 키(.pem)을 Putty에서 사용할 수 있도록 Puttygen을 이용해 .ppk 파일로 변환해야함.

1. Puttygen Start → All programs → Putty → Puttygen
2. Type of key to generate → RSA (이전 버전의 Puttygen의 경우 SSH-2 RSA 선택)
3. Load → 해당 pem 파일 선택
4. Save private key → ppk 파일 생성

<br>

### 2. Putty Setting

1. Category → Session

   1. Host Name → user_name@public_DNS(IPv4)

      - **user_name 목록**

          ```
          Amazon Linux2 or Amazon Linux AMI → ec2-user
          Centos AMI → centos
          Debian AMI → admin or root
          Fedora AMI → ec2-user or fedora
          RHEL AMI → ec2-user or root
          SUSE AMI → ec2-user or root
          Ubuntu AMI → ubuntu
          ```

   2. Port 22 확인

2. Category → Connection → SSH → Auth

   - Private key file for authentication → Browse → .ppk 파일 선택 



### Linux 인스턴스로 원격 파일 전송(PSCP)

- CMD에서.

```
pscp -i C:\path\my-key-pair.ppk C:\path\Sample_file.txt ec2-user@public_dns:/home/ec2-user/Sample_file.txt
```

