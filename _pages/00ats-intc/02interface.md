---
title: ATS-INTC Interface
author: Fangliang Zhao
date: 2024-01-16
category: docs
layout: post
---

### Interfaces provided for Operating System

#### Interfaces of priority scheduler:

| Interface |       Arguments        |     Output   | Description |
|-----------|------------------------|--------------|-------------|
| push      | priority, task pointer |              | It writes the address of enqueue_priority with task pointer. |
| fetch     |                        | task pointer | It reads the task pointer from the address of dequeue. |
| rmembuf   | memory buffer pointer  |              | It writes the address of membuf with the memory buffer pointer. |


#### Interfaces of IPC:

| Interface |       Arguments        |     Output   | Description |
|-----------|------------------------|--------------|-------------|
| send      | ipc arguments          |              | It writes the address of arguments with IPC results. |
| wait      |                        | ipc res      | It waits the IPC results from the address of results. |
| rmembuf   | memory buffer pointer  |              | It writes the address of membuf with the memory buffer pointer. |


#### Interfaces of blocked task queue:

| Interface |       Arguments        |     Output   | Description |
|-----------|------------------------|--------------|-------------|
| push      | irq, task pointer      |              | It writes the address of enqueue_irq with blocked task pointer. |


> **Note**: All operations must wait for the flag in related control field being cleaned.