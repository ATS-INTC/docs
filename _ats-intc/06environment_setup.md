---
title: The Environment Setup of ats-intc-rocket-chip project
author: Fangliang Zhao
date: 2024-01-30
category: docs
layout: post
---


### 1. Rocket-tools

You must install rocket-tools firstly. It is used when running the simulation and benchmarks of rocket-chip. 

1. Download a pre-built package from [release](https://github.com/chipsalliance/rocket-tools/releases).
2. Unzip the downloaded package to `/opt`(The target directory is `/opt/riscv`).
3. Export `RISCV` environment variable.
   ```sh
   vim ~/.zshrc
   RISCV=/opt/riscv
   export RISCV
   export PATH=$PATH:$RISCV/bin
   source ~/.zshrc
   ```

### 2. Checkout the code

```sh
git clone https://github.com/ATS-INTC/ats-intc-rocket-chip.git --recursive
```

### 3. Run Simulation of rocket-chip and customized benchmarks

You must enter the platform directiory(such as axu15eg). Then you can use one-line command to run customized benchmarks.

```sh
make test_one CASE=atsintc
```

The `CASE` variable is the target benchmark which is under the benchmarks directory of `riscv-tests` submodule.

If you would like to run all benchmarks, you just need to run `make test`.

> If you can not get verilator from github, please add the ip of github.com in your `/etc/hosts`.
> Maybe you need to install `flex` and `bison`.(`sudo apt install flex bison`)

