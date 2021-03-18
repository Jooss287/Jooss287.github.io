---
title: PCISample 함수 분석
date: 2020-08-21 18:30:00 +0900
categories: [Language, C]
tags: [windows driver]     # TAG names should always be lowercase
toc: true
---

## DriverEntry
* ```DriverUnload```: Unload 루틴 포인터, Unload 시 해다 함수 실행  
* ```DriverExtension```: Dirver Extension pointer. ```AddDevice```을 통해서 드라이버 확장 루틴 진행 
* ```MajorFunction```: Driver의 Dispatch루틴에 대한 진입경로, 드라이버가 처리하는 ```IRP_MJ_XXX```요청에 대한 pointer를 설정해야 합니다. 

## PCIe_AddDevice
* ```IoCreateDevice```: pDriver 생성  
* ```RtlZeroMemory```: memory 0으로 초기화 매크로  
* ```InitializeListHead```: List_Entry structure 초기화(head 생성)   
* ```IoInitializeDpcRequest```: DcpForIsr 등록  
* ```KeSetEvent```: set event object  
* ```PoSetPowerState```: Device power state에 대한 상태를 정의  
* ```IoAttachDeviceToDeviceStack```: Attach 성공 시 호출자의 최상위 device object를 반환  
* ```IoRegisterDeviceInterface```: Application에서 접근하는 GUID를 등록합니다.  
* ```pFDO->Flag```: bitwise를 사용하여 관련된 값 설정  
> DO_BUFFERED_IO/DO_DIRECT_IO: I/O Manager가 사용하는 버퍼링의 타입을 지정  
> DO_POWER_PAGABLE: 항상 보장된 Thread의 문맥 아래에서만 PowerManager의 명령어를 받는다.  
> DO_POWER_INRUSH: Thread의 문맥과 상관없이 언제든 PowerManager의 IRP를 받는다.  
> DO_DEVICE_INITIALIZING: IoCreateDevice()를 통해 생성된 직후의 DeviceObject는 모두 이 값을 가진다.  

드라이버 마다 디바이스를 관리하기 위해 디바이스 오브젝트를 생성한다.
그리고 이러한 디바이스 오브젝트들이 순서를 가지고 쌓여서 디바이스 스택이 된다.

### DPCRoutine
* ```KeSetEvent```: 이벤트를 세팅합니다(?) - 조금 더 알아봐야할 듯

## PCIe_PnPDispatch
### IRP_MJ_PNP
Plug and Play Manager에서 MajorFunction Code로 IRP_MJ_PNP를 발생시켜 해당 함수를 실행시키게 합니다.
IRP: I/O request packet
* ```IoGetCurrentIrpStackLocation```: 지정된 IRP에서 호출자의 I/O Stack 위치에 대한 포인터를 반환
* ```IoCallDriver```: 소유하고 있던 IRP를 연결된 다른 디바이스 오브젝트로 소유권을 이전합니다.

### When Minor Code Sent
* ```IRP_MM_START_DEVICE```: device가 시작 및 재시작 될 때 발생합니다.
* ```IRP_MN_QUERY_PNP_DEVICE_STATE```: 초기 START_DEVICE가 성공적으로 실행 후 발생합니다.
* ```IRP_MN_STOP_DEVICE```: 디바이스를 중지시킬때 발생합니다.
이 때 디바이스 하드웨어 리소스를 재구성할 수 있습니다.
Windows2000이후의 시스템에서 QUERY_STOP_DEVICE가 성공적으로 완료된 이후에만 해당 IRP를 발생시킵니다. 
* ```IRP_MN_QUERY_REMOVE_DEVICE```: 드라이버가 컴퓨터에서 장치를 제거 할 예정임을 알리고 컴퓨터를 방해하지 않고 장치를 제거 할 수 있는지 여부를 쿼리합니다.
* ```IRP_MN_CANCEL_REMOVE_DEVICE```: 디바이스가 제거되지 않음을 알리는 IRP입니다.
* ```IRP_MN_REMOVE_DEVICE```: Device의 드라이버를 삭제하도록 할때 IRP를 발생시킵니다.
또한 오래된 버전을 제거하거나 유저가 업데이트를 요청 할 때 발생하기도 합니다.
Windows2000이후의 시스템에서 START_DEVICE가 실패 할 경우에도 발생합니다. 
* ```IRP_MN_SURPRISE_REMOVAL```: I/O operations를 더이상 사용 할 수 없을 때 이 메세지를 발생시킵니다.
사용자 모드 응용 프로그램이나 다른 커널 모드 구성 요소에 알리기 전에 이 메세지를 발생시킵니다.
이 IRP가 완료된 후 등록 된 응용 프로그램 및 드라이버에 장치가 제거되었음을 알립니다. 

