---
title: 코드의 수행시간 체크하기
date: 2021-04-16 17:04:00 +0900
categories: [Cpp]
tags: [time_check]
toc: true
---

C++ 작업을 진행하다 보면 내가 만든 코드의 수행시간이 얼마나 될지 체크해야하는 경우가 생깁니다. 프로그램이 더 빨리 혹은 긴밀하게 연계되어 돌아가는 코드에서는 더욱더 이러한 수행시간을 체크하는것이 중요해집니다.

Windows OS에는 다양한 Time Check 방법들이 존재합니다.

* clock_t
* GetTickCount()
* timeGetTime()
* chrono::system_clock::now()
* QueryPerformenceCounter


하나하나 정리하며 추가로 알게 된 정보에 대해서는 아래에 더 추가하도록 하겠습니다.

기본적으로 모든 함수들을 0.1ms, 5ms, 20ms, 100ms, 1000ms 다섯가지 방법으로 10번 반복하여 정리하도록 하겠습니다.
코드의 수행시간을 체크하려면 수행시간동안 딜레이를 줄 함수가 필요합니다. 이번 실험에서는 chrono함수를 이용하여 nanosecond~second의 딜레이를 주었습니다. 딜레이 함수에 대해서는 따로 포스팅해보독 할게요.

# 결과 정리
`GetTickCount`: Win32 기본 API로 헤더파일 추가 필요 없이 동작하는 Time Check 함수입니다. OS의 Interrupt Request에 영향을 받으므로 msdn에 따르면 10ms ~ 16ms정도의 resolution을 가진다고 합니다. IRQ는 약 15.625ms로 동작하는 내부 Interrupt 입니다. Return type이 DWORD라서 49.7일까지밖에 체크가 불가능합니다. 대체 함수로 `GetTickCount64`가 있습니다.

`timeGetTime`: 윈도우가 시작되어서 지금까지 흐른 시간을 ms단위로 리턴하는 함수입니다. 이 함수를 사용하기 위해서는 ```windows.h```와 ```Winmm.lib```를 추가해야 합니다(코드설명 참조). 이 함수의 기본 정밀도는 5ms이며 `timeBeginPeriod`와 `timeEndPeriod`를 사용하여 정밀도를 올릴 수 있습니다. return type이 `GetTickCount`와 같이 DWORD이라 49.7일까지 측정 가능합니다. msdn 에 따르면 더 정밀하게 측정하려면 `QueryPerformanceCounter`함수를 사용하여야 합니다.

`clock_t`: CRT(Universal C runtime library) 초기화 이후 경과된 시간으로 Wall clock을 알려줍니다. msdn에 따르면 이 함수는 ISO C를 엄격하게 준수하지 않으며 순수 CPU시간을 반환값으로 지정합니다. 경과된 시간을 확인하려면 ```CLOCK_PER_SEC```으로 나누어야 합니다. 최대 clock반환 값인 약 24.8일까지 표현가능하고 그 이상일 경우에는 64비트 시간함수 또는 `Queryperformancecounter`함수를 사용하라고 나와 있습니다.

`system_clock::now`: C++11에서 추가된 C++ 언어 레벨에서의 time check 함수입니다. 다른 시간 함수들은 Windows에서 제공하는 API인 반면에 chrono는 OS에 영향을 받지 않고 사용 할 수 있습니다. 그 정확도도 매우 높은편입니다.

`QueryPerformanceCounter` : `windows.h`헤더에 추가된 API입니다. performance counter의 현재 value를 리턴하면서 1us 이하의 high resolution을 보장합니다(MSDN). 메인보드에 고성능 타이머를 이용하여 카운팅해서 고성능을 보장 할 수 있습니다. `QueryPerformanceFrequency`를 통해서 메인보드 타이머의 주파수를 알아내고 `QueryPerformanceCounter`를 사용하여 CPU의 클럭 수를 받습니다.

# 함수 별 수행시간  체크
## 수행시간 __0.1ms__  
* `GetTickCount`가 0 or 16ms인 것이 눈에 띕니다. IRQ에 영향을 받아 시간 분해능(Resolution)이 16ms가량 되는 것을 알 수 있습니다.

