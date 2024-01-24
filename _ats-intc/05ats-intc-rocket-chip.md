---
title: The Implementation of ATS-INTC in Rocket-chip
author: Fangliang Zhao
date: 2024-01-19
category: docs
layout: post
---


### How to add a interrupt controller in rocket-chip

The implementation of plic in rocket-chip is in `rocket-chip/src/main/scala/devices/tilelink/Plic.scala`. Then We just follow the implementation of plic and add a customized device to rocket-chip.

1. Create a sub directory `atsintc`, it also includes the `Config` and `Top` module of customized rocket-chip.
2. Create the `ATSINTC.scala` in the `atsintc` directory, it defines the `ATSINTCKey`, `ATSINTCAttachParams`, `ATSINTCAttachKey`, `CanHavePeripheryATSINTC`.
3. Add the `ATSINTCKey` to the `BaseSubsystemConfig` in the `Config.scala` file in the `subsystem` directory.
4. Add the `CanHavePeripheryATSINTC` to the `HasTileInterruptSources` in the `HasTiles.scala` file in the `subsystem` directory.

Then we can get the device-tree information about our customized `ATSINTC` while generating the rocket-chip.


### Add MMIO Read & Write

The method of how to add MMIO register map is included in [Register Router](https://chipyard.readthedocs.io/en/stable/TileLink-Diplomacy-Reference/Register-Router.html) and [MMIO Peripherals](https://chipyard.readthedocs.io/en/stable/Customization/MMIO-Peripherals.html).

#### Bug

But when we combined the [ShiftQueue](https://github.com/chipsalliance/rocket-chip/blob/master/src/main/scala/util/ShiftQueue.scala) with MMIO read and write operations, we got a bug and did not know how to deal with it. The implementation and the result of test benchmark are shown below.

![queue-bug](https://ats-intc.github.io/docs/assets/gitbook/images/queue-bug.png)

![queue-res](https://ats-intc.github.io/docs/assets/gitbook/images/queue-res.png)
