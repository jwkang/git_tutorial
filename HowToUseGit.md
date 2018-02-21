---
autor : jongwon kang
title : How to use git
date : 2018-02-20 12:00
---

# Git 저장소 만들기
## 기존 디렉토리를 Git 저장소로 만들기
**git init**

```
[jwkang2@localhost git_tutorial]$ git init
Initialized empty Git repository in /home/jwkang2/work/blog/git_tutorial/.git/
[jwkang2@localhost git_tutorial]$
```

- git init 시점에 생성돼 있는 파일은 모두 tracked 상태 이다.
- git init 이후 생성된 파일은 기본으로 untracked 상태가 된다.

```
[jwkang2@localhost git_tutorial]$ echo "My project" > README
[jwkang2@localhost git_tutorial]$ git status
On branch master

Initial commit

Untracked files:
  (use "git add <file>..." to include in what will be committed)

        HowToUseGit.md
        README
```

## 파일을 새로 추적하기
**git add**

```
[jwkang2@localhost git_tutorial]$ git add .
[jwkang2@localhost git_tutorial]$ git status
On branch master

Initial commit

Changes to be committed:
  (use "git rm --cached <file>..." to unstage)

        new file:   HowToUseGit.md
        new file:   README
```

- git add로 tracked 상태로 변경할 수 있다.
- 위 상태는 tracked 상태 이면서 staged 상태이다.
- **"Changes to be committed" 는 stage 상태를 의미한다.**

파일을 수정하게되면 **Tracked 상태이지만 stage 상태가 아닌** modified 상태로 변경된다.

```
[jwkang2@localhost git_tutorial]$ echo "a" >> README
[jwkang2@localhost git_tutorial]$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   README

no changes added to commit (use "git add" and/or "git commit -a")
```

- **Changes not staged for commit** 는 Tracked 상태이지만 Staged 상태가 아님을 의미한다.
1. git add 명령은 파일을 새로 추적(track)할 때 뿐만 아니라
2. **이미 추적하고 있는 파일을 Staged상태로 변경**하는데도 사용된다.

이미 staged 상태인 파일을 다시 수정할 경우 다음과 같이 **staged 상태이면서 동시에 unstaged 상태인 경우** 가 발생 한다.

```
[jwkang2@localhost git_tutorial]$ git status
On branch master
Changes to be committed:
  (use "git reset HEAD <file>..." to unstage)

        modified:   README

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   README

[jwkang2@localhost git_tutorial]$ git status -s
MM README
```

이상태에서 commit을 하게되면 마지막 add 한 시점(마지막 staged 시점)까지만 commit이 일어난다.
commit 후 **git status** 를 살펴보면 여전히 **modified** 상태인 것을 알 수 있다.

```
commit 명령 실행 후)
".git/COMMIT_EDITMSG" 11L, 269C written
[master fc6ffd9] add ok?
 1 file changed, 2 insertions(+), 1 deletion(-)
[jwkang2@localhost git_tutorial]$ git status
On branch master
Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   README

no changes added to commit (use "git add" and/or "git commit -a")
```

## Staged 상태와 Unstaged 상태의 변경사항 확인하기
**git diff**
- git diff 명령은 **Unstaged 상태의 파일의 변경상태** 를 알 수 있다

```
[jwkang2@localhost git_tutorial]$ git diff
diff --git a/README b/README
index 5b6ffc5..7933bd1 100644
--- a/README
+++ b/README
@@ -1,2 +1,4 @@
 modified
 a
+
+added
```

- **중요** : 기본적인 git diff 명령으로는 Staged 상태의 변경 내용을 알 수 없다. 즉 Unstaged 상태인 것들만 출력한다.
- git diff --staged option을 통해 Staged 상태의 변경상태를 알 수 있다.  (git diff --cached 명령도 동일)

```
[jwkang2@localhost git_tutorial]$ git diff --staged
diff --git a/README b/README
index 5b6ffc5..e911d49 100644
--- a/README
+++ b/README
@@ -1,2 +1,5 @@
 modified
 a
+
+added
+aaa
```

- 만약 Staged 상태이면서 동시에 Unstaged 상태인 file이 존재한다면 git diff --staged 명령은 staed 상태의 파일의 수성 사항만 출력한다.

## 변경사항 commit 하기
**git commit**
- 기본적으로 Unstaged 상태인 파일은 commit되지 않는다.
- git commit은 shell의 $EDITOR에 지정한 editor를 사용한다.
- git config --global core.editor 명령으로 사용할 편집기를 수정할 수 있다.
- git commit -a 으로 Tracked 상태인 파일을 자동으로 Staged 상태 변경 하고 commit을 시도한다. (git add 명령 생략되는 효과)

## 파일 삭제하기
**git rm**
- Tracked 상태인 파일을 Staging Area에서 제거한다.
- **워킹 디렉토리에 들어있는 실제 파일도 삭제된다.**
- git rm으로 삭제하고 commit을 실행하면, git은 더이상 그 파일을 추적(track)하지 않는다.

## 되돌리기
**git commit --amend**
완료한 commit을 수정할 때 사용한다.

```
# stage 상태로 올리는 것을 잊어버리고 commit을 한경우 다시 commit하는 예)
$ git commit -m "initial commit"
$ git add forgotten_file
$ git commit --amend
```
수정된 상태가 없다면 stage 상태에 있는 것은 그대로 두고 commit message만 수정함

## Staged 상태를 Unstaged 상태로 변경하기
**git reset**

```
$ git reset HEAD filename
```
reset 명령은 unstaged 상태로만 변경하고 다른 동작은 수행하지 않는다.

## Modified 파일을 clone 한 상태 시점으로 되돌리기 (수정을 버리기)
**git checkout -- <file>**
git status 명령에도 되돌리는 방법이 기술돼 있다.

```
PS C:\Users\krnom\work\git_tutorial> git status
On branch master
Your branch is ahead of 'origin/master' by 1 commit.
  (use "git push" to publish your local commits)

Changes not staged for commit:
  (use "git add <file>..." to update what will be committed)
  (use "git checkout -- <file>..." to discard changes in working directory)

        modified:   HowToUseGit.md

no changes added to commit (use "git add" and/or "git commit -a")
```

단, 수정한 파일이 전부 되돌아 감으로 사용에 주의해야 한다.

## 기타 tip
1. git status 짧막하게 확인하기

```
[jwkang2@localhost git_tutorial]$ git status -s
M README
```

2. 파일 무시하기
.gitignore 파일을 생성하고 다음과 같이 입력하면 확장자가 o나 a인 파일을 무시하라는 의미가 된다.

```
[jwkang2@localhost git_tutorial]$ cat .gitignore
*.[oa]
```

gitignore 예시

```
# 현재 디렉토리에 있는 TODO 파일 무시
/TODO

# build 디렉토리 이하는 전부 무시
build/

# doc 디렉토리 이하의 모든 pdf 파일을 무시
doc/**/*.pdf
```

https://github.com/github/gitignore 에서 더 상세한 예를 확인할 수 있다.
