---
title: beyond-compare
date: 2018-07-30 14:22:23
tags:
---
### mac
运行beyond compare4的安装命令行工具，然后设置git

``` bash
git config --global diff.tool bc4
git config --global difftool.prompt false
git config --global difftool.bc4.cmd '"/usr/local/bin/bcomp" "$LOCAL" "$REMOTE"'

git config --global merge.tool bc4
git config --global mergetool.prompt false
git config --global mergetool.bc4.cmd '"/usr/local/bin/bcomp" "$LOCAL" "$REMOTE" "$BASE" "$MERGED"'
git config --global mergetool.bc4.trustexitcode true
```

https://blog.csdn.net/yu12377/article/details/73526751

<!-- more -->