| Name | 1회 | 2회 | 3회 | 4회 | 5회 | 6회 | 7회 | 8회 | 9회 | 10회 |
|:----:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
| `clock_t` |2ms|2ms|2ms|2ms|1ms|2ms|2ms|3ms|2ms|2ms|
| `GetTickCount()` |0ms|0ms|0ms|0ms|0ms|0ms|16ms|0ms|0ms|0ms|
| `timeGetTime()` |1ms|3ms|2ms|2ms|1ms|2ms|2ms|2ms|2ms|2ms|
| `system_clock::now()` |1.39ms|1.94ms|1.58ms|1.70ms|1.70ms|1.56ms|1.72ms|1.57ms|1.62ms|1.69ms|
| `QueryPerformenceCounter` |1.89ms|1.64ms|1.71ms|1.72ms|1.71ms|1.60ms|1.43ms|1.64ms|1.53ms|1.67ms|
___ 

## 수행시간 __1ms__  
| Name | 1회 | 2회 | 3회 | 4회 | 5회 | 6회 | 7회 | 8회 | 9회 | 10회 |
|:----:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|`clock_t`|2ms|2ms|1ms|2ms|1ms|2ms|2ms|2ms|1ms|2ms
|`GetTickCount()`|0ms|0ms|15ms|0ms|0ms|0ms|0ms|0ms|0ms|0ms
|`timeGetTime()`|1ms|2ms|2ms|2ms|2ms|2ms|2ms|1ms|2ms|2ms
|`system_clock::now()`|1.50ms|1.59ms|1.46ms|1.39ms|1.07ms|1.33ms|1.57ms|1.51ms|1.37ms|1.39ms
|`QueryPerformenceCounter`|2.31ms|1.42ms|1.91ms|1.31ms|1.38ms|1.49ms|1.52ms|1.69ms|1.49ms|1.42ms
___ 

## 수행시간 __5ms__  
| Name | 1회 | 2회 | 3회 | 4회 | 5회 | 6회 | 7회 | 8회 | 9회 | 10회 |
|:----:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|`clock_t`|5ms|6ms|5ms|6ms|6ms|6ms|6ms|6ms|6ms|6ms
|`GetTickCount()`|15ms|0ms|16ms|0ms|0ms|15ms|0ms|0ms|16ms|0ms
|`timeGetTime()`|6ms|5ms|7ms|5ms|6ms|6ms|5ms|6ms|6ms|6ms
|`system_clock::now()`|6.14ms|5.03ms|5.51ms|5.82ms|5.88ms|5.18ms|6.01ms|5.13ms|5.25ms|5.11ms
|`QueryPerformenceCounter`|5.93ms|5.76ms|5.40ms|5.44ms|5.79ms|5.07ms|5.89ms|5.66ms|5.04ms|5.82ms
___ 

## 수행시간 __20ms__  
| Name | 1회 | 2회 | 3회 | 4회 | 5회 | 6회 | 7회 | 8회 | 9회 | 10회 |
|:----:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|`clock_t`|21ms|21ms|21ms|20ms|21ms|20ms|21ms|21ms|21ms|20ms
|`GetTickCount()`|32ms|15ms|31ms|16ms|16ms|31ms|16ms|15ms|15ms|16ms
|`timeGetTime()`|20ms|20ms|21ms|21ms|21ms|21ms|21ms|21ms|22ms|20ms
|`system_clock::now()`|20.35ms|20.67ms|20.84ms|20.97ms|20.96ms|20.21ms|20.93ms|20.14ms|20.37ms|20.45ms
|`QueryPerformenceCounter`|20.51ms|20.89ms|20.17ms|20.49ms|20.56ms|20.93ms|20.67ms|20.03ms|20.94ms|20.65ms
___ 

