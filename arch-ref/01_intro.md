Introduction
=========================================================================
The Epiphany architecture defines distributed shared-memory computing fabric. It consists of a 2D array of compute nodes connected by a low-latency mesh network-on-chip.

**Figure 1.1:** An Implementation of the Epiphany Architecture

![alt tag](../figures/epiphany_arch.jpg)

Figure 1 highlights the key Epiphany components:

* Distributed shared memory with 32 bytes/cycle of sustained bandwidth per node.

* A 2D mesh Network-On-Chip for node-to-node communication

* A static dual issue superscalar floating-point RISC core

The Epiphany architecture was designed for real time signal processing applications. Examples of such application domains include: image processing, communication, sensor signal processing, encryption, and compression. High speed inter-processor communication is supported by the Epiphany architecture’s 2D eMesh Network-On-Chip (NOC), shown in Figure 2, which connects the on-chip processor nodes. The mesh network efficiently handles traffic patterns in high-throughput real-time applications. The network takes advantage of spatial locality and an abundance of short point-to-point on-chip wires to send complete transactions—consisting of source address, destination address, and data—in a single clock cycle. Each routing link can transfer up to 8 bytes of data on every clock cycle, allowing 64 bytes of data to flow through every routing node on every clock cycle. 

**Figure 1.2:** eMesh Network-On-Chip

![alt tag](../figures/emesh.jpg)

The memory map of the Epiphany architecture is flat and unprotected. Every mesh node has direct access to the complete memory system, without limitation.

The shared-memory architecture and low-latency, on-chip mesh network allows multicore programs to pass messages from a few bytes to kilobytes with very little overhead. The high bandwidth and low latency of the eMesh NOC means the Epiphany can support parallel programming at a large kernel as well as fine-grained level in which small tasks can be executed in parallel.
 
**Epiphany strengths:**

* **Ease of Use:** A multicore architecture that is ANSI-C/C++ programmable. This makes the architecture accessible to every programmer, regardless of his or her level of expertise.

* **Effectiveness:** The general-purpose instruction set, superscalar instruction issue, and large unrestricted register file ensures that the application code written in ANSI-C can approach the peak theoretical performance of the Epiphany architecture. 

* **Scalability:** The architecture scales to thousands of cores on a single chip and millions of cores within a larger system. This provides the basis for future performance gains from increased parallelism.

The Epiphany architecture is programming-model neutral and compatible with most popular parallel-programming methods, including Single Instruction Multiple Data (SIMD), Single Program Multiple Data (SPMD), Host-Slave programming, Multiple Instruction Multiple Data (MIMD), static and dynamic dataflow, systolic array, shared-memory multi-threading, message-passing, and communicating sequential processes (CSP). Adapteva anticipates that with time, the ecosystem around the Epiphany multicore architecture will grow to include many of these methods.

Epiphany features that enable effective parallel programming include:

* General-purpose processors that support task level programming at each node.

* Distributed shared-memory that minimizes communication overhead.

* Distributed network-on-chip technology

* Built-in hardware support for efficient multi program data-sharing.

----
Copyright 2008-2016 Adapteva Inc. All rights reserved.
(See LICENSE.md for details)
