---
title: 컴퓨터 두대로 방송세팅 혹은 녹화 세팅하기
date: 2020-06-21 18:30:00 +0900
categories: [ETC]
tags: [etc recording]     # TAG names should always be lowercase
toc: true
---

컴퓨터 두대로 녹화 혹은 방송을 하게 되면 항상 세팅에서 시간을 잡아먹는 경우가 많다.
시간을 투자하면 복구할수는 있지만 그 시간을 최소한으로 하기 위해 md파일로 작성해 둔다.
이 글은 세팅을 하는것에만 중점을 두고 소프트웨어의 사용법에 대해서는 적지 않는다.  

## 보유상황
* 게임용 PC
    * X6 Supercast capture board
    * VoiceMeeter Banana(SW)
    * Virtual Audio Cable (VB-CABLE) (SW)
* 녹화용 PC
    * OBS Studio(SW)
    * VoiceMeeter Banana(SW)


## 설정하고자 하는 세팅
* 게임용 PC의 사운드는 그대로 출력 될 것(헤드셋 or 스피커)

* 게임용 PC에서 접속한 디스코드의 소리도 녹화 가능하게 설정(설정을 바꾸고 싶을 경우 변경법도 저장)

* 녹화용 PC는 Capture board에서 들어온 PC소리와 화면을 녹화

* 녹화용 PC의 Capture board소리는 스피커로 나오지 않음(내부적으로 녹화할때만 처리)

## 게임용 PC 세팅
게임용 PC 설정의 핵심은 모든 소리를 VoiceMeeter에서 잡아 원하는 곳으로 보내도록 하는 것이다.

### 게임용 화면을 녹화용 화면으로 캡쳐하기
1. 바탕화면 우클릭 - 디스플레이 설정

2. 화면을 아래 사진과 같이 ```디스플레이 복제```로 설정  

![디스플레이설정1](/assets/etc/img/displaysetting1.png)
![디스플레이설정2](/assets/etc/img/displaysetting2.png)

### 게임용 PC의 소리를 녹화용 PC로 보내기
1. 화면의 오른쪽 밑 소리 버튼을 우클릭 - 소리로 들어가기

    * 버튼이 없을 경우 ```소리 설정 열기 ▶ 사운드 제어판```
    
2. ```재생``` 탭에서 ```RoseX2 HDMI``` 버튼을 우클릭해서 ```기본 통신 장치```로 설정  

![기본 통신장치 설정](/assets/etc/img/settingRoseX2HDMI.png)

3. 녹화용 PC에서 Supercast App을 통해 화면이 나오는것을 확인

### 게임용 PC의 게임소리를 녹화용 PC로 보내기
게임용 PC에서 나는 소리를 바로 스피커로 보내지 않고 VoiceMeeter의 VB-CABLE을 거쳐 스피커와 CaptureBoard로 보내는 방식을 사용한다.
이 방식은 외장 사운드 카드를 구입하는 추가 지출이 들지 않지만 녹화용PC에서 녹화 혹은 송출을 할 때 조금(0.5초 가량)의 소리 싱크가 맞지 않는다는 단점이 있다.

1. 시작표시줄 오른쪽 밑의 ```소리``` 버튼을 우클릭하여 ```소리 설정``` 열기

2. 기본 소리 출력을 CABLE Input(VB-Audio Virtual Cable)로 설정  

![기본 소리 설정 변경](/assets/etc/img/defaultInputSoundSetting.png)

3. VoiceMeeter에서 HARDWARE INPUT2에서 CABLE Output 설정

4. VoiceMeeter 오른쪽 위의 A1, A2를 각각 스피커,RoseX2 HDMI로 설정 

![VoiceMetter Setting](/assets/etc/img/GamingPC_VoiceMetter.png)

### 디스코드의 소리를 녹화용 PC로 보내기
게임 소리를 녹화용 PC로 보낼 때 이용한 CABLE Output에 디스코드 소리를 얹어 함께 보내면 두 소리를 모두 한번에 보낼 수 있다.

1. Discord 설정의 ```음성 및 비디오```탭에서 ```출력 장치```를 ```CABLE Input(VB-Audio Virtual Cable)```로 설정  

![디스코드 입/출력 설정](/assets/etc/img/discordSetting.png)

2. __다른 사람의 목소리를 녹화할 때에는 항상 사전에 허락을 받도록 하자__
3. 디스코드의 소리를 녹화용 PC로 보내고 싶지 않을 경우에는 ```출력 장치```를 원하는 스피커 혹은 헤드셋으로 설정하면 된다.

