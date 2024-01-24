---
title: Deep Dive of the ShiftQueue in Rocket-chip
author: Fangliang Zhao
date: 2024-01-24
category: docs
layout: post
---

### The Implementation of ShiftQueue

```scala
/** Implements the same interface as chisel3.util.Queue, but uses a shift
  * register internally.  It is less energy efficient whenever the queue
  * has more than one entry populated, but is faster on the dequeue side.
  * It is efficient for usually-empty flow-through queues. */
class ShiftQueue[T <: Data](gen: T,
                            val entries: Int,
                            pipe: Boolean = false,
                            flow: Boolean = false)
    extends Module {
  val io = IO(new QueueIO(gen, entries) {
    val mask = Output(UInt(entries.W))
  })

  private val valid = RegInit(VecInit(Seq.fill(entries) { false.B }))
  private val elts = Reg(Vec(entries, gen))

  for (i <- 0 until entries) {
    def paddedValid(i: Int) = if (i == -1) true.B else if (i == entries) false.B else valid(i)

    val wdata = if (i == entries-1) io.enq.bits else Mux(valid(i+1), elts(i+1), io.enq.bits)
    val wen =
      Mux(io.deq.ready,
          paddedValid(i+1) || io.enq.fire() && ((i == 0 && !flow).B || valid(i)),
          io.enq.fire() && paddedValid(i-1) && !valid(i))
    when (wen) { elts(i) := wdata }

    valid(i) :=
      Mux(io.deq.ready,
          paddedValid(i+1) || io.enq.fire() && ((i == 0 && !flow).B || valid(i)),
          io.enq.fire() && paddedValid(i-1) || valid(i))
  }

  io.enq.ready := !valid(entries-1)
  io.deq.valid := valid(0)
  io.deq.bits := elts.head

  if (flow) {
    when (io.enq.valid) { io.deq.valid := true.B }
    when (!valid(0)) { io.deq.bits := io.enq.bits }
  }

  if (pipe) {
    when (io.deq.ready) { io.enq.ready := true.B }
  }

  io.mask := valid.asUInt
  io.count := PopCount(io.mask)
}
```
### The Internal structures

#### valid

```scala
private val valid = RegInit(VecInit(Seq.fill(entries) { false.B }))
```

It is related to the `mask` interface. The `valid(i)` is according to the `valid(i + 1)` and the `enq`, `deq` interface.

```scala
valid(i) :=
      Mux(io.deq.ready,
          paddedValid(i+1) || io.enq.fire() && ((i == 0 && !flow).B || valid(i)),
          io.enq.fire() && paddedValid(i-1) || valid(i))
```

#### elts

```scala
private val elts = Reg(Vec(entries, gen))
```

### The Interfaces of ShiftQueue

The first four interfaces are the same as those of the `Queue` in chisel. The `mask` is a new interface of `ShiftQueue`. The `enq` and `deq` are the `DecoupledIO` (or `ReadyValid`) which has `ready`, `valid`, `bits` signals.

#### enq

The `ready` signal is related to the last valid bit.

```scala
io.enq.ready := !valid(entries-1)
```

#### deq

```scala
io.deq.valid := valid(0)
io.deq.bits := elts.head
```

#### count

It is the current amount of data in the queue.

#### flush

#### mask

It is the bitmap that indicates which cell has a data. The `count` can be derive from the `mask` by using `PopCount` function.





