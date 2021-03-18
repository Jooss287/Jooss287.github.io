---
title: Inf 작성하기, Driver 빌드하기
date: 2020-08-24 18:30:00 +0900
categories: [Language, C]
tags: [windows driver]     # TAG names should always be lowercase
toc: true
---

# Inf file 작성
드라이버에 관한 정보는 드라이버를 설치 할 때 필요한 Inf 파일에 기록됩니다.

inf의 작성방법에 대한 정보는 아래의 링크로 확인하시기 바랍니다.
* [inf 구조](https://oulth.tistory.com/25)  
* [Window driver guide msdn](https://docs.microsoft.com/ko-kr/windows-hardware/drivers/install/general-guidelines-for-inf-files)

실제로 하드웨어 드라이버와 연결 시 Inf 파일에서 설정 해 주어야 할 부분은 __[Stancdard.NT$ARCH$]__ 입니다.  
PCI카드의 BARs(Base Address Registers)의```Vender ID```와 ```Device ID```를이용하여 Window Driver에 연결 할 PCI 주소를 알려줍니다.  
MSDN에 작성된 작성방법을 참고하여 PCI 카드의 정보를 작성 할 수 있습니다.
* [MSDN Identifier for PCI Devices](https://docs.microsoft.com/ko-kr/windows-hardware/drivers/install/identifiers-for-pci-devices)
```
PCI\\VEN_102C&DEV_00E0&REV_04
PCI\\VEN_102C&DEV_00E0
PCI\\VEN_102C&DEV_00E0&REV_04&CC_0300
PCI\\VEN_102C&DEV_00E0&CC_030000
PCI\\VEN_102C&DEV_00E0&CC_0300
PCI\\VEN_102C&CC_030000
PCI\\VEN_102C&CC_0300
PCI\\VEN_102C
PCI\\CC_030000
PCI\\CC_0300
```

테스트용도로 사용하고 있는 Cyclone 10의 PCI 카드를 PC여 연결하여 확인하였더니
장치관리자에 드라이버가 설치되지 않은 PCI의 정보를 이용하여 아래와 같이 Device의 정보를 알 수 있었습니다.  
![장비 연결 시 볼 수 있는 정보](TIL_img/20-08-24_Driver_EquipmentInfo.png) 

# Driver build
드라이버의 빌드과정은 일반적인 프로그램 제작과는 조금 다른 과정을 거칩니다.
Visual studio에서 inf빌드가 가능하지만 안정성을 이유로 EWDK를 사용하여 빌드과정을 거칩니다.
Microsoft 설치파일배포 페이지에서 EWDK를 다운받을 수 있습니다.  
[EWDK downalod page](https://docs.microsoft.com/en-us/windows-hardware/drivers/download-the-wdk#download-icon-ewdk-with-visual-studio-build-tools)
![MS EWDK 다운로드 홈페이지](TIL_img/20-08-24_EWDK_Download.png)

WDK와 버전을 맞춰 EWDK를 다운로드 한 뒤 마운트시키면 몇가지 파일들이 보입니다.
그 중 LunchBuildEnv.cmd를 켜서 Command Mode에서 build 작업을 수행합니다.
프로젝트가 있는 폴더로 ```cd``` 명령어를 이용하여 이동 후 빌드 작업을 수행하게 됩니다.
![EWDK 실행 시 화면](TIL_img/20-08-24_EWDK_Lunch.png)

프로젝트 폴더가 있는 곳에서 project 이름과 build type 등을 입력 해 주면 됩니다.
```shell script
msbuild PCISample.vcxproj /t:Rebuild /p:Configuration="Debug";Platform=x64
```

테스트 할 경우에는 모든 명령어를 쓰지 않고 미리 bat파일로 작성하여 자동화시켜 줍니다.
* __samplebuild.bat__
```shell script
@echo off
msbuild PCISample.vcxproj /t:Rebuild /p:Configuration="Debug";Platform=x64
IF %ERRORLEVEL% NEQ 0 GOTO ERROR

:OK
echo ################################
echo ################################
echo         Build Success!!
echo ################################
echo ################################
goto end

:ERROR
echo ################################
echo ################################
echo         Build Error!!
echo ################################
echo ################################
goto end

:end
```

# Driver Install
64bit PC에서 드라이버를 설치하기 위하여 default 값으로 WHQL(Windows Hardware Quality Labs Testing) 인증을 받게 되어 있습니다.
그러나 회사 내부에서 사용하기 위한 Driver는 MS사의 WHQL인증을 받지 않습니다.  
PC에서 인증되지 않은 드라이버를 설치하기 위해서 command 창을 열어줍니다. 
```shell script
bcdedit /set testsigning on
bcdedit /set debug on 
```
위의 코드를 입력하고 재부팅 하게 되면 아래와 같은 글자를 볼 수 있습니다.  
![Testmode 결과 이미지](/asset/img/20-08-24_windows10_TestMode.png)
* [Runtime 시 장비 설정 방법](https://ruinses.tistory.com/654)  

1. 실행 창에서 ```devmgmt.msc```를 입력하여 장치관리자로 들어갑니다.
2. ```동작``` - ```레거시 하드웨어 추가```
3. 목록에서 직접 선택한 하드웨어 설치(고급) 선택
4. EWDK로 build 한 inf 파일 선택 및 설치
5. 오류 없이 설치가 되었을 경우 아래와 같이 드라이버가 추가 된 것을 확인 할 수 있습니다.  
![드라이버 설치 결과](/asset/img/20-08-24_DriverInstall.png)
