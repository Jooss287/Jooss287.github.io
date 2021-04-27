---
title: WDM 용어 정리
date: 2020-08-04 18:30:00 +0900
categories: [Language, C]
tags: [windows driver]     # TAG names should always be lowercase
toc: true
---

## 파일 입출력과 IRP
응용 프로그램 단에서 드라이버와 통신을 하게 되면 드라이퍼를 '파일'로써 인식하고 Win32API 를 사용하여 통신을 하게 됩니다.  
Win32API의 CreateFile을 호출하게 되면 Dirver단에서 ```IRP_MJ_CREATE``` 명령어가 생성되어 드라이버에게 전달합니다.
또한 드라이버에서는 ```MRP_MJ_CREATE```명령어를 처리하는 콜백 함수가 ```DEVICE_OBJECT```에 등록되어 있어야 합니다.

1. 응용 프로그램에서 ```I/O Manager```로 [요청]
2. 요청한 디바이스 스택 발견 [Symbolic Name 사용]
3. 디바이스 스택 발견 시, I/OManager는 IRP 생성
4. 디바이스 스택의 최상위 디바이스 오브젝트 획득
5. 최상위 디바이스 오브젝트를 생성한 드라이브 오브젝트 검색/발견
6. 발견 시, I/O Manager는 해당 드라이버에 ```IoCallDriver()```함수를 이용하여 IRP를 드라이버에 전달
7. 최상위의 드라이브 오브젝트는 연결구조[디바이스 스택은 Device Object의 포인터로 연결되어 있음]를 이용하여 밑에 존재하는 디바이스 오브젝트를 찾고, 해당 디바이스 오브젝트를 생성한 드라이버 오브젝트를 찾음
8. 다시 최상위 드라이버 오브젝트는 ```IoCallDriver()```함수를 이용해서 IRP를 밑의 드라이버 오브젝트에 전달
9. 최종적으로 ```IoCompleteRequest()```를 호출해서 IRP의 처리가 끝이 났다고 ```I/O Manager```에게 알려줌
10. ```I/O Manager```는 IRP를 파괴, 결과물을 응용 프로그램에게 알려줌

## 관련 용어 정리
### PDO, FDO
* PDO(Physica Device Object) - Bus Driver가 사용하는 Device Object.
pDO는 상위 디바이스 드라이버가 생성시켜 주는 pDO라서 직접 생성하는 것이 아니라 받아오는 방식입니다.

* FDO(Function Device Object) - 기능을 담당하는 디바이스 오브젝트 AddDevice에서 ```IOCreateDevice()```를 이용하여 FDO를 생성해 줍니다.
이 때 생성된 FDO를 이용하여 다른 모든 루틴에서 FDO를 이용하여 작업을 하게 됩니다.

* FIDO(Filter Device Object) - PDO와 FDO 사이에 붙어서 IRP가 FDO까지 내려가지 않고 FIDO에서 작업을 처리 할 수 있게 할 수 있습니다.
상위 레벨 디바이스를 거쳐 하위 레벨 디바이스에 도달해 작업을 처리하지 않고 FIDO를 이용해 바로 처리하게 할 수 있습니다.(써보지 않음 부정확)
### Paged Pool, Nonpaged Pool
* Page와 Frame
    > __Page__: 가상 메모리를 일정된 한 크기로 나눈 블록(가상 메모리의 최소 크기 단위)    
    __Frame__: 물리 메모리를 일정한 크기로 나눈 블록 
* Paged Pool
    > 시스템 공간의 가상 메모리 영역을 지칭하는 말  
    __page out__: 실제 메모리에서 제거되어 페이징 파일에 기록 될 내용  
    __page in__: 페이징 파일에서 실제 메모리로 올라 올 내용
* Non-paged Pool
    > 실제 메모리에 상주하여 어느 IRQL 수준에서든지 페이지 폴트를 내지 않고 액세스 할 수 있다고 보장된 시스템 가상 주소
* page fault
    > 프레임과 매칭을 못 했을 경우에 발생

### IRQ, IRQL(Interrupt Request Level)
운영체제는 하드웨어 이벤트에 효과적으로 응답하기 위하여 인터럽트라는 매커니즘을 사용합니다.
하드웨어 이벤트 뿐만 아니라 소프트웨어의 인터럽트도 지원을 하며 인터럽트에 대한 우선순위는 IRQL로 관리합니다.
하위 level의 인터럽트를 처리하다가 더 상위의 인터럽트가 발생 시 상위 인터럽트부터 우선적으로 처리하게 됩니다.
* IRQ(Interrupt Request): 물리적인 인터럽트
* IRQL: Windows에서의 논리적인 인터럽트
* DPC(Deferred Procedure Calls): 인터럽트를 처리하는 동안 다른 인터럽트가 발생하는 경우 문제가 생길 요지가 있습니다.
 이를 방지하기 위해 DPC에서 우선순위가 낮고 나중에 처리되어야 할 인터럽트들을 큐 형태로 관리합니다. 


### Reference
* [IRP, I/O Stack 관련 내용 정리](https://richong.tistory.com/275)  
* [윈도우 드라이버를 만들 때 알아야 할 기초적인 내용들](https://www.benjaminlog.com/entry/what-every-driver-programmer-should-know)