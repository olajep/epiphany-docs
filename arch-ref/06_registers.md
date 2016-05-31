# Epiphany Registers

## Register Summary

This appendix contains detailed descriptions for all the registers within the Epiphany core architecture. Complete 32 bit addresses are constructed by combining the local 20 bit addresses shown in the tables with the MSB aligned 12 bit core ID.

Table 7.1: Register Summary

| Local Address     | Register Name |  Access | Comment                     |
|-------------------|---------------|---------|-----------------------------|
| **CORE**          |               |         |                             |
| 0xF0000 - 0xF00FC | R0-R63        | RD/WR   | General purpose registers   |
| 0xF0400           | CONFIG        | RD/WR   | Core configuration          |
| 0xF0404           | STATUS        | RD/WR   | Core status                 |
| 0xF0408           | PC            | RD/WR   | Program counter             |
| 0xF040C           | DEBUGSTATUS   | RD      | Debug status                |
| 0xF0414           | LC            | RD/WR   | Hardware loop counter       |
| 0xF0418           | LS            | RD/WR   | Hardware loop start address |
| 0xF041C           | LE            | RD/WR   | Hardware loop end address   |
| 0xF0420           | IRET          | RD/WR   | Interrupt PC return value   |
| 0xF0424           | IMASK         | RD/WR   | Interrupt mask              |
| 0xF0428           | ILAT          | RD/WR   | Interrupt latch             |
| 0xF042C           | ILATST        | WR      | ILAT "set" alias            |
| 0xF0430           | ILATCL        | WR      | ILAT "clear" alias          |
| 0xF0434           | IPEND         | RD/WR   | Interrupts in process       |
| 0xF0438           | CTIMER0       | RD/WR   | Core timer0                 |
| 0xF043C           | CTIMER1       | RD/WR   | Core timer1                 |
| 0xF0440           | FSTATUS       | WR      | Write to all status bits    |
| 0xF0448           | DEBUGCMD      | WR      | Debug command register      |
| **DMA**           |               |         |                             |
| 0xF0500           | DMA0CONFIG    | RD/WR   | DMA0 configuration          |
| 0xF0504           | DMA0STRIDE    | RD/WR   | DMA0 stride                 |
| 0xF0508           | DMA0COUNT     | RD/WR   | DMA0 count                  |
| 0xF050C           | DMA0SRCADDR   | RD/WR   | DMA0 source address         | 
| 0xF0510           | DMA0DSTADDR   | RD/WR   | DMA0 destination address    | 
| 0xF0514           | DMA0AUTO0     | RD/WR   | DMA0 slave lower data       |
| 0xF0518           | DMA0AUTO1     | RD      | DMA0 slave upper data       |
| 0xF051C           | DMA0STATUS    | RD/WR   | DMA0 status                 |
| 0xF0520           | DMA1CONFIG    | RD/WR   | DMA1 configuration          |
| 0xF0524           | DMA1STRIDE    | RD/WR   | DMA1 stride                 |
| 0xF0528           | DMA1COUNT     | RD/WR   | DMA1 count                  |
| 0xF052C           | DMA1SRCADDR   | RD/WR   | DMA1 source address         | 
| 0xF0530           | DMA1DSTADDR   | RD/WR   | DMA1 destination address    | 
| 0xF0534           | DMA1AUTO0     | RD/WR   | DMA1 slave lower data       |
| 0xF0538           | DMA1AUTO1     | RD      | DMA1 slave upper data       |
| 0xF053C           | DMA1STATUS    | RD/WR   | DMA1 status                 |
| **MEMORY**        |               |         |                             |
| 0xF0604           | MEMSTATUS     | RD/WR   | Memory status               |
| 0xF0608           | MEMPROTECT    | RD/WR   | Memory protection config    | 
| **MESH**          |               |         |                             |
| 0xF0700           | CMESHCONFIG   | WR      | CMesh node configuration    |
| 0xF0704           | COREID        | RD      | Processor node ID           |
| 0xF0708           | MULTICAST     | WR      | Multicast configuration     |
| 0xF0710           | RMESHCONFIG   | WR      | Rmesh node configuration    |
| 0xF0720           | XMESHCONFIG   | WR      | XMesh node configuration    |

## Alphabetical List of Registers

----

## COREID (0xF0704)

