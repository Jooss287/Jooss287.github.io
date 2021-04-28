---
title: 드라이버 디버깅 세팅하기
date: 2020-09-04 18:30:00 +0900
categories: [Language, C]
tags: [windows driver]     # TAG names should always be lowercase
toc: true
---

드라이버를 만들어서 설치를 하게 되면 다양한 문제에 봉착합니다.
바로 잘 만들어지면 최고의 결과겠지만 초보자의 단계에선 절대 그럴 수 없는듯 합니다.
WDK를 설치하면 같이 설치되는 WinDbg가 이미 드라이버 설치를 끝난 단계에서만 사용 가능한줄 알았으나 연결 해 놓으면 드라이버 설치 시에 생기는 문제도 알려 줍니다.

기본적으로 드라이버 개발 시에는 개발을 진행하는 PC에서 테스트를 하는 것이 아닌 원격 PC를 이용하여 아무것도 설치되지 않은 PC를 이용하여 진행합니다.  
드라이버를 설치 할 PC를 ```원격 PC```로, 개발을 진행하는 PC를 ```개발PC```로 명명하고 진행하겠습니다.

# WinDbg 연결 방법

## 원격 PC 설정

```원격 PC```에서 ```개발PC```의 접근권한을 허용하기 위해 Command 창을 관리자 권한으로 열어줍니다.

* 윈도우 버튼을 눌러 "CMD" 입력 후 ```Shift```+```Ctrl```+```Enter```
* 아래의 커맨드를 입력합니다.  

```bash
bcdedit /set debug on  
bcdedit /set testsigning on  
bcdedit /dbgsettings net hostip:{개발PC IP} port:50000 key:{a.b.c.d}  
```  

입력 시 Key의 값을 입력하지 않으면 임의의 키를 생성 해 줍니다.
보안이 필요한 Online 상에서 사용하지만 내부적으로 사용 시에는 직접 쉬운 키를 입력하여 사용합니다.
입력 시에는 .을 이용한 4개 파트를 적어주어야 합니다. Ex. abcd.bcd.ed.aaa (파트1.파트2.파트3.파트4)

## 개발 PC 설정

```Windbg```를 설정하기 앞서 MS사에서 제공하는 Symbol들을 저장할 폴더를 만들어야 합니다.  
예시로 아래와 같은 폴더를 생성하였습니다. 이 폴더는 Driver Debug를 하면서 필요한 자료이 저장이 됩니다.

```bash
C:\sym
C:\sym\websym
```

그 이후 ```WDK``` 를 설치하고 나면 시작에서 ```WinDbg```를 찾을 수 있습니다.

1. 운영체제 환경에 따라 32bit, 64bit를 선택하여 열어줍니다.
2. ```File```-```Symbol File Path```를 열어 아래의 명령어를 입력합니다.
3. ```C:\sym; srv*C:\sym\symbol*http://msdl.microsoft.com/download/symbols```
4. 작업을 완료하면 ```Save Workspace```를 눌러 작업을 저장합시다. ```Save Workspace As```를 통해 다른 특정 이름으로 저장이 가능합니다.
  
위의 주소는 ```Kernel Debug```창에서 Help를 눌러 ```symbol```을 검색하여 나오는 ```Microsoft public symbol server```에서 찾을 수 있습니다.

작업이 완료되었다면 ```File```-```Kernel Debug```에서 ```Port```와 ```원격PC```에서 설정한 ```Key```값을 입력하면 자동으로 연결이 시작됩니다.
이 때 ```원격PC```를 재부팅하면 드라이버의 생성 과정을 디버깅 할 수 있습니다.

## Windbg report

```WinDbg```를 켜 놓은 상태에서 드라이버 설치를 진행하다 문제가 생겼을 경우 아래와 같이 Report를 받을 수 있습니다.
![report debug message](/assets/img/20-09-04_DriverDebugging.png)

## Driver Debug Print

```bash
ed Kd_DEFAULT_MASK 8
```