### StartDevice
* ```KeInitializeEvent```: Event Object 초IoConnectInterrupt기화  
* ```IoCopyCurrentIrpStackLocationToNext```: 하위 드라이버의 스택 위치에 IRP 스택 파라미터를 복사합니다.  
* ```IoSetCompletionRoutine```: 주어진 IRP에서 Next-Lower-level driver 동작 요청이 완료되었을 경우 실행 할 루틴 지정  
* ```KeWaitForSingleObject```: 주어진 dispatcher가 signaled state나 timeout이 될 때까지 thread를 대기상태로 둡니다.  
* ```MmMapIoSpace```: PhysicalAddress 메모리를 지정합니다. 물리메모리공간을 위한 가상주소를 매핑합니다.  
* ```IoConnectInterrupt```: Device Driver의 Interrupt Service Routine(ISR)을 등록합니다.
* ```IoSetDeviceInterfaceState```: 응용 프로그램이 연결될 이름을 지정/금지 합니다. 지정 시 Win32 CreateFile()함수를 사용 할 수 있습니다.

#### Interrupt Service Routine 
* ```IoRequestDpc```: 드라이버에서 제공하는 lower-IRQL들을 관리합니다.(?)

### StopDevice
* ```IoSkipCurrentIrpStackLocation```: 현재 IRP의 스택을 다음 계층의 드라이버를 위해 소유권을 포기합니다.

### RemoveDevice
* ```IoDisconnectInterrupt```: Device가 멈추거나 제거되었을 때 Device Driver의 인터럽트 오브젝트를 Release합니다. 
* ```RtlFreeUnicodeString```: RtlAnsiStringToUnicodeString 이나 RtlUpcaseUnicodeString로 설정된 메모리 버퍼를 해제합니다.
* ```IoDetachDevice```: 호출된 Device Object와 Lower Driver's Object의 연결을 해제시킵니다.
* ```IoDeleteDevice```: 시스템에서 device object를 제거합니다.

## PCIe_PowerDispatch
* ```PoStartNextPowerIrp```: 전원 드라이버가 준비되었음을 알리는 명령함수
* ```PoCallDriver```: power IRP를 device stack 안의 next-lower driver로 전달한다.(?)

## PCIe_Cleanup
* ```MmUnmapLockedPages```: 선행 호출된 ```MmMapLockedPages```를 해제합니다. 
* ```IoFreeMdl```: 호출자 할장된 Memory Descriptor list(MDL)을 해제합니다.  
* ```MmFreeContiguousMemory```: Physically continuous memory 범위를 해제합니다.  
* ```MmUnMapIoSpace```: ```MmMapIoSpace```을 통해 매핑된 주소를 해제합니다.  
* ```ExFreePool```: Pool 메모리 블록을 해제합니다.  

## PCIe_DeviceControl

### CTL_CODE  (I/O Control Codes)
유저모드와 드라이버간에 통신을 위해 설정하는 매크로입니다.
유저모드의 어플리케이션은 DeviceControl IRP를 호출함으로써 IOCTL을 전달합니다.


#### Method parameter
>* METHOD_BUFFERED: DeviceControl()함수에서 제공하는 입력 버퍼와 출력 버퍼를 Buffered I/O 방식으로 처리  
>* METHOD_IN_DIRECT/METHOD_OUT_DIRECT: DeviceControl()함수에서 제공되는 입력 버퍼를 Buffered I/O방식으로 처리하고 출력 버퍼를 Direct I/O방식으로 처리     
>> METHOD_IN_DIRECT: I/O 관리자는 I/O를 요청한 스레드가 출력 버퍼에 대한 읽기 권한을 가지고 있는지 조사  
>> METHOD_OUT_DIRECT: I/O 관리자는 I/O를 요청한 스레드가 출력 버퍼에 대한 쓰기 권한을 가지고 있는지 조사
>* METHOD_NEITHER: DeviceControl()함수에서 제공되는 입력 버퍼와 출력 버퍼를 Neighter I/O 방식으로 처리


