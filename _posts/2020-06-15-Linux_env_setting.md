---
title: 리눅스 환경설정하기
date: 2020-06-15 18:30:00 +0900
categories: [OS, Linux]
tags: [linux]     # TAG names should always be lowercase
toc: true
---

TILC(Today I Learned Challenge)가 시작되었습니다.
다른 사람들의 TIL들을 보고 리뷰하면서 자기 자신에게 좋은 자극제가 되었으면 하는 바람에서 시작했습니다.  
그 첫 주제로 항상 Linux 환경에서 이것저것 해보고싶었는데 마침 ~~리눅스 환경을 날려먹는~~ 좋은 기회가 생겨 Linux에 Docker 컨테이너를 사용하여 진행중인 프로젝트 환경을 생성하고자 합니다.

### 진행중인 프로젝트 환경(설정해야 할 환경)
* Linux mint + Django + WSGI + Apache2.4
* Linux mint + CUDA + cudnn
* Linux mint + Python + virtual env
    
## Linux mint 설치 시 Nvidia로 인한 무한부팅 해결법
1. Linux mint live 설치나 부팅 시 e를 눌러 grub 옵션 편집으로 진입합니다.
2. ```quiet sqlash ___``` 의 ```___```를 지우고 ```nomodeset```를 입력
    - 그래픽 드라이버를 이용하지 않고 BIOS모드를 이용해서 부팅하는 방법
3. F10을 눌러 남은 설치/부팅을 진행합니다.
4. ```드라이버 매니저```에서 ```nvidia-driver-440```을 설치해서 nomodeset명령어를 더 입력하지 않아도 되도록 설정합니다.  
Reference: [리눅스 멀티부팅 삽질기](http://devmuz.blogspot.com/2018/08/linux-mint-cinnamon-182-sonya-ubuntu.html)

## Linux 설치 후 해야 할 것
### 멀티 부팅 환경 기본 부팅 Windows로 설정하기
1. ```/etc/default/grub```에서 ```GRUB_DEFAULT=0```의 값을 원하는 부팅 시스템 Index의 값으로 수정하면 간편하게 수정 가능합니다.  
2. ```~$ sudo nano /etc/default/grub ```에서 ```GRUB_DEFAULT=saved```로 변경하고 저장합니다.  
3. ```~$ sudo update-grub``` 명령으로 변경한 설정을 업데이트시킵니다.
4. ```sudo grub-set-default 2```로 index를 변경합니다.
5. ```grub-editenv list```를 사용해서 ```saved_entry=2```를 확인하면 부팅 순서 설정 끝.

Reference: [Ubuntu, Windows 듀얼 부팅시 GRUB 부팅 순서 변경하기](https://webnautes.tistory.com/512)
### 한글입력 가능하게 하기
1. 소프트웨어 매니저에서 Nabi 설치
2. 시스템 설정 - 언어 - 한국어 설정
Reference: [리눅스 민트 설치는 어떻게?](https://sergeswin.com/904/)

## Docker 설치
Linux 상에서 docker는 간단하게 설치 가능합니다.  
```shell
curl -fsSL https://get.docker.com/ | sudo sh
```
docker는 root 권한을 필요로 합니다. 모든 사용자가 docker를 사용하면서 sudo를 쓰지 않고 사용하도록 권한부여를 진행합니다.
```shell
sudo usermod -aG docker $USER
sudo usermod -aG docker AUTH-NAME
```
접속중인 계정은 로그아웃하고 재접속해야 합니다.  
docker가 제대로 설치되었는지 버전확인을 합니다.  
```shell
docker version
```
![성공 이미지](/assets/img/20-06-15_successImage.jpg)

저는 permission denied 에러가 발생해서 아래의 명령을 사용해서 [추가 설정](https://github.com/occidere/TIL/issues/116)을 진행했습니다.
```shell
sudo chmod 666 /var/run/docker.sock
```  
![에러 이미지](/assets/img/TIL_img/20-06-15_errImage.jpg)

## 필요한 이미지 설치
docker에서는 다른 사람이 미리 설정해놓은 이미지를 받아와서 실행시킬 수 있습니다.
* [Apache](https://hub.docker.com/_/httpd1)
* [Nvidia Cuda](https://hub.docker.com/r/nvidia/cuda)  
```shell
docker pull NAME:TAG
docker pull httpd:2.4
docekr pull nvidia/cuda:10.1-devel-ubuntu18.04
```

다운받은 이미지를 동작시켜 docker container가 정상적으로 동작하는지 확인합시다.
```shell
docker run --rm -it httpd:2.4 /bin/bash
```
명령어를 입력하면 컨테이너에 정상적으로 접속된것을 볼 수 있습니다.
```shell
root@CONTAINER_ID:CONTAINER_ROOT# 
```

Reference: [초보를 위한 도커 안내서](https://subicura.com/2017/01/19/docker-guide-for-beginners-2.html)