The row-column coordinate of the processor nodes is contained in the read-only COREID register, which is accessible by software using the “MOVFS RN, COREID” instruction. The COREID register facilitates writing code that is independent of processor nodes and that can be easily mapped to any node within the Epiphany architecture.

| Bits    | Name       | Description    |
|---------|------------|----------------|
| [5:0]   | COLUMN_ID  | Core column ID |  
| [11:6]  | ROW_ID     | Core row ID    |  
| [31:12] | RESERVED   | N/A            |  

----

## CONFIG (0xF0400) 

The CONFIG sets various eCore operating modes

| Bits    | Name       | Description                                |
|---------|------------|--------------------------------------------|
| [0]     | ROUNDMODE  | IEEE Floating-Point Truncate Rounding Mode |
|         |            | 0 = Round to nearest even rounding         |
|         |            | 1 = Truncate rounding                      |
| [1]     | IEN        | Invalid floating-point exception enable    |
|         |            | 0 = Exception turned off                   |
|         |            | 1 = Exception turned on                    |
| [2]     | OEN        | Overflow floating-point exception enable   |
|         |            | 0 = Exception turned off                   | 
|         |            | 1 = Exception turned on                    |
| [3]     | UEN        | Underflow floating-point exception enable  |
|         |            | 0 = Exception turned off                   |
|         |            | 1 = Exception turned on                    |
| [7:4]   | CTIMER0CFG | Controls the events counted by CTIMER0     |
|         |            | 0000 = off                                 |
|         |            | 0001 = clk                                 |
|         |            | 0010 = idle cycles                         |
|         |            | 0011 = reserved                            |
|         |            | 0100 = IALU valid instructions             | 
|         |            | 0101 = FPU valid instructions              |
|         |            | 0110 = dual issue clock cycles             |
|         |            | 0111 = load (E1) stalls                    |
|         |            | 1000 = register dependency (RA) stalls     |
|         |            | 1001 = reserved                            |
|         |            | 1010 = local memory fetch stalls           |
|         |            | 1011 = local memory load stalls            |
|         |            | 1100 = reserved                            |
|         |            | 1101 = external load stalls                |
|         |            | 1110 = mesh traffic monitor 0              |
|         |            | 1111 = mesh traffic monitor 1              |
| [11:8]  | CTIMER1CFG | Timer1 mode, same as for CTIMER0           |
|         |            | 0011 = selects the carry out from TIMER0   |
| [15:12] | CTRLMODE   | Controls eMesh routing modes (pass through)|
|         |            | 0000:Normal routing mode                   |
|         |            | 0100: Reserved                             |
|         |            | 1000: Reserved                             |
|         |            | 1100: Message mode routing (LABS)          |
|         |            | 0001: Force NORTH routing at destination   |
|         |            | 0101: Force EAST routing at destination    |
|         |            | 1001: Force SOUTH routing at destination   |
|         |            | 1101: Force WEST routing at destination    |
|         |            | xx10: Reserved                             |
|         |            | 0011: Multicast routing (LABS)             |
|         |            | 1011: Reserved                             |
|         |            | 0111: Reserved                             |
|         |            | 1111: Reserved                             |
| [16]    | RESERVED   | N/A                                        |
| [19:17] | ARITHMODE  | Selects 2nd datapath arithmetic mode       |
|         |            | 000 = 32 bit IEEE float-point mode         |
|         |            | 100 = 32 bit signed integer mode           |
|         |            | All other modes reserved.                  |
| [21:20] | RESERVED   | N/A                                        |
| [22]    | LPMODE     | 0=Clock gating disabled                    |
|         |            | 1=Clock gating enabled                     |
| [25-23] | RESERVED   | N/A                                        |
| [26]    | TIMERWRAP  | 0=Timer stops when it reaches 0x0          |
|         |            | 1=Timer wraps to 0xFFFFFFFF at 0x0         |
| [31:27] | RESERVED   | N/A                                        |

----

## CTIMER0 (0xF0438)

| Bits    | Name       | Description                                   |
|---------|------------|-----------------------------------------------|
| [31:0]  | CTIMER0    | 32-bit timer or lower 32-bits of 64-bit timer |

----

## CTIMER1 (0xF043C)

| Bits    | Name       | Description                                   |
|---------|------------|-----------------------------------------------|
| [31:0]  | CTIMER0    | 32-bit timer or upper 32-bits of 64-bit timer |

