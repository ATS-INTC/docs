---
title: ATS-INTC Interface
author: Fangliang Zhao
date: 2024-01-16
category: docs
layout: post
---


### Interfaces of priority scheduler:

| Interface |       Arguments        |     Output   | Description |
|-----------|------------------------|--------------|-------------|
| push      | priority, task pointer |              | These two arguments are priovided for software. when the task is created or a blocked task is waked by another task, the software will write the task pointer to the MMIO `enqueue_priority` register. |
| fetch     |                        | task pointer | When the previous task is finished or blocked, the software must fetch another task by reading the task pointer from the MMIO `dequeue` register. |
| rmembuf   | memory buffer pointer  |              | When the amount of task has exceed the limitation, the software must apply a spare memory buffer and write the start address of memory buffer to the MMIO `membuf` register. |


### Interfaces of IPC:

| Interface |           Arguments           |    Output    | Description |
|-----------|-------------------------------|--------------|-------------|
| push      | ipc type, ipc handler pointer |              | The receiver registes the IPC handler by write it to the MMIO `ipc_bqi` register. |
| send      | ipc type, receiver's pid      |              | The sender launch a IPC by write the ipc type and the receiver's pid to the MMIO `send` register. |
| rmembuf   | memory buffer pointer         |              | When the amount of IPC handler has exceed the limitation, the software must apply a spare memory buffer and write the start address of memory buffer to the MMIO `membuf` register. |


### Interfaces of external device blocked task queue:

| Interface |       Arguments        |     Output   | Description |
|-----------|------------------------|--------------|-------------|
| push      | irq, task pointer      |              | It writes the task pointer to the MMIO `enqueueirq` register. |


> **Note**: All operations must wait for the flag in related control field being cleaned.