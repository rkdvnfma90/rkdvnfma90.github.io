---
title: Git Cheat Sheet
date: 2022-03-11 15:33:00 +0900
categories: [TIL]
tags: [Git]
image:
  src: https://noticon-static.tammolo.com/dgggcrkxq/image/upload/v1566913419/noticon/xf9bevlrgugi7xj6xkhp.png
  width: 100
  height: 100
  alt: Git
---


## Branch

- `git checkout` [해시코드, 브랜치] : HEAD를 명시한 브랜치, 해시코드로 옮긴다. (브랜치 이동같은 경우 git switch 명령어를 사용해도 된다)
- `git branch --merged` : 현재 브랜치에 merge된 브랜치들을 확인할 수 있다.
- `git branch --no-merged` : 현재 브랜치에 merge가 되지 않은 브랜치들을 확인할 수 있다.
- `git push origin --delete` [삭제할 브랜치명] : 로컬에서 삭제한 브랜치를 원격에 반영한다.
- `git branch --move` [A] [B] : A브랜치의 이름을 B로 변경한다.
- `git branch --set-upstream origin` [B] : 변경한 브랜치를 원격에 반영한다.
- `git log` [A]..[B] : A와 B브랜치 사이에 있는 커밋들만 확인 가능.
- `git diff` [A]..[B] : A와 B브랜치 사이에 있는 코드 변경사항들만 확인 가능.

</br>
</br>

## Merge

- `fast-forward merge`
  - 가장 깔끔하고 간단하다.
  - 마스터 브랜치에서 새로운 브랜치(feature A)가 생성된 이후에 마스터 브랜치에 변동사항이 없다면 merge를 할 때 단순히 마스터 브랜치가 가리키고 있는 포인터를 옮기는 것이다. (d -> f)
  <img width="866" alt="image" src="https://user-images.githubusercontent.com/52060742/157812052-9d6dd2cf-f447-44eb-a1ff-7e85de4a6d59.png">
  - 그 후 feature A 브랜치를 삭제하면 끝!
  - 단점으로는 `히스토리에 merge가 되었다는 내용이 남지 않는다.` 그래서 히스토리를 남기고자 한다면 별도의 `merge commit`을 만든다. 이것을 `three-way merge`라고 한다.

<br>

- `three-way merge`
  - merge할 때 히스토리를 남기기 위한 방법이다.
  <img width="905" alt="image" src="https://user-images.githubusercontent.com/52060742/157813545-26dff610-c04a-455d-a693-9dccef2e2088.png">
  - `git merge --no-ff` [merge할 브랜치명] : 이 명령어를 통해 three-way merge를 할 수 있다. 해당 명령어 수행 후 커밋메시지 입력! 
- 충돌 발생시 해결 후 `git add [충돌해결 한 파일]` -> `git merge --continue`로 merge 진행 (단, fast-forward merge가 아니기 때문에 merge 커밋이 만들어진다.), 만약 merge를 취소하고 싶으면 `git merge --abort`
- 충돌해결 vscode로 진행하기 : `git config --global -e` 명령어로 설정파일을 열고 다음과 같이 추가해준다.
  ```
  [merge]
    tool = vscode
  [mergetool "vscode"]
    cmd = code --wait $MERGED
  ```
- 충돌 발생 후 `git mergetool`명령어를 사용하여 vscode에서 충돌을 해결할 수 있다.
- 충돌 해결 후 아래와 같이 해당 파일의 `.orig` 확장자의 파일이 나올 수 있는데 이 파일은 충돌이 발생했을 때의 내용을 담고있는 파일이다.
  ![image](https://user-images.githubusercontent.com/52060742/158166747-ee985641-29d6-421b-bd73-22586c2b486e.png)
  이것을 끄기위해 `git config --global mergetool.keepBackup false` 명령어를 실행시켜주자

<br>
<br>

## rebase

- three-way merge 상황일 경우 히스토리에 merge 커밋이 남게 된다. 별도의 merge commit이 생기지 않게 하는 방법이 바로 `rebase`이다.

  ![image](https://user-images.githubusercontent.com/52060742/158369984-08870872-0607-4629-ae4e-e95a8b082a86.png)

  위 사진과 같이 three-way merge 상황일 때 `feature A`브랜치를 `master`브랜치 최신 버전으로 rebase를 한다면 아래와 같이 `fast-forward merge`가 가능해진다.

  ![image](https://user-images.githubusercontent.com/52060742/158370283-37303a25-e09b-46d1-84a4-fd4cfa628e46.png)
- 주의할 점은 `feature A`브랜치에서 나 혼자만 작업하고 있다면 상관없지만 다른 개발자와 함께 `feature A`브랜치에서 작업을 하고 있다면 위험할 수가 있다. 그 이유는 rebase를 하게 되면 해당 `커밋의 포인터를 변경`하게 되는데 포인터의 정보를 변경하게 되면 기존의 커밋을 유지하는 것이 아니라 새로운 커밋을 만들게 된다. (겉으로는 똑같아 보이지만 실제로는 다르다)
- 그렇기 때문에 이미 히스토리가 원격지에 업로드 되어 있다면 업로드된 히스토리는 절대 `rebase` 하지말자! (내 로컬에 있는 커밋에는 `rebase`를 자유롭게 해도 된다!)