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
