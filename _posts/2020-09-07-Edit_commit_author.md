---
title: Git commit의 Author 변경하기
date: 2020-09-07 18:30:00 +0900
categories: [Git]
tags: [git, commit]     # TAG names should always be lowercase
toc: true
---

우리는 깃허브, 깃랩 등을 이용하여 깃을 사용하곤 합니다.
commit, pull request 등 여러 기능을 사용하면서 깃허브에서 잔디밭을 채우는데 희열(?)을 느끼며 개발을 하는 것 같기도 합니다.

제 깃허브 페이지에서 주말 잔디밭이 항상 비어있는 것을 보고 뭔가 이상하다고 생각하던 중, 집 컴퓨터에서 사용하는 SourceTree에 메일이 잘못 설정되어 있는것을 발견하였습니다.  
![](/assets/img/20-09-07_commitAutorList.JPG)

Git의 Rebase를 사용하여 잘못 정해진 Author를 수정 해 보겠습니다.

## 준비물
먼저, 공용 Repository 에는 Rebase작업을 하면 안 됩니다.
Git 홈페이지에는 Rebase는 Commit hash의 값을 바꾸게 되니 다른사람의 작업 내용이 꼬일 수 있다고 경고합니다. 

또한 저는 작업하려고 빼두었던 branch를 모두 merge를 하고 master 하나만 남겨두었습니다.  
Linux도 크게 다르지 않겠지만, 현재 가지고 있는 Window를 이용하여 진행하겠습니다.  
~~Linux 유저는 Window도 크게 어렵진 않을겁니다~~

## Rebase
작업 할 repository를 pull 하여 local로 받아 둡니다.
탐색기를 이용하여 ```local repository```로 이동 한 뒤 우클릭을 이용하여 ```git bash```를 실행시킵니다.  
![](/assets/img/20-09-07_Explorer_Gitbash.png)

Matser repository로 접근하였으니 여기서 rebase를 진행합니다. 
Commit hash는 commit 할 때 마다 생성되는 긴 문자열입니다.
```shell
git rebase -i -p commit_hash
```
명령어를 오류 없이 입력하면 편집창이 뜨게 됩니다.
![](/assets/img/20-09-07_rebaseEditMode.JPG)

여기서는 고쳐야 할 commit의 pick를 edit으로 바꿔서 이 부분을 수정 할 것이다 라고 알려주는 작업입니다.
edit로 수정할 부분을 지정하고 저장, 종료를 하면 git bash는 rebase mode로 진입합니다.  
과거 commit부터 검색하며 edit commit 부분을 만나면 수정 할 명령어를 입력합니다.
```shell
git commit --amend --author="jooss287 <jooss287@gmail.com>"
```
author를 변경하면 commit 내용을 바꿀 편집창이 나타나지만 여기서는 변경하지 않을 것이므로 그냥 닫아줍니다.
변경이 완료되었다면 다음 commit를 rebase하기 위해
```shell
git rebase --continue
```
모든 edit commit를 돌 때 까지 위의 작업을 반복합니다.  
작업이 완료되었다면 ```local```을 ```remote```로 옮겨야죠.
```shell
git push -f
```
그리고 기존에 없던 잔디밭이 생성 된 것을 확인하면 됩니다.

## Reference
* [이미 커밋된 내용에서 author 수정하기](https://jojoldu.tistory.com/120)
* [Git Commit Message 바꾸는 방법](http://tech.javacafe.io/2018/03/01/how-to-change-git-commit-message/) 