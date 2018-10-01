# BeagleBone Black

### 스펙

![bbb2](https://i.imgur.com/irxB6h8.png)

- Processor: TI AM335x (Sitaraprocessor) (CPU:ARM Cortex-A8, 64KB on-chip memory)
- 1GHz, 7 ADCs, 65 Digital pins: 3.3V, 512MB DDR3 RAM, 4GB eMMC, microSDslot, USB 2.0, microHDMI, Ethernet
- Interface: 8 PWMs, 4 UARTs, SPI, LCD, 2 CAN Buses



### USB to Serial (TTL) Debug

![image](https://i.imgur.com/ENwjziX.png)



### Minicom이란?

> 리눅스에서 작동하는 터미널 프로그램의 한 종류

- 주로 임베디드 보드와 PC간 시리얼 케이블로 연결해서 서로 시리얼 통신을 할 때 사용.
- 시리얼 통신으로 보내는 명령을 minicom을 통해 해결



### BBB 부팅 순서

- Flash Memory (eMMC vs microSD)
  1. eMMC 메모리에서 부팅
  2. eMMC에 OS 파일이 없으면 microSD에서 부팅
- 예외적으로, Boot 버튼 누른 상태에서 AC Adapter 연결시 microSD에서 부팅
  - 공장 출하시, eMMC 메모리에 Debian Linux 설치됨
    - eMMC → BBB Login
    - debian/temppwd
    - root



### LED 순서(hmce)

![image](https://i.imgur.com/xgvvVCU.png)

- USR0: HeartBeat
- USR1: microSD
- USR2: CPU
- USR3: eMMC

> SD카드를 꽂았을 때, LED0~2만 깜빡거림. LED3은 깜빡거리지 않아야 함.



### Booting 절차

1. BBB가 Bootloader를 읽어 실행.
   - MLO(Xloader 또는 SPL)를 읽어 실행.
   - MLO는 u-boot.img를 읽어들이는데 이 때, 설정파일 uEnv.txt를 읽어 부팅 옵션을 정함
2. Bootloader는 kernel을 읽어들임.
3. Kernel은 root file system을 읽어들임.

- Boot 버튼을 누른 상태로 전원 연결시 microSD에서 MLO를 읽어들임
- eMMC 메모리에 MLO가 없으면 Bootloader가 없는 것으로 간주됨.



### BBB Bootloader

- u-boot 사용(http://www.denx.de/wiki/U-Boot)
- MLO, u-boot.img, uEnv.txt로 구성됨(u-boot.img가 핵심파일)
- MLO는 X-Loader 또는 SPL(Second Program Loader)라고 부름.
- 부팅시 Space Bar 누르면 U-boot 프로그램 모드로 들어감.



### U-Boot 소스코드 다운로드

1. git clone git://git.denx.de/u-boot.git
2. cd u-boot
3. git checkout v2018.09-rc2 -b tmp
4. https://rcn-ee.com/repos/git/u-boot-patches/v2018.09-rc2/0001-am335x_evm-uEnv.txt-bootz-n-fixes.patch 다운로드 후 ~/u-boot 디렉터리로 복사
5. https://rcn-ee.com/repos/git/u-boot-patches/v2018.09-rc2/0002-U-Boot-BeagleBone-Cape-Manager.patch 다운로드 후 ~/u-boot 디렉터리로 복사
6. patch -p1 < 0001-am335x_evm-uEnv.txt-bootz-n-fixes.patch
7. patch -p1 < 0002-U-Boot-BeagleBone-Cape-Manager.patch



### Linaro Cross Compiler 설치

- https://releases.linaro.org/components/toolchain/binaries/5.3-2016.05/arm-linux-gnueabihf/gcc-linaro-5.3.1-2016.05-x86_64_arm-linux-gnueabihf.tar.xz 설치 후 압축해제

- 해당 디렉터리 이동

- export CC = ~/gcc-linaro-6.4.1-2018.05-x86_64_arm-linux-gnueabihf/bin/arm-linux-gnueabihf-

  ~/.bashrc 마지막 라인에 복사후 저장

- 테스트

  - ${CC}gcc -version 



### U-Boot 소스코드 컴파일

- Build(CC변수가 미리 선언되어있어야 함)
  - make ARCH=arm CROSS_COMPILE=$CC distclean
  - make ARCH=arm CROSS_COMPILE=$CC am335x_evm_defconfig
  - make ARCH=arm CROSS_COMPILE=$CC
- 위 컴파일 결과로 MLO, u-boot.img 파일 생성됨



### U-boot 설치

- microSD를 PC에 설치

- 컴파일된 MLO, u-boot.img 파일을 microSD로 복사

  ~/u-boot

  - sudo dd if=MLO of=/dev/sdX bs=128k seek=1 count=1
  - sudo dd if=u-boot.img of=/dev/sdX bs=384k seek=1 count=2

- uEnv.txt 파일

  - 최신 Richard C. Nelson의 Ubuntu Linux Image에는 이미 /boot/uEnv.txt 파일이 존재하므로 만들필요 없음.



### U-boot Download/Compile/Install

1. **Download & patch**
   - git clone git://git.denx.de/u-boot.git
   - cd u-boot
   - git checkout v2018.09-rc3 -b tmp
   - wget -c https://rcn-ee.com/repos/git/u-boot-patches/v2018.09/0001-am335x_evm-uEnv.txt-bootz-n-fixes.patch
   - https://rcn-ee.com/repos/git/u-boot-patches/v2018.09/0002-U-Boot-BeagleBone-Cape-Manager.patch
   - patch -p1 < 0001-am335x_evm-uEnv.txt-bootz-n-fixes.patch
   - patch -p1 < 0002-U-Boot-BeagleBone-Cape-Manager.patch
2. **Compile**
   - make ARCH=arm CROSS_COMPILE=$CC distclean
   - make ARCH=arm CROSS_COMPILE=$CC am335x_evm_defconfig
   - make ARCH=arm CROSS_COMPILE=$CC
3. **Install**
   - sudo dd if=MLO of=/dev/sdX bs=128k seek=1 count=1
   - sudo dd if=u-boot.img of=/dev/sdX bs=384k seek=1 count=2



### MicroSD 다루기

1. **Partiton**

   - sudo fdisk /dev/sdX

   - 명령어: m, n, p, w, t

   - 부팅 파티션: Boot type: FAT(LBA)(0xc), 

     나머지 파티션: ext4(0x83)

2. **Format**

   - sudo mkfs.vfat /dev/sdX1
   - sudo mkfs.ext4/dev/sdX2

3. **Mount**

   - sudo mount /dev/sdX2 /media/user/rootfs