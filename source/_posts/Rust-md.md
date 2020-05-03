---
title: Rust.md
date: 2019-11-25 10:31:55
tags:
---

### 1. rustup
```sh
rustup default nightly # use nightly version
rustup default stable # use stable version
rustup update # update rust version
```

<!-- more -->

### 2. cargo build 时 Blocking waiting for file lock on build directory
``` bash
pkill rls
rm -rf ~/.cargo/registry/index/*
```
https://stackoverflow.com/questions/47565203/cargo-build-hangs-with-blocking-waiting-for-file-lock-on-the-registry-index-a?rq=1
