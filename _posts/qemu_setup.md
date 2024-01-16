---
title: Qemu Environment Setup
author: Fangliang Zhao
date: 2024-01-16
category: Qemu
layout: post
---

### How to setup a `QEMU` environment

```sh
git clone https://github.com/ATS-INTC/qemu.git
cd qemu
./configure --target-list="riscv64-softmmu" --enable-slirp
make -j
```