Memory Architecture
============================================================================
## Memory Address Map

The Epiphany architecture uses a single, flat address space consisting of $2^{32}$ 8-bit bytes. Byte addresses are treated as unsigned numbers, running from 0 to $2^{32} - 1$. This address space is regarded as consisting of $2^{30}$ 32-bit words, each of whose addresses is word-aligned, which means that the address is divisible by 4. The word whose word-aligned address is A consists of the four bytes with addresses A, A+1, A+2 and A+3. Each mesh node has a local, aliased, range of memory that is accessible by the mesh node itself starting at address 0x0 and ending at address 0x00007FFF. Each mesh node also has a globally addressable ID that allows communication with all other mesh nodes in the system. The mesh-node ID consists of 6 row-ID bits and 6 column-ID bits situated at the upper most-significant bits (MSBs) of the address space. The complete memory map for the 32 bit Epiphany architecture is shown in Figure 1.


**Figure 2.1:** Epiphany Global Address Map

![alt tag](../figures/memorymap.jpg)

Data can be accessed diretly from local memory, another core's memory, or from external memory. Instructions can only fetched directly from local memory. Performance is optimized when the data and instructions are placed in separate local-memory banks.

Figure 2 shows a 64-node region of the memory map, highlighting the upper address range of each mesh node and its corresponding mnemonic (row, column) coordinate. The memory map is laid out as a mesh to match the natural geometrical mapping of Epiphany’s Network-On-Chip. The dotted line in Figure 6 shows the I/O boundary and memory map for a hypothetical system consisting of four 16-core chips connected in a glue-less fashion on a board. A 32-bit address map supports up to 4095 cores in a single shared memory system. In most practical systems some of the memory space will be dedicated to off-chip SDRAM and memory mapped IO peripherals.

**Figure 2.2:** Epiphany Shared Memory Map

![alt tag](../figures/memory_example.jpg)

Each CPU can access any other CPU's memory by specifying the appropriate row-column fields of the address in a memory read or write transactions. The startup cost for node-to-node communication is zero clock cycles. From a programmer's viewpoint, the only difference between on-chip communication and off-chip communication is in transaction bandwidth and latency. In the Figure 6 memory map, if core (32,32) wants to perform a read operation from core (39,39), it would send a read address with the upper bits 0x9e7 and specify a return address with upper bits 0x820 to the mesh network. The network takes care of the rest, making sure that the read request propagates to the read destination and that data is correctly returned to the mesh node that initiated the request.

## Memory Order Model

All read and write transactions from local memory follow a strong memory-order model. This means that the transactions complete in the same order in which they were dispatched by the program sequencer.

For read and write transactions that access non-local (remote) memory, the memory order restrictions are relaxed to improve performance. This is called a weak memory-order model. In the Epiphany memory order model, the only guarantees are:
* Load operations complete before the returned data is used by a subsequent instruction.
* Load operations using data previously written use the updated values.
* Store operations eventually propagate to their ultimate destination.

Table 1 shows the ordering guaranteed in the Epiphany architecture. Instruction #1 refers to the first instruction in a sequential program, and instruction #2 refers to any instruction following the first one in that same program. Programs relying on strict memory ordering must use appropriate fenches in software to guarantee proper operation.

**Table 2.1:** Memory Transaction Ordering Rule

| First Transaction  | Second Transaction | Memory Ordered         |
| -------------------|--------------------|------------------------|
| Local access       | Local access       | Yes                    |
| Read from core "A  | Read from core "A" | Yes                    |
| Write to core "A"  | Write to core "A"  | Yes                    |
| Read from core "A" | Write to core "A"  | Yes                    |
| Read from core "A" | Read from core "B" | Yes                    |
| Read from core "A" | Write to core "B"  | Yes                    |
| Write to core "A"  | Read from core "A" | No                     |
| Write to core "A"  | Write to core "B"  | No                     |
| Write to core "A"  | Read from core "B" | No                     |


## Endianness

The Epiphany architecture is a little-endian memory architecture. 


## Load/Store Alignment Restrictions

The Epiphany architecture expects all memory accesses to be suitably aligned: doubleword accesses must be doubleword-aligned, word accesses must be word-aligned, and halfword accesses must be halfword-aligned. Table 2 summarizes the restrictions on the three LSBs of the address used to access memory for each type of memory transaction. An “x” in the address field refers to a bit that can be any value. Load and store transactions with unaligned addresses generate a software exception that is handled by the node's interrupt controller. For unaligned write accesses, data is still written to memory, but the data written will be incorrect. Unaligned reads return values to the register file before an unaligned exception occur.

**Table 2.2:** Load and Store Memory-Alignment Restrictions

| Data Type  | Address[2:0] |
|------------|--------------|
| Byte       | xxx          |
| Halfword   | xx0          |
| Word       | x00          |
| Doubleword | 000          |

## Program-Fetch Alignment Restrictions
All instructions must be aligned on half-word boundaries. 

----
Copyright 2008-2016 Adapteva Inc. All rights reserved.
(See LICENSE.md for details)
