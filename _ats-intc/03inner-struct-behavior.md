---
title: ATS-INTC Inner Structures and Behavior
author: Fangliang Zhao
date: 2024-01-16
category: docs
layout: post
---


### Inner Structures

We only support 4 groups of internal data structures. Each group consists of two data structures: **Priority Scheduler** and **IPC Handler Queues**. The depth of each queue is 256.

#### Priority Scheduler

Each Priority Scheduler consists of 8 priority queue.

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



#### External Device Line Interrupt

#### IPC Send Signal