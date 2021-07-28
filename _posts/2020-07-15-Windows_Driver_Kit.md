---
title: Windows Dirver 개발환경 세팅하기
date: 2020-07-15 18:30:00 +0900
categories: [Language, C]
tags: [windows driver]     # TAG names should always be lowercase
toc: true
---

windows driver는 하드웨어적으로 장비와 연결짓기 위해 kernel 단에서 활동하는 프로그램을 말합니다.
Microsoft에서는 driver를 만들기 위한 방법으로 WDK(windows Driver Kit)을 제공합니다.

Windows 10 1903 build를 기준으로 driver 개발 환경을 만들어 보도록 하겠습니다.

## Install

Dirver를 만들기 위해서는 SDK(Software Developement Kit)와 WDK 두개가 필요합니다.

우선 visual studio 2019를 설치하면서 c++ 워크로드에서 옵션에 ```Windows 10 SDK(10.0.18362.0)```를 볼 수 있습니다.
1903 Build에 맞는 SDK가 ```10.0.18362.0```이므로 다른 window build version을 사용하려면 추가적으로 옵션선택을 하여 설치해야 합니다.

또한 WDK를 다운받기 위해 [MS WDK Download page](https://docs.microsoft.com/ko-kr/windows-hardware/drivers/other-wdk-downloads)
에 접속하여 __Step2__ 에 나와 있는 WDK를 다운로드 및 설치합니다. **SDK version과 WDK version이 반드시 일치해야 합니다.**
WDK 설치가 완료되면 ```visual studio wdk extension``` 설치를 묻습니다.
마찬가지로 설치해서 ```Visual studio 2019```에서 작업 할 수 있도록 합시다.

## 개발환경 설치 확인 테스트

설치가 완료되었으면 정상적으로 설치가 되었는지 테스트를 해야 합니다.
```Visual studio 2019```에서 프로젝트 생성 창을 띄우게 되면 프로젝트 템플릿으로 ```Empty WDM Driver```를 볼 수 있습니다.

생성된 프로젝트에 기본으로 있는  ```.inf```파일은 드라이버 설치 파일입니다.
테스트하는 경우에는 필요가 없으니 삭제해 주고 ```.c```파일을 하나 생성하고 테스트 내용을 입력합니다.

```c
#include <ntddk.h>

NTSTATUS DriverEntry(
 INPDRIVER_OBJECTDriverObject,
 INPUNICODE_STRINGRegistryPath
)

{
 UNREFERENCED_PARAMETER(DriverObject);
 UNREFERENCED_PARAMETER(RegistryPath);
 DbgPrintEx(DPFLTR_IHVDRIVER_ID, 0, "Hello\n");
 returnSTATUS_UNSUCCESSFUL;
}
```

위의 테스트 코드의 출처는 Reference에 명시해 두었습니다. 정확한 코드를 이해 한 후 변경하도록 하겠습니다.
필드가 문제 없이 진행된다면 설치가 정상적으로 되었다고 판단 할 수 있습니다.

## VS2019 WDM build error

1. ```Spector-mitigrated libraries``` / ```스펙터 완화를 지원하는 라이브러리가 필요합니다```  
```Visual studio Installer```에서 ```개별 구성 요소```에 ```스펙터 완화된 라이브러리```로 구성 요소를 검색합니다.  
![개별 구성 요소](/assets/img/20-07-15_WDK.png)  
이미 설치된 ```MSCV 빌드 도구```와 같은 버전을 골라 설치합니다. 만약 Intel이 아닌 ARM 게열이라면 ARM 계열을 선택하여 설치합니다.

2. ```ntddk.h: No such file or directory```  
이 에러는 wdk include가 되지 않아 생기는 에러입니다. 아래의 폴더를 추가하여 경로를 인식시켜 줍니다.  

    ```common
    Project Property - C/C++ - General - Additional include directories

    INPUT "C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\km"
    ```

3. ```suppress.h: No such file or directories``` or ```excpt.h: No such file or directory```  
wdk 개발환경에 관한 블로그에서는 해당하는 폴더를 추가하라는 이야기가 나와 있지 않았습니다.
뭔가 설치를 잘못 한 것인지 sdk와 wdk 재설치를 해도 달라지는 없이 없어 ```suppress.h```와 ```excpt.h```가 존재하는 폴더를 찾아 include 시켰습니다.
2번과 마찬가지로 아래와 같이 include 폴더를 추가 해 줍니다.  

```common
Project Property - C/C++ - General - Additional include directories

suppress.h: "C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\km\shared"
excpt.h: "C:\Program Files (x86)\Windows Kits\10\Include\10.0.18362.0\km\crt"
```

## Reference

* [WDM - Driver 개발의 시작 (DriverEntry)](https://cshmax.tistory.com/1)
* [Windows/Driver WDK 10 개발환경 구축(VS2015,SDK, WDM10)](https://m.blog.naver.com/lucidmaj7/221086265628)
* [visual studio errors](https://m.blog.naver.com/jonghong0316/221593313898)
* [윈도우 디바이스 드라이버 기초(1)](https://nroses-taek.tistory.com/276)