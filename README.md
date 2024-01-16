---
layout: home
title: Overview
permalink: /
---

This Repository is the overall description documents of `ATS-INTC`.

We assume that you have create a `ats-intc` directory. You need to change the current directory to `ats-intc`.

## QEMU environment setup

```sh
git clone https://github.com/ATS-INTC/qemu.git
cd qemu
./configure --target-list="riscv64-softmmu" --enable-slirp
make -j
```

## ATS-INTC

The documents related to `ATS-INTC` hardware specification is under [`ats-intc`](./ats-intc/) directory.

