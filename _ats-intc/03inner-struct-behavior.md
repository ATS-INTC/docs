---
title: ATS-INTC Inner Structures and Behavior
author: Fangliang Zhao
date: 2024-01-16
category: docs
layout: post
---


### Inner Structures

The **Priority Scheduler** and **IPC Handler Queues** belong to the specific kernel or user process. We only support four groups of two structures. The **Blocked Queue for External Device** and **Process Status Table** belong to the whole system and maintaine the global information, so we just need support one. 

#### Priority Scheduler

The Priority Scheduler consists of 8 priority queues. The implementation in the QEMU is the multi-FIFO queue. 

![multi-fifo](https://ats-intc.github.io/docs/assets/gitbook/images/multi-fifo.png)

The implementation in the Rocket-chip is based on this [paper](https://ieeexplore.ieee.org/document/4380693). The repository is [here](https://github.com/zflcs/chisel-priority-queue).

#### IPC Blocked Queues

Each IPC Blocked Queues consists of 2 ipc blocked queue.

#### Blocked Queue for External Device

Each external device corresponds to a blocking queue. The maximum number of supported external devices is 6.

#### Process Status Table

Each process， no matter the process is online or offline, corresponds to a `Status Item` in Process Status Table. The total number of supported process is 16. The structure of `Status Item` is shown below.

```sh
0      0x08       0x10   Byte
+---------+----------+
| ps_mbuf | ipc_mbuf |
+---------+----------+
```

The lower 8 bytes record the memory buffer pointer of `Priority Scheduler` and the upper 8 bytes record the memory buffer pointer of `IPC Blocked Queues`. We use the lowest bit to indicate wheather the related process is online or offline(The memory buffer must be 2-byte aligned.).

### Inner Behavior

#### The Behavior of ATSINTC

![behavior](https://ats-intc.github.io/docs/assets/gitbook/images/behavior.png)

#### The basic function of priority scheduling

![priority scheduling](https://ats-intc.github.io/docs/assets/gitbook/images/priorityscheduler.svg)

The `push` and `fetch` operation is performed based on the assigned MMIO address.

#### External Device Line Interrupt handler

![external interrupt handling](https://ats-intc.github.io/docs/assets/gitbook/images/extintr.svg)

1. It Receives an interrupt from an external device and obtains the interrupt vector number.
2. It takes out a task from the corresponding device blocking queue based on the interrupt vector number.
3. It inserts the fetched task into the kernel's highest priority task queue.

#### IPC Send Signal

![syscall](https://ats-intc.github.io/docs/assets/gitbook/images/syscall.svg)

![ipc](https://ats-intc.github.io/docs/assets/gitbook/images/ipc.svg)

1. It receives the sender's IPC initiation signal and obtains the receiver's process id and IPC type number.
2. It searches the process status table. If the receiver is online, it takes out the corresponding blocking IPC handler coroutine from the receiver's IPC blocking queue according to the IPC type number; If the receiver is offline, it reads the memory according to the ipc_mbuf pointer in the status table. This operation may require several memory reads and writes.
3. It adds the fetched IPC handler coroutine to the receiver's highest priority queue. If the receiver is not online, the memory is read according to ps_mbuf in the status table.

   