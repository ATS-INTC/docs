---
title: ATS-INTC Register Space
author: Fangliang Zhao
date: 2024-01-16
category: docs
layout: post
---

### Register Space

The `ATS-INTC` register space is shown below. The registers are memory-mapped into non-cacheable memory space. The memory space must be aligned on 32-bit boundary. All registers are in Little Endian format.

```sh
MSB                           LSB
31    24 23   16 15    8 7      0
+-------+-------+-------+-------+
| BYTE3 | BYTE2 | BYTE1 | BYTE0 |
+-------+-------+-------+-------+
```

|  Address Space Offset  |                      Name                     |
| ---------------------- | --------------------------------------------- |
| 0x0000 - 0x0800        | Priority Scheduler Descriptor of Process 0    |
| 0x0800 - 0x1000        | Priority Scheduler Descriptor of Process 1    |
|        ......          |                    ......                     |
| 0x7F_F800 - 0x80_0000  | Priority Scheduler Descriptor of Process 4095 |
| 0x80_0000 - 0x80_0100  | IPC Descriptor of Process 0                   |
| 0x80_0100 - 0x80_0200  | IPC Descriptor of Process 1                   |
|        ......          |                  ......                       |
| 0x8F_FF00 - 0x90_0000  | IPC Descriptor of Process 4095                |
| 0x90_0000 - 0x90_2128  | External Interrupt Handler Descriptor         |

#### Priority Queue Descriptor

This structure is used for process to operate the priority scheduler in `ATS-INTC`.

```sh
0      0x20     0x28      0x30       0x38    0x7F8        0x800   Byte
+---------+--------+---------+----------+--------+------------+
| control | membuf | dequeue | enqueue0 | ...... | enqueue249 |
+---------+--------+---------+----------+--------+------------+
```

##### Control Field Detail

<!-- ```sh
0         8        16        24         32 Byte
+---------+---------+---------+---------+
| bitmap0 | bitmap1 | bitmap2 | bitmap3 |
+---------+---------+---------+---------+
``` -->

```sh
0                                   249 250 251    255 Bit
+-------------------------------------+---+---+------+
| *********************************** | # | % | ---- |
+-------------------------------------+---+---+------+
```

Each bit in bitmap* is used to ensure mutually exclusive access between each operation.
1. \* means this bit is used for the enqueue operation.
2. \# means this bit is used for the dequque operation.
3. % means this bit is used for the recording memory buffer operation.
4. \- means this bit is reserved.

#### IPC Descriptor (Not completed)

This structure is used for process to launch IPC using `ATS-INTC`.

```sh
0       0x8     0x10          0x88      0x90      0x98     0xf8      0x100   Byte
+---------+--------+-------------+---------+---------+--------+----------+
| control | membuf | ipc_content | ipc_bq0 | ipc_bq1 | ...... | ipc_bq14 |
+---------+--------+-------------+---------+---------+--------+----------+
```

#### External Interrupt Handler Descriptor

This structure is used for kernel to operate the queues in which the tasks are blocked on external devices in `ATS-INTC`.

```sh
0      0x80       0x88       0x90     0x38         0x128   Byte
+---------+----------+----------+--------+-------------+
| control | enqueue0 | enqueue1 | ...... | enqueue1023 |
+---------+----------+----------+--------+-------------+
```