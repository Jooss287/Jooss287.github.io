---
title: 깔끔한 commit 기록 남기기
date: 2020-03-26 18:30:00 +0900
categories: [Git]
tags: [git, commit]     # TAG names should always be lowercase
toc: true
---

기록을 남기는것에 대해서 최대한 간결하고 깔끔하게 보이도록 하려고 노력한다.
Github를 이용하기 전에도 어떻게 하면 주석이나 코드를 깔끔하게 짤 수 있을까에 대한 고민이 항상 이어져왔던것같다.  

단순히 branch를 merge하면서 프로젝트를 진행하니 branch에서 임시로 남긴 commit도 함께 남는 경우와 오타수정처럼 간단한 작업에서도 에서도 commit이 발생하여 commit history가 지저분해지는 상황이 자주 발생하였다.

평상시에 commit를 남발하더라도 프로젝트에 남는 commit 만큼은 최대한 알아보기 쉽게 남기기 위하여 고민하고 적용 한 것을 기록하려고 한다.

## branch 간 merge  

소규모 프로젝트에서 많은 branch를 관리하기엔 실제로 접근하는 수가 많지 않다.
그래서 최대한 필요한 내용만 사용하면서도 관리를 최대한 깔끔하게 하려고 5가지의 branch만을 사용하고 있다.

| Branch name | 용도 | 하위 branch(merge 방법) |
|------------|------|-----------|
| master | 배포용(Release) |  development(merge),<br> hotfix(rebase merge) |
| development | 개발자용(Tester) | Feature(squash merge),<br> hotfix(rebase merge) |  
| feature | 기능 추가 | private(squash merge) |
| private | 임시 저장용도<br> --git 이용 장소가 여러곳일 경우 | <center>x</center>|
| hotfix | 우선 수정사항(bug, 시급한 오타 등) | (temporary branch) |

[merge 방법](https://evan-moon.github.io/2019/08/30/commit-history-merge-strategy/)은 총 세가지가 있다.
장단점이 있지만 history관점에서만 보면 commit history, branch history를 어떻게 할 것인가에 대하나 설정이라고 볼 수 있을 것 같다.

| merge method | commit history | branch history |
|---|---|---|
| merge | O | O |
| squash and merge | X | O |
| rebase and merge | O | X |

### 최종적으로

**hotfix**는 새 branch의 정보가 필요가 없으므로 rebase merge  
**private**는 온갖 개인 메모 등의 commit가 들어 있으므로 squash merge  
**feature, development, master**는 private, hotfix에서 정제된 commit만 남아 있으므로 merge

### Reference

1. [커밋 히스토리를 이쁘게 단장하자](https://evan-moon.github.io/2019/08/30/commit-history-merge-strategy/)