---
title: 'git 명려어 정리'
date: 2020-9-22 16:21:13
category: 'git'
draft: false
---

```
git 에서 중요한 명령어를 정리한 글입니다.
```

## 로컬 브랜치 및 커밋 관리

`git commit` : 커밋 메세지 남기기
`git commit --amend [commit]` : 커밋 메세지 수정
`git branch [branch name]` : 브랜치 생성
`git checkout [branch name]` : 만들어진 브랜치 변경
`git merge [another branch name]` :  이미 있는 브랜치 합치기 (히스토리 그래로 남겨져 있음)
`git rebase [another branch name]` : 이미 있는 브랜치 위에 커밋 합치기 (하나의 커밋으로 보임)

`git checkout [commit]` : HEAD 변경
`git branch -f [branch name] [commit]` : 해당 커밋 라인 강제로 branch 변경
`HEAD^` : HEAD의 바로 앞 커밋 선택
`HEAD~[number]` : HEAD의 숫자만큼 앞 커밋 선택

`git reset [commit]` : 예전 커밋상태로 이동 (기존에 잘못 작성한 커밋 제거)
`git revert [commit]` : 선택된 커밋의 반대되는 커밋이 생성 (기존에 잘못 작성된 커밋 남아 있음)

`git cherry-pick [commit1] [commit2]` : HEAD 및에 해당 커밋을 순서대로 복사해서 연결
`git rebase -i [commit]` : 선택된 commit 아래에 현재 커밋과 선택된 commit 사이의 커밋을 순서 지정 및 제거를 하여 합치기

`git tag [tag name] [commit]` : 선택된 commit에 tag name 붙히기, 생성된 태그는 변경 불가능 
`git describe` : tag 기준으로 현재 커밋의 위치를 나타냄

## 원격
`origin master`: 원격 저장소 브랜치 (직접 커밋 남기는 것 불가능)
`master` : 로컬 저장소 브랜치

`git clone [remote commit url]` : 원격 저장소 받아오기
`git push [src remote] [src place]:[dest place]` : 로컬 저장소의 브랜치를 원격 저장소의 브랜치에 커밋 전달, 실행 후 로컬 저장소의 원격 브랜치 현재 위치로 이동. (dest place가 존재하지 않을 경우, 원격에 dest place 생성)
`git fetch [src remote] [src place]:[dest place]` : 원격 저장소의 브랜치를 로컬 저장소의 브랜치로 커밋 업데이트 (dest place가 존재하지 않을 경우, 로컬에 dest place 생성)
`git pull [src remote] [src place]:[dest place]` : `git fetch [src remote] [src place]:[dest place]`후 `git merge [dest place]` 적용
`git pull --rebase` : fetch와 rebase를 동시에 적용

## 참고 자료
https://learngitbranching.js.org/?locale=ko