### 게임용 PC의 마이크 소리를 디스코드와 녹화용 PC로 보내기
사용하고 있는 마이크가 3.5파이 pin에서 돌아가는 유선 마이크라 파워가 약해 목소리가 작은 문제가 있었다.
소리를 보냄과 동시에 소리를 소프트웨어적으로 증폭할 필요가 있었다. ~~윈도우 내부 마이크 증폭은 잡음이 함께..~~  
우리가 사용하는 마이크를 가상 케이블에서 증폭시켜 디스코드와 녹화용 PC로 보내도록 한다.  

![pi파이 마이크](/assets/etc/img/3.5pi_microphone.png)

1. 사운드 제어판의 ```녹음``` 탭에서 사용하고 있는 마이크를 더블 클릭한다.

2. ```수신 대기```탭에서 이 장치로 듣기 ✅표시, 이 장치로 재생 설정을 ```VoiceMeeter Aux Input```으로 설정한다.  

![마이크 설정](/assets/etc/img/microphoneSetting.png)

3. 소프트웨어적으로 증폭을 시킬 것이므로 ```수준```탭의 마이크 증폭은 0dB로 설정 (~~마이크 잡음의 원인~~)

4. Discord 설정의  ```음성 및 비디오``` 탭에서 ```입력 장치```를 ```VoiceMeeter Aux Output(VB-Audio VoiceMeeter Aux VAIO)```로 설정  

![디스코드 입력 설정](/assets/etc/img/discordSetting.png) 

5. VoiceMeeter의 오른쪽 위 A3을 VoiceMetter Aux Input으로 설정한다. 

6. VoiceMeeter의 ```HARDWARE INPUT1```을 마이크로 설정후 밑의 A3로 마이크 값을 보내도록 설정한다.

7. VoiceMeeter중앙의 ```VIRTUAL INPUT``` 탭에서 VoiceMeeter AUX를 설정 가능하다.

아래 그림처럼 A2(CaptureBoard)와 B2(VoiceMeeter AUX Output)으로 설정하자.  

![VoiceMeeter 설정](/assets/etc/img/GamingPC_VoiceMetter.png)

## 녹화용 PC 세팅
녹화용 PC 세팅의 핵심은 PC자체의 소리와 녹화용 소리의 채널을 분리시켜 모든 소리 Input/Output라인을 두개의 루트로 설정하는 것이다.

### 녹화용 PC 자체 오디오 루트 만들기
1. 사운드 제어판에서 ```재생```탭에서 사용하고 있는 스피커를 ```기본 통신 장치```로 ```VoiceMeeter Aux Input```를 ```기본 장치```로 설정한다.  

![PC 내부 소리 설정](/assets/etc/img/recordPC_defaultSoundSetting1.png) 

2. 시작표시줄 오른쪽 밑의 ```소리```에서 ```소리 설정 열기```

3. 출력 장치와 입력 장치를 모두 VoiceMeeter Aux Input/Output으로 설정한다.

![PC 내부 소리 설정](/assets/etc/img/recordPC_defaultSoundSetting2.png)

4. VoiceMeeter의 오른쪽 위 ```HARDWARE OUT```에서 A1을 사용하고 있는 스피커로 설정한다.

5. VoiceMeeter에서 ```VIRTUAL INPUTS```의 ```VoiceMeeter AUX```를 A1로 연결시킨다.  

![녹화용 PC voiceMeeter 설정](/assets/etc/img/recordPC_voiceMeeter_setting.png)

### 게임용 PC에서 들어온 소리를 이용하는 오디오 루트 만들기
1. 사운드 제어판에서 ```녹음```탭에서 ```Audio In```을 기본 통신 장치로,
```VoiceMeeter Aux Output```을 기본 장치로 설정한다.  

![PC 녹음 설정](/assets/etc/img/recordPC_recordSoundSetting.png)

2. ```Audio In```을 더블클릭해서 ```수신 대기```탭을 들어간다.

3. 이 장치로 듣기 ✅표시, 이 장치로 재생을 ```CABLE Input(VB-Audio Virtual Cable)```로 설정한다.

![Audio In 설정](/assets/etc/img/recordPC_X6setting.png)  

### 녹화용 PC 세팅에서 장비 이용법

* 게임용 PC에서 들어오는 소리를 이용 할 때에는 ```CABLE Output(VB-Audio Virtual Cable)```을 이용한다.(OBS Studio, XSplit 등)

* 녹화용 PC 자체 소리(스피커,마이크)를 이용 할 때에는 ```VoiceMeeter AUX Output```을 이용용한다.(Discord 등)

## Reference
* [투컴 방송 세팅하기 (Youtuber 거니)](https://youtu.be/vSSwdX5wy9Y)

* [VoiceMeeter 사용법](https://bbs.ruliweb.com/av/board/300041/read/299932)
