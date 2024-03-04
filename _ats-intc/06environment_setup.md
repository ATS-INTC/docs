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

### 4. Checkout vivado project and Generate bitstream

After running benchmarks successfully, you can use the digilent-vivado-scripts to checkout vivado project.(You must be under the paltform directory.)

1. Using `make checkout` to create the vivado project in batch mode. 
2. Open the project in vivado GUI and change the top module from `soc_wrapper.v` to `system_wrapper.v`. 
3. Click the `generate_bitstream` button in the left.
4. After writing bitstream successfully, you must export the hardware(include bitstream) to the `$PLATFORM/proj` directory.
5. Using `make gen_bin` to generate the target bitstream used in FPGA and using `make upload` to upload the target bitstream and dtbo to the arm-linux in the PS side of FPGA.(If you use the other way to communicate with the arm-linux, you do not need to use this `script`.)