----

## DMAxAUTO0  (0xF0514/0xF0534)

The DMA AUTO registers are used in DMA slave mode. In slave mode, the rate of transactions is controlled by the rate of writes being to the AUTODMA register rather than by the DMA transaction master. In the case of double-word data transfers, DMAxAUTO0 receives the lower 32 bits and DMAxAUTO1 receives the upper 32 bits.

| Bits    | Name       | Description                                   |
|---------|------------|-----------------------------------------------|
| [31:0]  | DMAxAUTO0  | Lower 32-bits of the DMA slave mode register  |

----

## DMAxAUTO1 (0xF0518/0xF0538)

| Bits    | Name       | Description                                   |
|---------|------------|-----------------------------------------------|
| [31:0]  | DMAxAUTO1  | Upper 32-bits of the DMA slave mode register  |


----

## DMAxCONFIG (0xF0500/0xF0520)

The DMA configuration register is used to configure the type of DMA transfer. The following table shows the configuration options for each channel in the DMA engine.

| Bits   | Name       | Description                                     |
|--------|------------|-------------------------------------------------|
| [0]    | DMAEN      | Turns on DMA channel                            |
|        |            | 1=enabled                                       |
|        |            | 0=disabled                                      |
| [1]    | MASTER     | Sets up DMA channel to work in master made      |
|        |            | 1=master mode                                   |
|        |            | 0=slave mode                                    |
| [2]    | CHAINMODE  | Sets up DMA to fetch new descriptor at compltion|
|        |            | 1=Chain mode                                    |
|        |            | 0=One-shot mode                                 |
| [3]    | STARTUP    | Forces fetch of new descriptor                  |
|        |            | 1=Fetch descriptor based on bits [31:16]        |
|        |            | 0=Normal operation                              |
| [4]    | IRQEN      | Enables interrupt at the end of transfer        |
|        |            | 1=Interrupt enabled                             |
|        |            | 0=Interrupt disabled                            |
| [6:5]  | DATASIZE   | Size of data transfer.                          |
|        |            | 00=byte, 01=half-word, 10=word, 11=double-word  |
| [9:7]  | CTRLMODE   | RESERVED                                        |
| [10]   | MSGMODE    | Interrupt attach to last transfer               |
|        |            | Generates interrupt at receiver for remote write|
|        |            | DMA pause until last return for remote reads    |
|        |            | 1=enabled                                       |
|        |            | 0=disable                                       |
| [11]   | RESERVED   | N/A                                             |
| [12]   | SHIFT_SRCIN | Left shift inner loop source stride by 16 bits |  
| [13]   | SHIFT_DSTIN | Left shift inner loop destination stride 16 bits|
| [14]   | SHIFT_SRCIN | Left shift outer loop source stride by 16 bits  |  
| [15]   | SHIFT_DSTIN | Left shift outer loop destination stride 16 bits|
| [31:16]| NEXT_PTR  | Address of next DMA descriptor                  |

----

## DMAxCOUNT (0xF0508/0xF0528)

This register is used to set up the number of transactions in the inner and outer loops of the DMA transaction. The upper 16 bits specify the outer loop of the DMA transfer and the and lower 16 bits of the register specify the number of inner loops. The outer and inner loops must be set to a value of one or greater. The DMA block transfer is complete when the DMACOUNT register reaches zero. The inner count value is cleared to the initial count every time the outer loop is decremented.

| Bits  | Name       | Description                                     |
|-------|------------|-------------------------------------------------|
| [15:0]| INNER_COUNT| Transactions remaining within inner loop        |   
|[31:16]| OUTER_COUNT| Number of outer loop iterations remaining (“2D”)| 

----

## DMAxDSTADDR (0xF0510/0xF0530)

This register contains the 32-bit address of the transaction currently being transferred. The address can be a local address (bits [31:20] all zero) or a global address. The register gets loaded when the descriptor is fetched from memory and is updated at the completion of every transaction. The updated address is equal to the old destination address added with the value in the destination field in the stride register.

| Bits  | Name       | Description                                     |
|-------|------------|-------------------------------------------------|
|[31:0] | DSTADDR    | Current destination address                     |

----

## DMAxSRCADDR (0xF050C/0xF052C)

