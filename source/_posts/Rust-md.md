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

### 3. vs code debug rust
安装CodeLLDB、Rust(rls)插件，然后配置launch.json。
``` json
    {
      "type": "lldb",
      "request": "launch",
      "name": "Debug executable 'sslocal'",
      "cargo": {
        "args": ["build", "--bin=sslocal", "--package=shadowsocks-rust"],
        "filter": {
          "name": "sslocal",
          "kind": "bin"
        }
      },
      "args": ["-c", "${workspaceFolder}/config.json"],
      "cwd": "${workspaceFolder}",
      "env": { "RUST_BACKTRACE": "full" },
      "stopOnEntry": false,
      "sourceLanguages": ["rust"],
      "sourceMap": {
        // 这个配置可以debug std lib
        "/rustc/8d69840ab92ea7f4d323420088dd8c9775f180cd": "${env:HOME}/.rustup/toolchains/stable-x86_64-apple-darwin/lib/rustlib/src/rust"
      }
    },
```

https://jason-williams.co.uk/debugging-rust-in-vscode