#### MDL
```MDL(Memory Descriptor List)```: 가상 메모리가 어떤 물리메모리와 연결되어 있는지 보여주는 기능을 합니다.
* Direct I/O: MDL구조체를 사용하여 I/O를 요청한 스레드의 물리 주소 공간에 위치한 데이터를 가리키는 방식
* Buffered I/O: 버퍼에 저장된 I/O가 요청한 스레드의 주소 공간에 시스템 주소 공간에 있는 임시 영역으로 복사되고, 드라이버는 복사된 데이터의 버퍼 포인터를 사용하는 방식
* Neither I/O: 드라이버는 버퍼에 해당하는 I/O를 요청한 스레드의 가상 주소를 제공하는 방식

| |Direct I/O|Buffered I/O|Neither I/O|
|-----|:-----:|:-----:|:-----:|
|데이터 버퍼 위치|MDL 구조체|시스템 영역에 있는 인터미디어트 버퍼|I/O를 요청한 스레드의 가상 주소|
|데이터 보호|I/O 관리자가 데이터 보호|데이터 보호하지 않음|데이터 보호하지 않음|
|IRP 내의 데이터 저장 필드| Irp->MdlAddress|Irp->AssociatedIrp|Irp->UserBuffer|
|페이징 I/O|불가능|가능|가능|
|컨텍스트|임의의 스레드 컨텍스트|임의의 스레드 컨텍스트|I/O를 요청한 스레드 컨텍스트|

[Reference](https://tmsen.tistory.com/entry/MDL-%EC%BB%A4%EB%84%90-%EB%B2%84%ED%8D%BC)


#### IOCTL_Mapping_Memory
* ```IoAllocateMdl```: 주어진 버퍼의 시작주소와 길이를 사용하여 충분한 MDL을 설정합니다.
* ```MmBuildMdlForNonPagedPool```: 메모리가 설정되지 않은 Virtual memory buffer를 지정합니다.
매개변수로 받는 MDL은 ```IoAllocateMdl```을 사용하여 버퍼에 대해 MDL을 작성해야만 합니다.
* ```MmMapLockedPages```: 주어진 MDL의 physical page을 매핑합니다. (Windows 2000이후 버전에서는 사용하지 않는 함수입니다.)
* ```MmMapLockedPagesSpecifyCache```: 가상 주소로 MDL에 있는 물리 주소를 매핑합니다. 호출자는 매핑하는데 사용되는 캐시 속성을 지정하기 위해 호출 할 수 있습니다.(?)
* ```ExAllocatePool```: Pool memory를 배분합니다.(msdn에서 ```ExAllocatePoolWithTag``` 를 쓰라고 합니다.)
* ```ExAllocatePoolWithTag```:  지정된 타입에 대한 Pool Memory를 배분하고 정의된 블록에 대한 포인터를 반환합니다. 
* ```InsertTailList```: Doubly Linked List의 Tail에 데이터를 추가합니다.

#### IOCTL_UnMapping_Memory
* ```MmIsAddressValid```: page fault을 체크합니다. msdn에서는 이 함수의 사용을 추천하지 않습니다.  
* ```MmUnmapLockedPages```: ```MmMapLockedPages```나 ```MmMapLockedPagesSpecifyCache```로 매핑된 것들을 Release 합니다.
* ```IoFreeMdl```: 호출자의 MDL을 Release 합니다.
* ```MmUnmapIoSpace```: ```MmMapIoSpace```로 매핑한 주소를 해제합니다.
* ```RemoveEntryList```: Doubly linked list로 된 LIST_ENTRY를 제거합니다.
* ```ExFreePool```: pool memory를 해제합니다.

#### IOCTL_Get_Configuration_Register
* ```IRP_MN_READ_CONFIG```: 

### IOCTL_Register_Shared_Event
* ```ObReferenceObjectByHandle```: Object Handle 의 access 확인정보를 제공합니다.
access 가능하다면 object의 body pointer 를 반환합니다.
 
#### IOCTL_Allocate_Contiguous_Memory_For_DMA
* ```MmGetPhysicalAddress```: 