This register contains the 32-bit source address of the transaction currently being transferred. The address can be a local address (bits [31:20] all zero) or a global address. The register gets loaded when the descriptor is fetched from memory and is updated at the completion of every transaction. The updated address is equal to the old source address added with the value in the destination field in the stride register.

| Bits  | Name       | Description                                     |
|-------|------------|-------------------------------------------------|
|[31:0] | SRCADDR    | Current source address                          |


----

## DMAxSTATUS (0xF051C/0xF053C)

| Bits  | Name       | Description                                     |
|-------|------------|-------------------------------------------------|
| [3:0] | DMASTATE   | 0x0=DMA idle                                    |
|       |            | 0x5=DMA active                                  |
|       |            | 0x6=DMA in slave mode waiting for transaction   |
|       |            | 0xA=Waiting for last data item to return        | 
|       |            | 0xB=DMA in transfer pause state                 |
|       |            | 0xD=DMA configuration error.                    |
|[15:4] | RESERVED   | N/A                                             |
|[31:16]|CURR_PTR    | Address of current DMA descriptor               |

----

## DMAxSTRIDE (0xF0504/0xF0524)

The register contains two signed 16-bit values specifying the stride, in bytes, used to update the source and destination address register after a completed transaction. The lower 16 bits specify source address register update stride and the upper 16 bits specify the destination address stride. At the end of an inner-loop turn, this register is loaded with the outer-loop stride values to make address adjustments of the source and destination addresses before continuing with the next inner loop of data transfer. Before the next inner loop starts, the stride register is reloaded with the inner-loop stride values. The stride values are specified in bytes and should match the type of transfers being done. All DMA transactions must be aligned appropriately in memory.


| Bits  | Name       | Description                                     |
|-------|------------|-------------------------------------------------|
|[15:0] | SRC_STRIDE | Value to add to DMAxSRCADDR at each transaction |  
|[31:16]| DST_STRIDE | Value to add to DMAxDSTADDR at each transaction |  

----

## DEBUGCMD (0xF0448)

A write only alias register used to place control the debug state of the Epiphany core from an external agent.

| Bits  | Name       | Description                                     |
|-------|------------|-------------------------------------------------|
| [1:0] | COMMAND    | 00: Resume operation                            |
|       |            | 01: Halt processor                              |

----

## DEBUGSTATUS (0xF040C)

| Bits  | Name       | Description                                     |
|-------|------------|-------------------------------------------------|
| [0]   | HALT       | 0: Processor operating normally                 |
|       |            | 1: Processor in “halt” state                    |
| [1]   | EXT_PEND   | 0: No external load or fetch pending            |  
|       |            | 1: External load or fetch pending               | 
| [2]   | MBKPT_FLAG | 0: No multicore breakpoint active               |
|       |            | 1: Multicore breakpoint active (LABS)           |
|[31:3] | RESERVED   | N/A                                             |

----

## FSTATUS (0xF0440)

| Bits  | Name       | Description                                     |
|-------|------------|-------------------------------------------------|
| [31:0]| STATUS     | Write to all bits in STATUS register.           |

----

## ILAT (0xF0428)

The ILAT register records all interrupt events. All events are positive edge-triggered, meaning that there is no need to hold the interrupt bit high until the event has been completed. Each bit in the ILAT register (except for the user interrupt at bit 9) is tied to a specific hardware event. The ILAT register can be accessed directly or through the address aliases ILATST and ILATCL.

| Bits  | Name       | Description                                     |
|-------|------------|-------------------------------------------------|
| [9:0] | ILAT       | Latched interrupts waiting to enter CPU         |
|[31:10]| RESERVED   | N/A                                             |

----

## ILATST (0xF042C)

An alias for the ILAT register that allows bits within the ILAT register to be set individually. Writing a “1” to an individual bit of the ILATST register will set the corresponding ILAT bit to “1”. Writing a “0” to an individual bit will have no effect on the ILAT register. The ILATST alias cannot be read.


| Bits  | Name       | Description                                     |
|-------|------------|-------------------------------------------------|
| [9:0] | ILAT       | ILAT= ILAT 'OR'  bit set in this field          |
|[31:10]| RESERVED   | N/A                                             |


----

## ILATCL (0xF0430)

An alias for the ILAT register that allows bits within the ILAT register to be cleared individually. Writing a “1” to an individual bit of the ILATCL register will clear the corresponding ILAT bit to “0”. Writing a “0” to an individual bit will have no effect on the ILAT register. The ILATST alias cannot be read.

