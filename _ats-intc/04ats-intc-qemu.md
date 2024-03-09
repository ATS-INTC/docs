---
title: The Implementation of ATS-INTC in QEMU
author: Xi Luo
date: 2024-03-08
category: docs
layout: post
---

### Features & TODOs

* [x] Async task scheduler
* [ ] IPC controller
* [x] Interrupt controller

### Implementation

We follow the implementation of PLIC (Platform Level Interrupt Controller) in `hw/intc/sifive_plic.c` & `include/hw/intc/sifive_plic.h` to add a customized device to QEMU's RISCV VirtIO Board, and implement it with the design described in these documents.

#### 1. Add one customized device to QEMU.

We choose to add our device to a separate directory. So we crate subdirectory `ats` in both `hw` and `include/hw` directory. Then, modify `hw/Kconfig` and `hw/meson.build` to append the directory we crate.

In `hw/ats`, we create files `Kconfig`, `meson.build`, and `riscv_lite_executor.c`. In `include/hw/ats`, we create file `riscv_lite_executor.h`. The content of these files is similar to those files of PLIC (`hw/intc/Kconfig`, `hw/intc/meson.build`, `hw/intc/sifive_plic.c` and `include/hw/intc/sifive_plic.h`), in order to form a basic framework of a device and add it into QEMU’s compile system.

#### 2. Add this device to the VirtIO Board.

In `hw/riscv/Kconfig`, append `select RISCV_LITE_EXECUTOR` to the `config RISCV_VIRT` block.

In `include/hw/riscv/virt.h`, add `VIRT_LITE_EXECUTOR` to the `VIRT_` `enum`, which is used for MMIO region.

In `hw/riscv/virt.c`, we need to modify three parts:

- Modify `virt_memmap` array to add `ATS-INTC`’s MMIO region.
- Modify `virt_machine_init` function to crate `ATS-INTC` instance.
- Modify `create_fdt_sockets` function to add `ATS-INTC` to the board’s FDT (Flatted Device Tree).

#### 3. Receive interrupts

Add `riscv_lite_executor_irq_request` function to `ATS-INTC`’s code (`hw/ats/riscv_lite_executor.c` and `include/hw/ats/riscv_lite_executor.h`).

In the `riscv_lite_executor_realize` function of `hw/riscv/virt.c`, call `qdev_init_gpio_in` function to connect the board’s interrupt line to `ATS-INTC`’s `riscv_lite_executor_irq_request` function. After that, this function will be called when interrupt arrives.

#### 4. Add MMIO read & write

In `ATS-INTC`’s code:

Create two new functions, `riscv_lite_executor_read` and `riscv_lite_executor_write`.

Register them into the `MemoryRegionOps` `struct`, `riscv_lite_executor_ops`.

In `riscv_lite_executor_realize` function, call `memory_region_init_io` to register `riscv_lite_executor_ops` as the MMIO operator of this device.

#### 5. Implement inner queues

We use QEMU’s `QSIMPLEQ` data structure as our queue implementation. Usage of `QSIMPLEQ` can be found at [this article](https://gitee.com/LC_rosy/weekly-progress/blob/master/24.1.9~24.1.15/queue.h%E6%BA%90%E7%A0%81%E9%98%85%E8%AF%BB.md).

With inner queues crated, we implement these three functions in `ATS-INTC`’s code, making this device’s behavior correspond to [the design](https://ats-intc.github.io/docs/ats-intc/03inner-struct-behavior/):

- `riscv_lite_executor_irq_request`
- `riscv_lite_executor_read`
- `riscv_lite_executor_write`


