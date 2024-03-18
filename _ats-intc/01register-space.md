---
title: ATS-INTC Register Space
author: Fangliang Zhao
date: 2024-01-16
category: docs
layout: post
---

### Overview

The `ATS-INTC` register space is shown below. The registers are memory-mapped into non-cacheable memory space. The memory space must be aligned on 32-bit boundary. 

|  Address Space Offset  |                      Name                     |
| ---------------------- | --------------------------------------------- |
| 0x0000 - 0x0800        | Priority Scheduler Descriptor of Process 0    |
| 0x0800 - 0x0900        | IPC Descriptor of Process 0                   |
| 0x0900 - 0x1000        | External Interrupt Handler Descriptor 0       |
| 0x1000 - 0x1800        | Priority Scheduler Descriptor of Process 1    |
| 0x1800 - 0x1900        | IPC Descriptor of Process 1                   |
| 0x1900 - 0x2000        | External Interrupt Handler Descriptor 1       |
|        ......          |                    ......                     |
| 0xFF_E000 - 0xFF_E800  | Priority Scheduler Descriptor of Process 4094 |
| 0xFF_E800 - 0xFF_E900  | IPC Descriptor of Process 4094                |
| 0xFF_E900 - 0xFF_F000  | External Interrupt Handler Descriptor 4094    |
| 0xFF_F000 - 0xFF_F800  | Priority Scheduler Descriptor of Process 4095 |
| 0xFF_F800 - 0xFF_F900  | IPC Descriptor of Process 4095                |
| 0xFF_F900 - 0x100_0000 | External Interrupt Handler Descriptor 4095    |


### Priority Queue Descriptor

This structure is used for process to operate the priority scheduler in `ATS-INTC`.

```sh
0      0x08       0x10       0x18       0x20    0x7F8        0x800   Byte
+---------+----------+----------+----------+--------+------------+
| dequeue | enqueue0 | enqueue1 | enqueue2 | ...... | enqueue254 |
+---------+----------+----------+----------+--------+------------+
```


### IPC Descriptor

This structure is used for process to launch IPC using `ATS-INTC`.

```sh
0    0x8      0x10      0x18      0x20      0x28     0x90       0x98      0x100   Byte
+------+---------+---------+---------+---------+--------+----------+----------+
| send | ipc_bq0 | ipc_bq1 | ipc_bq2 | ipc_bq3 | ...... | ipc_bq29 | ipc_bq30 |
+------+---------+---------+---------+---------+--------+----------+----------+
```

### External Interrupt Handler Descriptor

This structure is used for kernel to operate the queues in which the tasks are blocked on external devices in `ATS-INTC`.

```sh
0       0x08       0x10       0x18   0x1120        0x700   Byte
+----------+----------+----------+--------+------------+
| enqueue0 | enqueue1 | enqueue2 | ...... | enqueue223 |
+----------+----------+----------+--------+------------+
```