| Bits  | Name       | Description                                     |
|-------|------------|-------------------------------------------------|
| [9:0] | ILAT       | ILAT= ILAT 'ANDNOT' bit set in this field       |
|[31:10]| RESERVED   | N/A                                             |

## IMASK (0xF0424)

This is a masking register for blocking interrupts on a per-interrupt basis. All interrupts are latched by the ILAT register but can be blocked from reaching the program sequencer by setting the appropriate bit in the IMASK register. At each bit position, a “1” means the interrupt is masked.

| Bits  | Name       | Description                                     |
|-------|------------|-------------------------------------------------|
| [9:0] | IMASK      | Bif field mask for ILAT register                |
|[31:10]| RESERVED   | N/A                                             |

----

## IRET (0xF0420)

When an interrupt is serviced, the program counter of the upcoming sequential instruction is saved in the IRET register. The value in the IRET register is used by the RTI instruction to return to the original thread at a later time. For nested interrupt service routines, the IRET should be saved on the stack.

| Bits  | Name       | Description                                       |
|-------|------------|---------------------------------------------------|
| [31:0]| IRET       | Program counter (PC) at the time of the interrupt |

----

## IPEND (0xF0434)

This is a status register that keeps track of the interrupt service routines currently being processed. A bit is set when the interrupt enters the core and redirects the program flow and is cleared by the software executing an RTI instruction. The lowest numbered bit set to “1” indicates the currently serviced interrupt. Only interrupts in the ILAT register with a number less than the lowest bit in the IPEND register reach the program sequencer. This register can be used to implement nested interrupts. The register should never be directly written by a program.


| Bits  | Name       | Description                                       |
|-------|------------|---------------------------------------------------|
| [9:0] | IPEND      | Maintains record of all interrupts in process     |
|[31:10]| RESERVED   | N/A                                               |


----

## LC (0xF0414)

| Bits  | Name       | Description                                       |
|-------|------------|---------------------------------------------------|
| [31:0]| LC         | Current loop count, decremented when LE==PC       |

----

## LE (0xF041C)
| Bits  | Name       | Description                                       |
|-------|------------|---------------------------------------------------|
|[31:0] | LE         | Loop end address                                  |


----

## LS (0xF0418)

| Bits  | Name   | Description                                  |
|-------|--------|----------------------------------------------|
| [31:0]| LS     | Loop start address                           |


----

## MEMPROTECT (0xF0608)

