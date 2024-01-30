---
title: Qemu Environment Setup
author: Fangliang Zhao
date: 2024-01-16
category: docs
layout: post
---

### How to setup a `QEMU` environment

```sh
git clone https://github.com/ATS-INTC/qemu.git
cd qemu
./configure --target-list="riscv64-softmmu" --enable-slirp
make -j
```

> Maybe some packages are required such as ninja-build, libglib2.0-dev. You can use this command to install them. `sudo apt install ninja-build libglib2.0-dev`

