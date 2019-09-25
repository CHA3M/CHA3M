---
title: ! "[iOS] frida 환경 구성"
date: 2019-08-15
tags:
  - chaem
  - iOS
  - frida
categories:
  - iOS
---

진행환경
- iphone5s (10.3.3)  
- Mac os (windows 에서도 가능)  

# frida를 이용해서 iOS 앱을 분석하기 위한 환경구성  

## 1. pip 설치  
- sudo easy_install pip  
- 위 명령어가 안된다면 다음 두줄 실행  
 > curl https://bootstrap.pypa.io/get-pip.py -o get-pip.py  
 > sudo python get-pip.py  

## 2. python3 설치  
- 설치여부 확인 : python3 --version  
- python3 설치 :  https://www.python.org/downloads/mac-osx/ 에 접속하여 최신 버전 다운로드 및 설치  

## 3. Mac에 frida 설치  
- pip3 install frida-tools  
- pip에서 권한 오류 발생 시, `$ /Applications/Python\ 3.7/Install\ Certificates.command` 프로그램 실행  

## 4. iPhone에 frida 설치  
- Cydia를 설치한 뒤, https://build.frida.re를 소스에 추가하여 Frida (64bits 임)또는 Frida for 32bits device 중 알맞은 환경의 패키지 다운로드  
- Cydia를 이용하여 frida를 설치하면 default로 `/usr/sbin/frida-server` 가 구동됨  
- Cydia 앱을 이용하지 않고 frida git(https://github.com/frida/frida/releases)에서 알맞은 바이너리를 다운받아 filezila로 아이폰에 넣어서 구동하는 방법도 있음 (권한 줘야함 `chmod 777 frida-server`) 
- iPhone5s의 경우 `frida-server-12.6.14-ios-arm64.xz` 64환경이었음  
- 바이너리 구동방법 : `./frida-server &` (&를 붙이면 빽그라운드에서 실행시킨다는 의미)  

## 5. frida 연결 확인
- iPhone에서 frida-server 구동 (`ps -ef | grep frida` 로 확인)  
- USB로 연결 (`frida-ls-devices`로 연결된 단말기 확인가능)
- PC에서 `frida-ps -U` 명령어 실행 시 프로세스들이 뜨면 된것임!  
- `frida -U 프로세스ID` 로 attach하여 사용하면됨 

```
C:\Users\chaem  
λ frida -U 5412  
     ____  
    / _  |   Frida 12.6.13 - A world-class dynamic instrumentation toolkit  
   | (_| |  
    > _  |   Commands:  
   /_/ |_|       help      -> Displays the help system  
   . . . .       object?   -> Display information about 'object'  
   . . . .       exit/quit -> Exit  
   . . . .  
   . . . .   More info at http://www.frida.re/docs/home/  
Waiting for USB device to appear...  
  
[Apple iPhone::PID::5412]->  
```
 - 앱패키지명 아는 방법 (frida-ps -Uai)

```
λ frida-ps -Uai
PID  Name                   Identifier
----  ---------------------  ---------------------------------------
3729  Cydia                  com.saurik.Cydia
393  Mail                   com.apple.mobilemail
```

*잘안될때 체크*
frida 버전 서로 맞는지 확인 / 최신버전인지 확인  
- 단말기 : frida-server --version   
- PC : frida --version  
- frida-server-12.6.14 이상버전 사용(?)  
  
내가 이 환경을 구성할 때 원래 frida-server-12.6.13 버전이었는데, 계속 frida-server에 pc가 접근하지 못하는 문제로 엄청 고생함...  
그러다가 바이너리를 새로 다운받아서 다시 첨부터 해보는데 갑자기 되서 당황스러웠음 (몇번 다시 첨부터 했었는데 안됐었음 ㅠㅠ)  
결국 정확한 원인은 못찾았는데, 환경을 구성하는동안 frida가 12.6.14버전으로 릴리즈된 것임!!!   
그래서 12.6.14 이상 버전부터 되게 된것이라고 믿고있음...  

