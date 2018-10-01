# 1주차

1. **사용자 계정 생성**

2. **minicom 설치**

   - sudo apt-get install minicom

3. **minicom 설정**

   - sudo minicom -s
   - 115,200bps
   - 8N1(8 Data, No parity, Stop bit)

4. **ls /dev로 USB 포트 확인**

5. **minicom 실행**

   - sudo minicom

   

   # 2주차

   1. **lsblk**

      1-1. microSD 메모리를 PC에 장착

      1-2. 리눅스 터미널에서 lsblk 명령어 실행

      ​	mounting지점 및 sdX에서 X확인.

      ​	sda는 PC Linux의 하드디스크임.

   2. **/dev/ttyUSB0 포트 이용해 BBB연결(디버그 케이블 이용)**

      2-1. BBB를 디버그 케이블을 이용해 PC 연결

      2-2. sudo minicom

      2-3. Power adapter 연결

      2-4. debian/temppwd(또는 root/패스워드 없음)

      2-5. uname -r(커널 버전 확인)

   3. **PWR&RESET, LED 확인**

      3-1. Reset 버튼 누름

      3-2. 부팅시 메시지와 LED확인(hmce 순서)

      3-3. PWR 버튼을 눌러 BBB 종료

   4. **MicroSD 메모리에 우분투 리눅스 설치**

      4-1. http://www.rcn-ee.net/rootfs/2018-08-10/elinux/ubuntu-18.04.1-console-armhf-2018-08-10.tar.xz 다운로드 → 압축해제 및 해당 디렉터리(ubuntu) 이동

      4-2. miscroSD 장착후 lsblk 명령으로 /dev/sdX에서 X 찾아냄

      4-3. sudo ./setup_sdcard.sh -mmc /dev/sdX --dtb beaglebone

      4-4. 설치후 microSD를 PC에서 꺼내어 BBB에 장착 후 디버그케이블 연결

      4-5. sudo minicom

      4-6. Power Adapter 연결(eMMC에 Bootloader가 있을 경우 Boot버튼을 누른 상태에서 Power Adapter 연결하고 Boot 버튼을 뗀다)

      4-7. BBB 로그인(ubuntu/temppwd)

      4-8. uname -r 으로 BBB 커널 버전 확인



## 3주차

1. **U-boot bootloader 다운로드**

   1-1. git clone git://git.denx.de/u-boot.git

   1-2. cd u-boot

   1-3. git checkout -b tmp v2018-09-rc2

   1-4. https://rcn-ee.com/repos/git/u-boot-patches/v2018.09-rc2/0001-am335x_evm-uEnv.txt-bootz-n-fixes.patch 다운로드 후 ~/u-boot 디렉터리로 복사

   1-5. https://rcn-ee.com/repos/git/u-boot-patches/v2018.09-rc2/0002-U-Boot-BeagleBone-Cape-Manager.patch 다운로드 후 ~/u-boot 디렉터리로 복사

   1-6. 에러코드 수정작업(patch)

   ​	patch -p1 < 0001-am335x-evm-uEnv.txt-bootz-n-fixes.patch

   ​	patch -p1 < 0002-U-Boot-BeagleBone-Cape-Manager.patch

2. **git 사용**

   2-1. git --help

   2-2. git branch tmp1

   2-3. git checkout tmp1

   2-4. git checoout -b tmp2

   2-5. git checkout -b tmp3 v2018.09-rc2

   2-6. git tag

   2-7. git checkout v2018.09-rc2

   2-8. git branch

   2-9. git branch -a

   2-10. git branch -d tmp1

   2-11. git status

   2-12. export 한글 간편 안내서( http://rogerdudler.github.io/git-guide/index.ko.html )



## 4주차

1. **Linaro Cross Compiler: gcc 설치**

   1-1. https://releases.linaro.org/components/toolchain/binaries/5.3-2016.05/arm-linux-gnueabihf/gcc-linaro-5.3.1-2016.05-x86_64_arm-linux-gnueabihf.tar.xz 설치

   1-2. 압축해제 후 디렉터리를 홈 디렉터리로 이동

   1-3. export CC = ~/gcc-linaro-6.4.1-2018.05-x86_64_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-

   1-4. ${CC}gcc --version 테스트. Linaro GCC 글자가 보이면 잘 설치된 것.

   1-5. [1-3]을 ~/.bashrc 파일의 맨 밑에 복사하고 저장.

2. **U-boot bootloader 소스코드 컴파일**

   - 디렉터리 위치: ~/u-boot/

   2-1. make ARCH=arm CROSS_COMPILE=$CC distclean

   2-2. make ARCH=arm CROSS_COMPILE=$CC am335x_evm_defconfig

   2-3. make ARCH=arm CROSS_COMPILE=$CC

   - Your dtc is too old, please upgrade to dtc 1.4 or newer
     - 위 에러 발생시, sudo apt-get install device-tree-compiler 실행
     - Bison, flex를 설치할 수도 있음.

3. **U-boot을 MicroSD에 설치**

   - 디렉터리 위치: ~/u-boot/

   3-1. microSD를 PC에 장착

   3-2. lsblk 확인하여 /dev/sdX에 마운팅된 디렉터리 확인

   3-3. sudo dd if=MLO of=/dev/sdX bs=128k seek count=1

   3-4. sudo dd if=u-boot.img of=/dev/sdX bs=384k seek=1 count=2

4. **MicroSD에 새로 설치된 U-boot 이용하여 부팅하기**

   4-1. microSD를 BBB에 장착

   4-2. boot 버튼 누르고 power adapter 연결

   4-3. Boot 메시지 확인하여 u-boot 버전 확인(2018.09)

   4-4. U-boot SPL 글자로 시작하는 첫 부팅 메세지에서 컴파일 시간 확인

   4-5. 로그인 후 BBB 부팅 후 커널 버전 확인(uname -r)

5. **U-boot 소스코드 수정해서 부팅하기(고급)**

   5-1. ~/u-boot/common/spl/spl.c 에서 라인 577을 수정하여 개발자 영문 이름 넣기

   ​	컴파일하여 부트로더 재설치후 부팅해서 부팅메세지 확인

   5-2. ~/u-boot/env/env.c 에서 라인 188 수정하여 개발자 영문이름 넣기

   ​	컴파일하여 부트로더 재설치 후 부팅해서 부팅메세지 확인.



## 5주차

1. **Git 설정**

   1-1. git config --global user.name "HolaJun"

   1-2. git config --global user.email ljh_0107@naver.com

2. **Linux Kernel Source Code Download**

   2-1. git clone git://github.com/robertcnelson/bb-kermel.git

   2-2. cd bb-kernel

   2-3. git tag

   2-4. git checkout -b tmp9 4.18.9-bone9

   2-5. git show

3. **Kernel Build**

   3-1. Linaro Compiler가 설치되어 있는지 확인

   3-2. build_kernel.sh 파일을 수정

   ​	unset CC 라인을 찾아서 주석처리(#)

   3-3. ./build_kernel

4. **Kernel Install**

   - 디렉터리: ~/bb-kernel

   4-1. microSD를 PC에 장착

   4-2. lsblk 명령 실행 후 /dev/sdX에서 X 확인

   4-3. system.sh 파일 수정

   ​	MMC=/dev/sdX  찾아서 X 수정 및 주석(#) 제거 

   4-4. tools/install_kernel.sh (주의: /dev/sda, /dev/sdb가 절대 아니어야 함)

   4-5. microSD 카드를 BBB에 장착하고 부팅

   4-6. uname -r 명령어를 실행하여 버전 확인