| Bits | Name           | Description                                  |
|------|----------------|----------------------------------------------|
[ [0]  | PAGE0          | 1: Addr 0x0000-->0x0FFF set as read-only     |
| [1]  | PAGE1          | 1: Addr 0x1000-->0x1FFF set as read-only     |
[ [2]  | PAGE2          | 1: Addr 0x2000-->0x2FFF set as read-only     |
| [3]  | PAGE3          | 1: Addr 0x3000-->0x3FFF set as read-only     |
[ [4]  | PAGE4          | 1: Addr 0x4000-->0x4FFF set as read-only     |
| [5]  | PAGE5          | 1: Addr 0x5000-->0x5FFF set as read-only     |
[ [6]  | PAGE6          | 1: Addr 0x6000-->0x6FFF set as read-only     |
| [7]  | PAGE7          | 1: Addr 0x7000-->0x7FFF set as read-only     |
| [9:8]| RESERVED       | N/A                                          |
| [10] | DIS_EXT_RD     | 1: Disable reading core from emesh           |
| [11] | DIS_EXT_WR_MMR | 1: Disable writing to MMR from emesh         |  
| [12] | DIS_EXT_WR_MEM | 1: Disable writing to local memory emesh     |
| [13] | DIS_CORE_CWR   | 1: Disables store to cmesh from core         |
| [14] | DIS_CORE_XWR   | 1: Disables core’s ability to write off-chip |
| [15] | EXC_EN         | 1: Enables MEMPROTECT exception              |

----

## MEMSTATUS (0xF0604)

| Bits | Name           | Description                                  |
|------|----------------|----------------------------------------------|
| [1:0]| RESERVED       | N/A                                          |
| [2]  | MEM_FAULT      | Memory protection fault                      |  
|[9:3] | RESERVED       | N/A                                          |
| [10] | READ_BREACH    | Read from emesh attempted when DIS_EXT_RD==1 |  
| [11] | WRITE_BREACH   | Read from emesh when DIS_EXT_WR==1           |  
| [12] | CWRITE_BREACH  | Write to on-chip cores when DIS_CORE_CWR=1   |
| [13] | XWRITE_BREACH  | Write to on-chip cores when  DIS_CORE_XWR=1  |  

----

## MESHCONFIG (aka CMESHCONFIG) (0xF0700)

| Bits | Name           | Description                          |
|------|----------------|--------------------------------------|
| [0]  | RESERVED       | N/A                                  |
| [1]  | LPMODE         | 0=No clock gating mode               |
|      |                | 1=Aggressive clock gating            |
| [3:2]| RESERVED       | N/A                                  |
|[7:4] | MESHEVENT1     | Config for cmesh even to track       |
|      |                | 0000 = off                           |
|      |                | 0001 = reserved                      |
|      |                | 0010 = any wait                      |
|      |                | 0011 = core wait                     |
|      |                | 0100 = south wait                    |
|      |                | 0101 = north wait                    |
|      |                | 0110 = west wait                     |
|      |                | 0111 = east wait                     |
|      |                | 1000 = southeast wait                | 
|      |                | 1001 = northwest wait                |
|      |                | 1010 = south access                  |
|      |                | 1011 = north access                  |
|      |                | 1100 = west access                   |
|      |                | 1101 = east access                   |
|      |                | 1110 = core access                   | 
|      |                | 1111 = any access                    |
|[11:8]| MESHEVENT0     | Same config as MESHEVENT1            |
|[12]  | WESTEDGE       | Blocks wave propagating to west      |
|[13]  | EASTEDGE       | Blocks wave propagating to east      |
|[14]  | NORTHEDGE      | Blocks wave propagating to north     |
[15]   | SOUTHEDGE      | Blocks wave propagating to south     |

----

## MULTICAST (0xF0708)

| Bits | Name        | Description                                |
|------|-------------|--------------------------------------------|
|[11:0]|MULTICAST_ID | multicast ID to match                      |   

----

## PC (0xF0408)

| Bits | Name        | Description                                |
|------|-------------|--------------------------------------------|
|[31:0]| PC          | Contains next sequential PC to be executed |

----

##  STATUS (0xF0404)

The register reflects the state of the processor and should always be saved on entering an interrupt service routine. The STATUS register can be written using the MOVTS instruction or by directly writing to the register from an external host. The sticky flags can only be cleared through the MOVTS instruction or an externally generated write transaction. Status bits [2:0] are ready only bits controlled by the operational state of the CPU, but can be written forcibly through the FSTATUS alias.

| Bits  | Name        | Description                                |
|-------|-------------|--------------------------------------------|
[ [0]   | ACTIVE      | Core active indicator                      |
|       |             | 0=core idle, 1=core active                 |
| [1]   | GID         | Global interrupts disabled indicator       |
|       |             | 0 = all interrupts enabled                 |
|       |             | 1 = all interrupts disabled                |
| [2]   | RESERVED    | N/A                                        |
| [3]   | WAND        | WAND flag set by local core                | 
| [4]   | AZ          | Integer Zero Flag                          |
| [5]   | AN          | Integer Negative Flag                      |
| [6]   | AC          | Integer Carry Flag                         |
| [7]   | AV          | Integer Overflow Flag                      |
| [8]   | BZ          | Floating-Point Zero Flag                   |
| [9]   | BN          | Floating-Point Negative Flag               |
|[10]   | BV          | Alternate Overflow Flag                    |
|[11]   | RESERVED    |                                            |
|[12]   | AVS         | Sticky Integer Overflow                    |
|[13]   | BIS         | Sticky Floating-Point Invalid              |
|[14]   | BVS         | Sticky Floating-Point Overflow             |
|[15]   | BUS         | Sticky Floating-Point Underflow            |
|[19:16]| EXCAUSE     | Software exception cause                   |
|       |             | 1111=unimplemented                         |
|       |             | 1110=swi                                   |
|       |             | 1101=unaligned load/store                  |
|       |             | 1100=illegal access                        |
|       |             | 0111=fpu exception                         |
|       |             | 0000=no exception                          |
|[32:20]| RESERVED    | N/A                                        |

----
Copyright 2008-2016 Adapteva Inc. All rights reserved.
(See LICENSE.md for details)
