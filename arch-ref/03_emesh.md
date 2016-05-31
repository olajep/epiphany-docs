Network-On-Chip ("eMesh")
===========================================================================

**Figure 3.1:** eMesh Overview

## Network Topology

The eMesh Network-On-Chip (NOC) shown in Figure 3.1 employs a bidirectional 2D mesh topology with only nearest-neighbor direct connections. Every router in the mesh is connected to the north, east, west, south, and to a mesh node. Write transactions move through the network, with a latency of 1.5 clock cycles per routing hop. A transaction traversing from the left edge to right edge of a 64-core chip would thus take 12 clock cycles. The edges of the 2D array can be connected to non-Epiphany interface modules, such as SRAM modules, FIFOs, I/O link ports, or standard buses such as AXI. Alternatively, the mesh edge connections can be left unconnected if not needed in the system.

The eMesh consists of three separate and orthogonal mesh structures, each serving different types of transaction traffic:

* **cMesh:** Used for write transactions destined for an on-chip mesh node. The cMesh network connects a mesh node to all four of its neighbors and has a maximum bidirectional throughput of 8 bytes/cycle in each of the four routing directions.

* **rMesh:** Used for all read requests. The rMesh network connects a mesh node to all four of its neighbors.

* **xMesh:** Used for write transactions destined for off-chip resources and for passing through transactions destined for another chip in a multi-chip system configuration. The xMesh network allows an array of chips to be connected in a mesh structure without glue logic. The xMesh network is split into a south-north network and an east-west network. The maximum throughput of the mesh depends on the available-off chip I/O bandwidth. 

Figure 2 shows a connection diagram of the three mesh networks. The example shows an Epiphany chip configuration with 16 mesh nodes per chip. Each mesh node is connected to all three mesh networks. The only difference between larger-array chips and smaller-array chips is in the divisor placement between the on-chip and off-chip transaction routing model. The off-chip boundary is indicated by a dotted line in the figure. 

**Figure 3.2:** eMesh Network Topology


Key features of the eMesh NOC includes:

* Optimization of Write Transactions over Read Transactions. Writes are approximately 16x more efficient than reads for on-chip transactions. Programs should use the high write-transaction bandwidth and minimize inter-node, on-chip read transactions.

* Separation of On-Chip and Off-Chip Traffic. The separation of the xMesh and cMesh networks decouples off-chip and on-chip communication, making it possible to write on-chip applications that have deterministic execution times regardless of the types of applications running on neighboring nodes.

* Deadlock-Free Operation. The separation of read and write meshes—together with a fixed routing scheme of moving transactions first along rows, then along columns—guarantees that the network is free of deadlocks for all traffic conditions.

* Scalability. The implementation of the eMesh network allows it to scale to very large arrays. The only limitation is the size of the address space. For example, a 32-bit Epiphany architecture allows for building shared memory systems with 4,096 processors and a 64-bit architecture allows for scaling up to 18 billion processing elements in a shared memory system.

## Routing Protocol

The upper 12 bits of the destination address are used to route transactions to their destination. Along the way, these upper bits—6 bits for row and 6 bits for column—are compared to the row-column ID of each mesh node in the routing path. Transactions are routed east if the destination-address column tag is less than the column ID of the current router node, and they are routed west if the destination-address column tag is greater than the column ID of the current router node.

Transactions first complete routing along a single row before traveling in a column direction. When the destination-address column tag matches the mesh-node column ID, a similar comparison is made in the row direction to determine whether the transaction should be routed to the south or to the north. The transaction routing continues until both the row tag and column tag for the destination match the row and column ID of the current mesh node. Then, the transaction is routed into the network interface of mesh node.

Table 1 summarizes the routing rules for the combinations of mesh-node IDs and transaction addresses.

**Table 3.1:** Routing Protocol Summary

| Column Comparison     | Row Comparison       | Routing Decision
| ----------------------| --------------       | ----------------
| COL_ADDR > COL_NODE   | Don’t Care           | East
| COL_ADDR < COL_NODE   | Don't care           | West
| COL_ADDR == COL_NODE  | ROW_ADDR < ROW_NODE  | North
| COL_ADDR == COL_NODE  | ROW_ADDR > ROW_NODE  | South
| COL_ADDR == COL_NODE  | ROW_ADDR == ROW_NODE | Into mesh node


## Arbitration Scheme

The routers at every node in all three mesh networks contain round-robin arbiters. The arbitration hardware, in combination with the routing topologies, ensures that there are no deadlocks. The round-robin scheme also ensures that there is some split of available bandwidth between the competing agents on the network. The large on-chip bandwidth and non-blocking nature of the write network guarantees that no agent needs to wait more than a few clock cycles for access to the mesh. Applications requiring exact and deterministic bandwidth can implement network-resource interleaving in software.


## Buffering

The eMesh NOC uses a minimum amount (1 stage) of buffering and pipelining within the network to minimize power consumption and propagation latency.

## Data Transfers

The eMesh network supports byte, half-word, word, or double-word atomic transactions. Mesh data is always aligned to the least-significant bits (LSBs). Maximum bandwidth is obtained with double word transactions. All transactions should have addresses aligned according to the transaction data size.

## Multicast Routing (LABS)

The eMesh supports efficient broadcasting of data to multiple cores through a special “multicast” routing mode. To use the multicast routing method, set the CTRLMODE field in the CONFIG register to 0011 in the master core sending eMesh write transactions. In multicast mode, the normal eMesh routing algorithm described is overridden and the transaction is instead routed radially outwards from the transmitting node. The write destination address is compared to the value found in the MULTICAST register at each eMesh node. If the eMesh write transaction destination address matches the MULTICAST register, the transaction enters the node.

## Read Request Example

Read transactions are non-blocking and are initiated as posted read requests to an address within the mesh fabric. A read request is sent out on the rMesh network and propagates towards the mesh node or external resource using the routing algorithm described in the previous section.

The source address is sent along with the read transaction on the outgoing read request. After the data has been read from the read address, the data is returned to the source address on the cMesh or xMesh network. The process is completely non-blocking, allowing transparent interleaving of read transactions from all mesh nodes. 

**Figure 3.3:** Read Request Example

## Direct Inter-Core Communication

The code example shows that Epiphany is a true shared memory architecture. In the example, a dot-product routine writes its result to a memory location in another mesh node. The only thing required to pass data from one node to another is the setting of a pointer. The hardware decodes the transaction and determines whether it belongs to the local node’s memory or to another node’s memory. 

----
Copyright 2008-2016 Adapteva Inc. All rights reserved.
(See LICENSE.md for details)




