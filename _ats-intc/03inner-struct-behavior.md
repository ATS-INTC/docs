---
title: ATS-INTC Inner Structures and Behavior
author: Fangliang Zhao
date: 2024-01-16
category: docs
layout: post
---

### Inner Structures and Behavior

假设在 FPGA 中实现

4 个在线数据结构，优先级数量为 8，队列深度为 256。

支持的中断数量 6，6个队列，每个队列深度为 256。

支持的最大进程数量为 16，每个进程需要一个状态描述符，（8 + 8）* 16 = 256 字节


#### Inner Structures

##### Blocked Queue for External Device

##### Priority Scheduler

##### IPC Message

##### Blocked Queue for IPC

##### Process Status Table

```Rust
pub struct ProcStatus {
    is_online: bool,
    ps_bf: u64,
    ipc_bf: u64,
}
```


#### Inner Behavior



##### External Device Line Interrupt

##### IPC Send Signal