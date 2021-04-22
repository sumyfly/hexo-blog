---
title: git
date: 2017-12-29 16:52:21
tags:
  - git
---

### 1. rename git branch locally and remotely

``` bash
git branch -m old_branch new_branch         # Rename branch locally    
git push origin :old_branch                 # Delete the old branch    
git push --set-upstream origin new_branch   # Push the new branch, set local branch to track the new remote
```

[git hub refference](https://gist.github.com/lttlrck/9628955)

### 2. github PR的mergefangs
#### 2.1 Create a merge commit
All commits from this branch will be added to the base branch via a merge commit.
创建一个专门用于合并的commit,保留原始的commit id.

#### 2.2 Squash and merge
The 1 commit from this branch will be added to the base branch.
创建一个commit,包含所有的changes，但是commit id丢失了。

#### 2.3 Rebase and merge
The 1 commit from this branch will be rebased and added to the base branch.
这里的1 commit我不是很理解，其实是整体一组commits。它们的修改时间（Date)没有变化，但是提交时间(commite Date)时间统一变为rebase命令的执行时间。
重新Rebase对象的分支，然后所有的commit id和commit Date变了，changes,commit message，Date没变。

> git 做diff时，是按照一个起始点，然后把所有changes显示出来。如果是三方合并，它们是从共同起始点开始，看相同的行有没有修改。所以如果从master到development或者相反的方向，**不要**使用rebase,也不要使用Squash,因为它们会修改**commit的id**，导致merge的时候出现冲突。

> 默认commit的时候，Date和commit Date是一样的，但是rebase之后commit Date的时间变成了rebase执行时的时间。

怎么选项merge方式：
- feature、fix分支到development，三种方式都可以选择，优先选择Rebase,保持commit一条直线。
- development、release、master分支由于要相互间持续的merge，所以它们之间的merge方法选择第一种，Create a merge commit。这种方式会保留commit id.那么merge的时候冲突就少了。

### 3. rebase操作
#### 3.1 同一个分支
git pull --rebase,如果有冲突，解决冲突后git rebase --continue.

#### 3.2 不同分支
git rebase target,把head rebase到target的Head上，然后本地修改会以它为起点，产生diff。之后执行git rebase --continue.
然后远程分支直接强制push。git push -f