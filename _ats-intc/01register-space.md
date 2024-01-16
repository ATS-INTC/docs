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
| 0x0900 - 0x1000        | Reserved                                      |
| 0x1000 - 0x1800        | Priority Scheduler Descriptor of Process 1    |
| 0x1800 - 0x1900        | IPC Descriptor of Process 1                   |
| 0x1900 - 0x2000        | Reserved                                      |
|        ......          |                    ......                     |
| 0xFF_C000 - 0xFF_C800  | Priority Scheduler Descriptor of Process 4092 |
| 0xFF_C800 - 0xFF_C900  | IPC Descriptor of Process 4092                |
| 0xFF_C900 - 0xFF_D000  | Reserved                                      |
| 0xFF_D000 - 0xFF_F128  | External Interrupt Handler Descriptor         |


### Priority Queue Descriptor

This structure is used for process to operate the priority scheduler in `ATS-INTC`.

```sh
0      0x20     0x28      0x30       0x38    0x7F8        0x800   Byte
+---------+--------+---------+----------+--------+------------+
| control | membuf | dequeue | enqueue0 | ...... | enqueue249 |
+---------+--------+---------+----------+--------+------------+
```

#### Control Field Detail

```sh
0                                   249 250 251    255   Bit
+-------------------------------------+---+---+------+
| *********************************** | # | % | ---- |
+-------------------------------------+---+---+------+
```

Each bit in bitmap* is used to ensure mutually exclusive access between each operation.
1. \* means this bit is used for the enqueue operation.
2. \# means this bit is used for the dequque operation.
3. % means this bit is used for the recording memory buffer operation.
4. \- means this bit is reserved.

### IPC Descriptor (Not completed)

This structure is used for process to launch IPC using `ATS-INTC`.

```sh
0       0x8     0x10                  0x18      0x20      0x28     0x90       0x98      0x100   Byte
+---------+--------+---------------------+---------+---------+--------+----------+----------+
| control | membuf | ipc message pointer | ipc_bq0 | ipc_bq1 | ...... | ipc_bq15 | reserved |
+---------+--------+---------------------+---------+---------+--------+----------+----------+
```

### External Interrupt Handler Descriptor

This structure is used for kernel to operate the queues in which the tasks are blocked on external devices in `ATS-INTC`.

```sh
0      0x80       0x88       0x90    0x120         0x128   Byte
+---------+----------+----------+--------+-------------+
| control | enqueue0 | enqueue1 | ...... | enqueue1023 |
+---------+----------+----------+--------+-------------+
```