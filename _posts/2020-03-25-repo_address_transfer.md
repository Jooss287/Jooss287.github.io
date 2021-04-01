---
title: 작업하던 Repository를 이전하기
date: 2020-03-25 18:30:00 +0900
categories: [Git]
tags: [git repository]     # TAG names should always be lowercase
toc: true
---

## Repo address transfer
소스 관리 툴을 처음 사용하면서 친구와 진행한 토이 프로젝트를 초창기 아무것도 모르고 개인 리포지토리에 생성 후 진행했다.
프로젝트가 어느정도 진행 된 후 개인 repo보다는 그룹으로 진행하는 것이 맞다는 생각이 들었고 주소 이전을 할 방법을 찾아보았다.   

git commend에 관한 설명을 제외하고 repo 주소 이전에 관한 내용만 기록하겠다. 

## Given situation
1. 4 branches, 49commits, 7issue 정도의 얼마 진행되지 않은 프로젝트
2. issue번호가 꼬일 것 같았지만 개수가 얼마 없어 기록으로나마 노가다성 옮기기 진행
3. 모든 branches, commits, issue 를 옮기고 싶으나 issue 를 옮길 방법은 없음
4. Git을 설치시에 같이 설치되는 Git CMD 창 이용

## how to transfer
### 1. git cmd 창에서 옮기고자 하는 리포지토리를 로컬로 전체 복사 
```
git clone --mirror {git Repo address}

옮기고자 하는 대상 리포지토리 이름이 개인 리포"Test_origin_repo" 라면
git clone --mirror http://github.com/Jooss287/Test_origin_repo
``` 
clone이 완료되면 실제로 현재 폴더에 {Repo name.git}폴더가 생성 된 것을 알 수 있다.   
*Command 입력 앞에 나오는 경로가 현재 경로이다*

### 2. Clone 한 폴더를 .git으로 폴더 이름 변경
```
git mv {Repo name.git} .git
``` 
진행하다가 폴더 이름을 변경하면서 에러가 났다.
```
fatal: bad source, source={Repo name.git} destination=.git/{Repo name.git}
```
관련 에러를 검색해보니 
[git init](https://waspro.tistory.com/539)와 
[git path](https://velog.io/@zansol/git%EB%A6%B0%EC%9D%B4%ED%83%88%EC%B6%9C%EA%B8%B0-%ED%8F%B4%EB%8D%94%EC%9D%B4%EB%A6%84-%EB%B3%80%EA%B2%BD%ED%95%98%EA%B8%B0)
의 문제로 나오는데 Init의 경우에는 mv명령어가 사용 불가능해졌고 path는 올바르게 사용하였다.

결국 임시방편으로 window에서 직접 .git폴더를 삭제하고 {Repo name.git}을 {.git}으로 변경

### 3. 새 리포지토리로 복사
새 리포지토리로 복사하기 전 git cmd창에서 복사하고자 하는 리포지토리를 연결해야 한다.
```
git remote set-url origin {new Repo address}

복사 될 새 리포지토리 이름이 "Test_target_repo"라면
git remote set-url origin http://github.com/Jooss287/Test_target_repo
```
Fatal 에러가 뜨지 않고 usage:로 시작하는 response가 나왔다면 연결이 완료되었다.   
이제 push를 이용해서 리포지토리를 복사한다.
```
git clone --mirror
```
이제 해당 홈페이지로 가서 Repo가 제대로복사되었는지 확인

### 4. Issue 작업
Issue 도 함께 복사되면 좋겠지만 그런 기능은 아직 존재하지 않는다(~~필요가 없을지도~~)   
이전 Repo 와 새로 이사한 Repo 가 남아 있어 비교하면서 Issue를 일일이 추가했다.
기존의 Repo의 Issue가 많을 경우 다른 방법을 강구해야하 하지만 Issue 의 개념을 깨달은지 얼마 되지 않아 아직 없는것에 감사해야 할 듯 

## Reference
1. [Git Repository 이동하기](https://velog.io/@nakta/Git-Repository-%EC%9D%B4%EB%8F%99%ED%95%98%EA%B8%B0)
2. [Fatal: Not a git repositroy](https://waspro.tistory.com/539)