## 수행시간 __100ms__  
| Name | 1회 | 2회 | 3회 | 4회 | 5회 | 6회 | 7회 | 8회 | 9회 | 10회 |
|:----:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|`clock_t`|101ms|101ms|100ms|101ms|101ms|101ms|101ms|101ms|101ms|100ms
|`GetTickCount()`|109ms|94ms|109ms|94ms|109ms|94ms|109ms|94ms|109ms|94ms
|`timeGetTime()`|101ms|101ms|101ms|101ms|101ms|101ms|100ms|101ms|100ms|101ms
|`system_clock::now()`|100.41ms|100.30ms|100.21ms|100.19ms|100.26ms|100.17ms|101.26ms|100.89ms|100.43ms|100.93ms
|`QueryPerformenceCounter`|100.33ms|100.38ms|100.07ms|100.98ms|100.79ms|100.41ms|100.53ms|100.23ms|100.70ms|100.35ms
___ 

## 수행시간 __1000ms__  
* 사실상 수행시간이 1s단위까지 오게 되면 모든 시간 함수들의 time check 능력이 크게 차이나지 않습니다.

| Name | 1회 | 2회 | 3회 | 4회 | 5회 | 6회 | 7회 | 8회 | 9회 | 10회 |
|:----:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|:--:|
|`clock_t`|1000ms|1001ms|1001ms|1001ms|1001ms|1001ms|1000ms|1001ms|1001ms|1000ms
|`GetTickCount()`|1000ms|1016ms|1000ms|1000ms|1000ms|1000ms|1000ms|1000ms|1000ms|1000ms
|`timeGetTime()`|1000ms|1000ms|1002ms|1002ms|1001ms|1001ms|1002ms|1001ms|1001ms|1002ms
|`system_clock::now()`|1000.76ms|1001.05ms|1000.46ms|1000.64ms|1001.29ms|1000.79ms|1000.34ms|1001.06ms|1000.28ms|1000.51ms
|`QueryPerformenceCounter`|1000.85ms|1002.72ms|1000.76ms|1000.60ms|1000.43ms|1001.16ms|1007.37ms|1000.64ms|1001.13ms|1000.44ms

___ 

# 함수 설명 및 결과
## clock_t
```cpp
int micro_sec = 1000;
cout << "Coock_t time check" << endl;
int i = 10;
while (i-- > 0)
{
    clock_t start = clock();
    this_thread::sleep_for(chrono::microseconds(micro_sec));
    clock_t end = clock();

    cout << (end - start) << "ms\t";
}
cout << endl;
```

## GetTickCount()
중복되는부분을 제외하고 반복문 안에만 표현하겠습니다.
```cpp
auto start = GetTickCount();
this_thread::sleep_for(chrono::microseconds(micro_sec));
auto end = GetTickCount();

cout << (end - start) << "ms\t";
```

## timeGetTime()
```cpp
auto start = timeGetTime();
this_thread::sleep_for(chrono::microseconds(micro_sec));
auto end = timeGetTime();

cout << (end - start) << "ms\t";
```
## system_clock::now()
```cpp
auto start = chrono::system_clock::now();
this_thread::sleep_for(chrono::microseconds(micro_sec));
auto end = chrono::system_clock::now();

chrono::microseconds delta = chrono::duration_cast<chrono::microseconds>(end - start);
cout << fixed;
cout.precision(2);
cout << delta.count()/1000.0 << "ms\t";
```
## QueryPerformenceCounter
```cpp
LARGE_INTEGER freq, start, end;
QueryPerformanceFrequency(&freq);
QueryPerformanceCounter(&start);
this_thread::sleep_for(chrono::microseconds(micro_sec));
QueryPerformanceCounter(&end);

double delta = static_cast<double>((end.QuadPart - start.QuadPart) / (freq.QuadPart / 1000.0));
cout << fixed;
cout.precision(2);
cout << delta << "ms\t";
```

# Reference
* [c++에서 코드 수행시간 측정](http://blog.naver.com/mycpp/221536816703)
* [tipsoft- QueryPerformance함수에 대하여](http://www.tipssoft.com/bulletin/board.php?bo_table=FAQ&wr_id=735)