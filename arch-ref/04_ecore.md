# Chapter 5: Epiphany CPU

## Processor Node Overview

Figure 3.1 shows the components at each processor node, which include:
* The eCore RISC CPU
* Multi-banked local memory
* Multi-channel DMA engine
* Timers
* Network interface
* A Network-On-Chip router 

**Figure 3.1:** Processor Node Overview

**CPU**

The heart of each processor node is the eCore CPU, a floating-point RISC microprocessor designed from scratch for parallel processing and tuned to achieve a balance between performance, energy efficiency, and ease-of-use for real-time operation.

**Local Memory**

A local memory system supports simultaneous instruction fetching, data fetching, and inter-core communication. To accomplish this, the local memory is divided into four independent 8 byte wide memory banks.

On every clock cycle, the following operations can occur:

* 8 byes can be fetched from memory to the program sequencer.
* 8 bytes of data can be passed between the local memory and the CPU registers
* 8 bytes can be written into the local memory from the network interface.
* 8 bytes can be transferred from the local memory to the eMesh using the DMA.

In aggregate, the local memory supports 32 bytes of memory bandwidth per cycle.
To maximize bandwidth, use doubleword transactions and place data and instructions so that two masters never access the same memory bank simultaneously.

**Direct Memory Access (DMA) Engine**

The multi channel DMA engine off-loads the heavy task of of inter-core communication from the CPU. The engine is capable of double word transaction on every clock cycle and supports efficient 1D/2D DMA transfers as well as descriptors.

**Event Timers**

Each processor node has two 32-bit event timers to trace key events within core and on the mesh. The timers can be used for program debug, program optimization, load balancing, traffic balancing, timeout counting, watchdog timing, as well as for system time keeping.

**Network Interface**

The network interface connects the local memory system and core to the eMesh network-on-chip. The network interface decodes load/store instructions, and DMA-transaction addresses and decides whether a transaction is destined for the processor node itself (in which case bits [31:20] of the address are all zero) or for the mesh network. Arbitration is performed if more than one transaction is going out, in the same clock cycle, on one of the three network meshes. The network operates at the same frequency as the CPU and can output one 8 byte wide transaction on the network per clock cycle.

**Memory Protection Unit**

The memory protection unit provides protection against unwanted access of specific memory regions in the local memory.

**Mesh-Node Crossbar Switch**

The local memory in a processor node is split into 4 banks that are 8 bytes wide, each one with single cycle operation.

Four masters can access local memory simultaneously:

* **Instruction Fetch:** 8-byte instructions are fetched from local memory into the instruction alignment buffer of the program sequencer.

* **Load/Store:** The core copies data between the register file and a memory bank or remote memory. 

**DMA:** The DMA channels work completely independently from the CPU to move data in and out of the node. Valid data-transfer sizes are 8, 16, 32, or 64 bits.

**Mesh:** An external agent may access the local memory through the mesh. Whenever the node initiates a read from a remote memory location, the transaction returns as a write transaction.

## Memory Access Arbiter 
 
The crossbar switch within a processor node implements fixed priority arbitration. The arbiter is used whenever there is a potential for a shared-resource conflict. Table 3.1 illustrates the access priority for the different masters within the processor node for different shared resources.

**Table 3.1:** Resource Access Priorities

| Resource  |Priority #1|Priority #2|Priority #3|Priority #4   |Priority #5|
|-----------|-----------|-----------|-----------|-----------   |-----------|
| Mem0      | cMesh     | rMesh     | Load/Store| Program-Fetch| DMA       |
| Mem1      | cMesh     | rMesh     | Load/Store| Program-Fetch| DMA       |
| Mem2      | cMesh     | rMesh     | Load/Store| Program-Fetch| DMA       |
| Mem3      | cMesh     | rMesh     | Load/Store| Program-Fetch| DMA       |
| rMesh     | Load-Store| DMA       | --        | --           | --        | 
| cMesh     | rMesh     | Load-Store| DMA       | --           | --        |
| xMesh     | rMesh     | Load-Store| DMA       | --           | --        |
| R0-R63    | cMesh     | rMesh     | Load-Store| --           | --        |
| MMR's     | cMesh     | rMesh     | Load-Store| --           | --        |


* [Overview   ]
* [Data Types ]
* [Memory Map ]
* [General Purpose Registers ]
* [Status Flags ]
* [Instruction Set Architecture ]
* [Pipeline ]
* [Interrupt Controller ]
* [Hardware Loops ]
* [Timers ]
* [Debug Unit ]


## Overview

The different sub components of the Epiphany architecture are illustrated in Figure 1. The processor includes a general purpose program sequencer, large general purpose register file, integer ALU (IALU), floating point unit (FPU), interrupt controller, timers, and debug unit. 

**Figure 1:** eCore Processor Overview

**Program Sequencer**
The program sequencer supports all standard program flows for a general-purpose CPU, including:

* **Loops:** One sequence of instructions is executed several times. Loops are implemented using general-purpose branching instructions, in which case the branching can be done by label or by register.

* **Functions:** The processor temporarily interrupts the sequential flow to execute instructions from another part of the program. The CPU supports all C-function calls, including recursive functions.

* **Jumps:** Program flow is permanently transferred to another part of the program. A jump by register instruction allows program flow to be transferred to any memory location in the 32-bit address space that contains valid program code.

* **Interrupts:** Interrupt servicing is handled by the interrupt controller, which redirects the program sequencer to an interrupt handler at a fixed address associated with the specific interrupt event. Before entering the interrupt service routine, the old value of the program counter is stored so that it can be retrieved later when the interrupt service routine finishes.

* **Idle:** A special instruction that puts the CPU into a low-power state waiting for an interrupt event to return the CPU to normal execution. This idle mode is useful, for example, in signal-processing applications that are real-time and data-driven.

* **Linear:** In linear program flows, the program sequencer continuously fetches instructions from memory to ensure that the processor pipeline is fed with a stream of instructions without stalling.

**Register File**

The 9-port 64-word register file provides operands for the IALU and FPU and serves as a temporary power-efficient storage place instead of memory. Arithmetic instructions have direct access to the register file but not to memory. Movement of data between memory and the register file is done through load and store instructions. Having a large number of registers allows more temporary variables to be kept in local storage, thus reducing the number of memory read and write operations. The flat register file allows user to balance resources between floating-point and integer ALU instructions as any one of the 64 registers be used by the floating-point unit or IALU, without restrictions.
In every cycle, the register file can simultaneously perform the following operations:
* Three floating-point operands can be read and one result written by FPU.
* Two integer operands can be read and one result written by IALU.
* One operand can be written or read using a load/store instruction.

**Integer ALU**

The Integer ALU (IALU) performs a single 32-bit integer operation per clock cycle. The operations that can be performed are: data load/store, addition, subtraction, logical shift, arithmetic shift, and bitwise operations such as XOR and OR. The IALU’s single-cycle execution means the compiler or programmer can schedule integer code without worrying about data-dependency stalls. All IALU operations can be performed in parallel with floating-point operations as long as there are no register-use conflicts between the two instructions. Pre- and post-modify addressing and doubleword load/store capability enables efficient loading and storing of large data arrays.

**Floating-point Unit**

The floating-point unit (FPU) complies with the single precision floating point IEEE754 standard, executes one floating-point instruction per clock cycle, supports round-to-nearest even and round-to-zero rounding modes, and supports floating-point exception handling. The operations performed are: addition, subtraction, fused multiply-add, fused multiply-subtract, fixed-to-float conversion, absolute, float-to-fixed conversion.

Regular floating-point operations such as floating-point multiply/add reads two operands and produce a single result. A fused multiply-add instruction takes three input operands and produces a single accumulated result. A large number of floating-point signal-processing algorithms use the multiply-accumulate operations, and for these applications the fused operations has the potential of reducing the number clock cycles significantly.

**Interrupt Controller**

The interrupt controller has 10 interrupts and exception signals, with full support for nested interrupts and interrupt masking.

**Hardware Loop Block**

The hardware loop engine enables zero overhead looping.

**Debug Unit**

The debug unit provide multicore debug capabilities such as: single stepping, breakpoints, halt, and resume.


## Data Type Support 

The CPU architecture supports the following integer data types:

* **Byte:** 8 bits

* **Half-word:** 16 bits (must be aligned on 2 byte boundary in memory)

* **Word:** 32 bits (must be aligned on 4 byte boundary in memory)

* **Double:** 64 bits (must be aligned on 8 byte boundary in memory)

The data types can be of signed or unsigned format, as shown below. All register-register operations operate on word types only, but data can be stored in memory as any size.

**Signed Integer Representation (msb...lsb)**

-aN-1∙2^N-1, aN-2∙2^N-2 aN-3∙2^N-3 aN-4∙2^N-4 aN-5∙2^N-5 . . . . .	a0∙2^0

**Unsigned Integer Representation (msb..lsb)**

aN-1∙2^N-1, aN-2∙2^N-2, aN-3∙2^N-3, aN-4∙2^N-4, aN-5∙2^N-5 . . . . .	a0∙2^0

**Floating-point Data Types**

The FPU supports the IEEE754 32-bit single-precision floating-point data format, shown below:

| BIT[31] | BIT[30:23]  | BIT[22:0]   |
|---------|-------------|-------------|
| SIGN    | EXPONENT    | MANTISSA    |

A number in this floating-point format consists of a sign bit, s, a 24-bit mantissa, and an 8-bit unsigned-magnitude exponent, e. For normalized numbers, the mantissa consists of a 23-bit fraction, f, and a hidden bit of 1 that is implicitly presumed to precede f22 in the mantissa. The binary point is presumed to lie between this hidden bit and f22. The least-significant bit (LSB) of the fraction is f0; the LSB of the exponent is e0. The hidden bit effectively increases the precision of the floating-point mantissa to 24 bits from the 23 bits actually stored in the data format. This bit also ensures that the mantissa of any number in the IEEE normalized number format is always greater than or equal to 1 and less than 2. The exponent, e, can range between 1 ≤ e ≤ 254 for normal numbers in the single-precision format. This exponent is biased by +127 (254/2). To calculate the true unbiased exponent, 127 must be subtracted from e.

The IEEE standard also provides for several special data types in the single-precision floating-point format, including:

* An exponent value of 255 (all ones) with a nonzero fraction is a not-a-number (NAN). NANs are usually used as flags for data flow control, for the values of uninitialized variables, and for the results of invalid operations such as 0 ∗ ∞.

* Infinity is represented as an exponent of 255 and a zero fraction. Because the number is signed, both positive and negative infinity can be represented.

* Zero is represented by a zero exponent and a zero fraction. As with infinity, both positive zero and negative zero can be represented. The IEEE single-precision floating-point data types supported by the processor and their interpretations are summarized in Table 1.

**Table 1:** IEEE Single-Precision Floating-Point Data Types

| Type     | Sign | Exponent    | Mantissa | Value                         |
|----------|------|-------------|----------|-------------------------------|
| NAN      | X    | 255         | nonzero  | Undefined                     |
| Infinity | S    | 255         | 0        | (-1)^S * Infinity             |
| Normal   | S    | 1 <= e <=254| Any      | (-1)^S * (1.M22-0)            |
|          |      |             |          | $(2^*(e-127))$                |
| Denormal | S    | 0           | Any      | (-1)^S * Zero                 |
| Zero     | S    | 0           | 0        | (-1)^S * Zero                 |

The CPU is compatible with the IEEE-754 single-precision format, with the following exceptions:

* No support for inexact flags.

* NAN inputs generate an invalid exception and return a quiet NAN. When one or both of the inputs are NANs, the sign bit of the operation is set as an XOR of the signs of the input sign bits.

* Denormal operands are flushed to zero when input to a computation unit and do not generate an underflow exception. Any denormal or underflow result from an arithmetic operation is flushed to zero and an underflow exception is generated.

* Round-to-±infinity is not supported.

By default, the FPU performs round-to-nearest even IEEE754 floating-point rounding. In this rounding mode, the intermediate result is rounded to the nearest complete number that fits within the final 32-bit floating-point data format. If the result before rounding is exactly halfway between two numbers in the destination format (differing by an LSB), the rounded result is that number which has an LSB equal to zero. Statistically, rounding up occurs as often as rounding down, so there is no large sample bias.

The FPU supports truncation rounding when the rounding mode bit is set in the Core Configuration Register. In truncate rounding mode, the intermediate mantissa result bits that are not within the first 23 bits are ignored. Over a large number of accumulations, there can be a large sample bias in the computation, so truncation rounding mode should be avoided for most applications.

The FPU detects overflow, underflow, and invalid conditions during computations. If one of these conditions is detected, a software exception signal is sent to the interrupt controller to start an exception handling routine.

Double-precision floating-point arithmetic is emulated using software libraries and should be avoided if performance considerations outweigh the need for additional precision.

## Local Memory Map
Table 2 summarizes the memory map of the eCore CPU local memory.

**Table 2:** eCore Local Memory Map Summary

| Name                 | Start   | End     | Size(B) | Notes             |
|----------------------|---------|---------|---------|-------------------|
| IVT                  | 0x00000 | 0x0003F | 64      | Local memory      |
| Run Time Environment | 0x00040 | 0x000FF | 192     | Local memory      |
| Bank 0               | 0x00040 | 0x01FFF | 8KB-192 | Local Memory      |
| Bank 1               | 0x02000 | 0x03FFF | 8KB     | Local Memory      |
| Bank 2               | 0x04000 | 0x05FFF | 8KB     | Local Memory      |
| Bank 3               | 0x06000 | 0x07FFF | 8KB     | Local Memory      |
| Reserved             | 0x08000 | 0xEFFFF | n/a     | Reserved          |
| MMRs                 | 0xF0000 | 0xF07FF | 2048    | Restricted access |
| Reserved             | 0xF0800 | 0xFFFFF | n/a     | n/a               |

All registers are memory-mapped and can be accessed by external agents through a read or write of the memory address mapped to that register or through a program executing MOVTS/MOVFS instructions. A complete listing of all registers and their corresponding addresses can be found in Appendix B. The eCore complete local memory space is accessible by any master within an Epiphany system by adding 12-bit processor node ID offset to the local address locations. Reading directly from the general-purpose registers by an external agent is not supported while the CPU is active. Unmapped bits and reserved bits within defined memory-mapped registers should be written with zeros if not otherwise specified.

## General Purpose Registers

The CPU has a general-purpose register file containing 64 registers shown in Table 3. General-purpose registers have no restrictions on usage and can be used by all instructions in the Epiphany ISA. The only general purpose register written implicitly by an instruction is register R14, used to save a PC on a functional call. The general purpose registers do not have reset values.

**Table 3:** General-Purpose Registers

|Name     |Synonym| Procedure Call Role             | Saved By |
|---------|-------|---------------------------------|----------|
| R0      | A1    | Argument/result/scratch Reg#1   | Caller   |
| R1      | A2    | Argument/result/scratch Reg#2   | Caller   |
| R2      | A3    | Argument/result/scratch Reg#3   | Caller   |
| R3      | A4    | Argument/result/scratch Reg#4   | Caller   |
| R4      | V1    | Register variable #1            | Callee   |
| R5      | V2    | Register variable #2            | Callee   |
| R6      | V3    | Register variable #3            | Callee   |
| R7      | V4    | Register variable #4            | Callee   |
| R8      | V5    | Register variable #5            | Callee   |
| R9      | V6/SB | Register variable #6/Static base| Callee   |
| R10     | V7/SL | Register Variable #7/Stack limit| Callee   |
| R11     | V8/FP | Variable Register #8            | Callee   |
| R12     | --    | Intra-procedure scrach reg      | Caller   |
| R13     | SP    | Stack Pointer                   | N/A      |
| R14     | LR    | Link Register                   | Callee   |
| R15     | FP    | Frame Pointer                   | Callee   | 
| R16-R27 | --    | General use                     | Caller   |
| R28-R31 | --    | General use / compiler constants| Caller   |
| R32-R63 | --    | General use                     | Caller   |

The first four registers, R0-R3 (or A1-A4), are used to pass arguments into a subroutine and to return a result from a function. They can also be used to hold intermediate values within a function. The registers R4-R8, R10, R11 (or V1-V5, V7-V8) are used to hold the values of a routine's local variables. The following registers are set implicitly by certain instructions and architecture convention dictates that they have fixed use. For more information regarding register usage, please refer to the Epiphany SDK reference manual.

* **Stack Pointer:** Register R13 is a dedicated as a stack pointer (SP).

* **Link Register:** The link register, LR (or R14), is automatically written by the CPU when the BL or JALR instruction is used. The register is automatically read by the CPU when the RTS instruction is used. After the linked register has been saved onto the stack, the register can be used as a temporary storage register.

## Status Flags

The Core Status Register flags updated by the different instructions include:

* **[0] ACTIVE:** When set, it indicates that core is currently active. The core is inactive at reset and is activated by an external agent asserting an interrupt. Once activated, the core stays active until the user asserts the IDLE instruction, at which time the core enters a standby state. During the standby state, core clocks are disabled and the power consumption is minimized. Applications that need minimal power consumption should use the IDLE instruction to put the core in a standby state and use interrupts to activate the core when needed.

* **[1] GID:** When set it indicates that all external interrupts are blocked. The bit is set immediately on an interrupt occurring, giving the interrupt service routine enough time to save critical registers before another higher priority interrupt can occur. The flag is cleared by executing an RTI instruction, indicating the end of the service routine or by a GIE instruction indicating it is safe to allow a higher priority to begin if one is currently latched in the ILAT register.

* **[3] WAND:** A multicore flag set by the WAND instruction. The WAND flag is an output of the core that gets “ANDed” together with the WAND flags from other cores to produce a global WAND interrupt when cores have raised their respective flags.

* **[4] AZ:** The AZ (integer zero) flag set by an integer instruction when all bits of the result are zero and cleared for all other bit patterns. The flag is unaffected by all non-integer instructions.

* **[5] AN:** The AN (integer negative) flag set to on by an integer instruction when the most-significant bit (MSB) of the result is 1 and cleared when the MSB of the result is 0. The flag is unaffected by all non-integer instructions.

* **[6] AC:** The AC (integer carry) flag is the carry out of an ADD or SUB instruction, is cleared by all other integer instructions, and is unaffected by all non-integer instructions.

* **[7] AV:** The AV (integer overflow) flag set by the ADD instruction when the input signs are the same and the output sign is different from the input sign or by the SUB instruction when the second operand sign is different from the first operand and the resulting sign is different from the first operand. The flag is cleared by all other integer instructions and is unaffected by all non-integer instructions.

* **[8] BZ:** The BZ (floating-point zero) flag is set by a floating-point instruction when the result is zero. The flag unaffected by all non-floating-point instructions.

* **[9] BN:** The BN (floating-point negative) flag is set by a floating-point instruction when the sign bit (MSB) of the result is set to 1. The flag unaffected by all non-floating-point instructions.

* **[10] BV:** The BV (floating-point overflow) flag is set by a floating-point instruction when the post rounded result overflows (unbiased exponent>127), otherwise the BV flag is cleared. The flag unaffected by all non-floating-point instructions.

* **[11] BC:** Reserved

* **[12] AVS:** Sticky integer overflow flag set when the AV flag goes high, otherwise not cleared. The flag is only affected by the ADD and SUB instructions.

* **[13] BVS:** Sticky floating-point overflow flag set when the BV flag goes high, otherwise not cleared. The flag is unaffected by all non-floating-point instructions.

* **[14] BIS:** Sticky floating-point invalid flag set by a floating-point instruction if the either of the input operand is NAN, otherwise not cleared. The flag is unaffected by all non-floating-point instructions.

* **[15] BUS:** Sticky floating-point underflow flag set by a floating-point instruction if the result is denormal or one of the inputs to the operation is denormal, otherwise not cleared. The flag is unaffected by all non-floating-point instructions.

* **[19:16] EXCAUSE:** A four bit field indicating the cause of a software exception. A software exception edge interrupt is generated whenever this field is non-zero.

## Instruction Set

The Epiphany instruction-set architecture (ISA) is optimized for real-time signal processing application, but it has all the features needed to also perform well in standard control code. Instruction-set highlights include:

* Orthogonal instruction set, with no restrictions on register usage.

* Instruction set optimized for floating point computation and efficient data movement.

* Post-modify load/store instructions for efficient handling of large array structures.

* Rich set of branch conditions, with 3-cycle branch penalty on all taken branches and zero penalty on untaken branches.

* Conditional move instructions to reduce branch penalty for simple control-code structures.

* Instructions with immediate modifies for high code density and low power consumption.

* Compact and efficient floating-point instruction set.

The ISA uses a split-width instruction encoding method, which improves code density compared with standard 32-bit width encoding. All instructions are available as both 16- and 32-bit instructions, with the instruction width depending on the registers used in the operation. Any command that uses registers 0 through 7 only and does not have a large immediate constant is encoded as a 16-bit instruction. Commands that use higher-numbered registers are encoded as 32-bit instructions. This encoding is transparent to the user, but is carefully integrated with the compiler to minimize C-based code size and power consumption.

The following section summarizes the instructions available in the Epiphany ISA. A complete alphabetical listing of the ISA can be found in Appendix A.

**Branch Instructions**

Unrestricted branching is supported throughout the 32-bit memory map using branch instructions and register jump instructions. Branching can occur conditionally, based on the arithmetic flags set by the integer or floating-point execution unit. The following table illustrates the condition codes supported by the ISA. The architecture supports two sets of flags to allow independent conditional execution and branching of instructions based on results from two separate arithmetic units. The full set of branching conditions allows the synthesis of any high-level control comparison, including: <, <=, =, ==, !=, >=, and >. Both signed and unsigned arithmetic is supported.

**Table 4:** Condition Codes

| Code | Function                         | Mnemonic | Logic              |
|------|----------------------------------|----------|--------------------|
| 0000 | Equal                            | BEQ      | AZ                 |
| 0001 | Not Equal                        | BNE      | ~AZ                |
| 0010 | Greater Than (Unsigned)          | BGTU     | ~AZ & AC           |
| 0011 | Greater Than or Equal (Unsigned) | BGTEU    | AC                 |
| 0100 | Less Than or Equal (Unsigned)    | BLTEU    | AZ ⎮ ~AC           |
| 0101 | Less Than (Unsigned)             | BLTU     | ~AC                |
| 0110 | Greater Than (Signed)            | BGT      | ~AZ & (AV==AN)     |
| 0111 | Greater Than or Equal (Signed)   | BGTE     | AV == AN           |
| 1000 | Less Than (Signed)               | BLT      | AV !=AN            |
| 1001 | Less Than or Equal (Signed)      | BLTE     | AZ ⎮ (AV != AN)    |
| 1010 | Equal (Float)                    | BBEQ     | BZ                 |
| 1011 | Not Equal (Float)                | BBNE     | ~BZ                |
| 1100 | Less Than (Float)                | BBLT     | BN & ~BZ           |
| 1101 | Less Than or Equal (Float)       | BBLTE    | BN ⎮ BZ            |
| 1110 | Unconditional Branch             | B        | --                 |
| 1111 | Branch and Link                  | BL       | --                 |

**Load/Store Instructions**

Load and store instructions move data between the general-purpose register file and any legal memory location within the architecture, including external memory and any other eCore CPU in the system. All other instructions, such as floating-point and integer arithmetic instructions, are restricted to using registers as source and destination operands. 

The Epiphany ISA supports the following addressing modes:

* **Displacement Addressing:** The memory address is calculated by adding an immediate offset to a base register value. The immediate offset is limited to 3 bits for 16-bit load/store instructions or 11 bits for 32-bit load/store instructions. The base register value is not modified by the load/store operation. This mode is useful for accessing local variables.

* **Indexed Addressing:** The memory address is calculated by adding a register value offset to a base register value. The base register value is not modified by the load/store operation. This mode is useful in array addressing.

* **Post-Modify Addressing:** The memory address is taken directly from the base register value. After the memory operation has completed, a register offset is added to the base register value and written back to the base register. This mode is useful for processing large data arrays and for implementing an efficient stack-pop operation.
Byte, short, word, and double data types are supported by all load/store instruction formats. All loads and stores must be aligned with respect to the data size being used. Short (16-bit) data types must be aligned on 16-bit boundaries in memory, word (32-bit) data types must be aligned on 32-bit boundaries, and double (64-bit) data types must be aligned on 64-bit boundaries. Unaligned memory accesses returns unexpected data and generates a software exception. Double data-type load/store instructions must specify an even register in the general-purpose register file. The corresponding odd register is written implicitly. Attempts to use odd registers with double data format is flagged as an error by the assembler.

**Integer Instructions**

General-purpose integer instructions, such as ADD, SUB, ORR, AND, are useful for control code and integer math. These instructions work with immediate as well as register-based operands. The instructions update the integer status bits of the STATUS register.

**Floating-Point Instructions**

An orthogonal set of IEEE754-compliant floating-point instructions for signal processing applications. These instructions update the floating-point status bits of the STATUS register.

**Secondary Signed Integer Instructions**

The basic floating point instruction set can be substituted with a set of signed integer instructions by setting the appropriate mode bits in the CONFIG register [19:16]. These instructions use the same opcodes as the floating-point instructions and include: IADD, ISUB, IMUL, IMADD, IMSUB.

**Register Move Instructions**

All register moves are done as complete word (32-bit) entities. Conditional move instructions support the same set of condition codes as the branch instructions specified in Table 4.

**Program Flow Instructions**

A number of special instructions used by the run time environment to enable efficient interrupt handling, multicore programming, and program debug.

## Pipeline

The Epiphany CPU has a variable-length instruction pipeline that depends on the type of instruction being executed. All instructions share the same instruction pipeline until the E1 pipeline stage, and instructions are guaranteed to complete once they reach that stage. Load instructions complete at stage E2, and floating-point instructions complete at stage E4. All other instructions complete at E1.

Instructions are dispatched in-order but can finish out-of-order. The pipeline controller makes sure that the integrity of the program is maintained by stalling the pipeline appropriately if there is a read-after-write (RAW) or write-after-write (WAW) pipeline hazard.


**Table 7:** Pipeline Stage Description

| Stage | Name            | Mnemonic | Action                                         |
|-------|-----------------|----------|------------------------------------------------|
| 1     | Fetch Address   | FE       | Fetch address sent to instruction memory       |
| 2     | Fetch Access    | IM       | Instruction returns from core memory           |
| 3     | Decode          | DE       | Instructions are decoded                       |
| 4     | Register Access | RA       | Operands read from register file               |
| 5     | Execution       | E1       | Load/store address calculation                 |
|       |                 |          | Register read for store instructions           |
|       |                 |          | Most instructions completed                    |
|       |                 |          | Integer status flags written                   |
|       |                 |          | Branching and jumps change program flow        |
| 6     | Execution       | E2       | Load instruction data returned from memory     |
| 7     | Execution       | E3       | Floating-point result done for truncation mode |
| 8     | Execution       | E4       | Floating-point result done for rounding mode   |


**Figure 2:** Pipeline Graphical View

In the execution of instructions, the CPU implements an interlocked pipeline. When an instruction executes, the target register of the read operation is marked as busy until the write has been completed. If a subsequent instruction tries to access this register before the new value is present, the pipeline will stall until the previous instruction completes. This stall guarantees that instructions that require the use of data resulting from a previous instruction do not use the previous or invalid data in the register.

**Dual-Issue Scheduling Rules**

The CPU has a static dual-issue architecture that allows two instructions to be executed in parallel on every clock cycle, if certain parallel-issue rules are followed. The basic requirement for dual issue is that the instruction dispatch is done in-order. This means that for two instructions to be issued in parallel (on the same clock cycle), there can be no read-after-write (RAW) or write-after-write (WAW) register dependencies between the two instructions.

For the purpose of the following data-dependency tables, the instruction set can be divided into the following instruction groups:

**Table 8:** Instruction Issue Grouping

| GROUP      | INSTRUCTIONS                                               |
|------------|------------------------------------------------------------|
| IALU 	     |  ADD, SUB, ASR, LSR, LSL, EOR, AND, ORR, BITR, MOVT, MOV   |
| IALU2      |  IADD, ISUB, IMUL, IMADD, IMSUB                            |
| FPU        |	FADD, FSUB, FMUL, FMADD, FMSUB, FIX, FLOAT, FABS          |
| LOAD/STORE |  LDR, STR                                                  | 
| CONTROL    |	JR, JALR, B<COND>, BL, MOVTS, MOVFS, NOP                  |


The following table shows the combinations of instructions that can be executed in parallel as long as there are no read or write register conflicts. 

**Table 9:** Issue scheduling rules

| Instruction Type | IALU | FPU/IALU2 | LOAD/STORE | CONTROL |
|------------------|------|-----------|------------|---------|
| IALU             | --   | YES       | NO         | NO      |
| FPU/IALU2        | YES  | --        | YES        | NO      |
| LOAD/STORE       | NO   | YES       | --         | NO      |
| CONTROL          | NO   | NO        | NO         | NO      |

The CPU is pipelined to maximize the operating frequency, and energy efficiency and cost, of the total system. As a result of the processor pipeline, there are data-dependencies that need to be resolved by software to avoid clock cycle penalties due to processor stalls. The CPU has a fully interlocked pipeline, meaning that it automatically stalls the CPU to guarantee correct operation of sequential programs with data dependencies. The C compiler has accurate information about the CPU pipeline and is able to avoid most data dependencies in a program. However, for routines optimized in assembly, it is your responsibility to avoid data dependencies if you wish to optimize performance.

The following tables show the number of clock cycles needed to separate an instruction that is reserving a certain register and a second instruction that depends on that register. The third column in the tables gives the number of clock cycles of separation needed between the first instruction and the second instruction to avoid the stalling. It is the job of the C compiler or assembly programmer to ensure that these clock cycles can be filled with useful work. An Rz field in the table indicates that the register that does not affect any pipeline dependency table, and any register number can be used. Instruction combinations that don’t have any register dependency stalls such as IALU after IALU instructions are not included in the table.

The last dependency table shows instructions that have dependencies that are fixed and are independent of instruction-to-instruction register dependencies.

The branch prediction mechanism used by the CPU assumes that the branch was not taken. There is no penalty for branches not taken. For branches that are taken, there is a three-cycle constant penalty. The table below summarizes the different branches and the penalties.


**Table 10:** Pipeline Hazards

First Instruction | Second Instruction | Cycle Separation  |
------------------|--------------------|:-----------------:|
IALU              | FPU                | 1                 |
IALU              | IALU2              | 1                 |
FPU/IALU2         | FPU/IALU2          | 3 + ROUNDING      |
FPU/IALU2         | STORE              | 2 + ROUNDING      |
FPU/IALU2         | IALU               | 3 + ROUNDING      |
FPU/IALU2         | BRANCH<BBx>        | 3 + ROUNDING      |
FPU/IALU2         | MOV<BBx>           | 3 + ROUNDING      |
LDR               | IALU               | 1                 |
LDR               | FPU                | 2                 |
LDR               | STR                | 1                 |
LDR               | LDR                | 1                 |
LDRB or LDRH      | --                 | 2                 |
LDR EXTERNAL      | --                 | 10+               |
FETCH EXTERNAL    | --                 | 10+               |
BRANCH NOT TAKEN  | --                 | 0                 |
BRANCH TAKEN      | --                 | 3                 |
B (UNCONDITIONAL) | --                 | 3                 |
JL                | --                 | 3                 |
JR                | --                 | 3                 |
JALR              | --                 | 3                 |


In a special case, a 1-cycle penalty occurs for jumps to 32-bit instructions that straddle two lines in the local 64-bit-wide memory. Branches with dependency on floating-point status flags also incur stall cycles if there is insufficient cycle separation between the floating-point instruction and the branching instruction.


## Interrupt Controller

**Overview**

The interrupt controller provides full support for prioritized nested interrupt service routines. Figure 3 shows the behavior of the hardware mechanisms within the interrupt controller and how the user can control the behavior of the system through code. 

**Figure 3:** Interrupt Service Routine Operation


The following table summarizes the different interrupts and exceptions specified in the interrupt vector table (IVT). All interrupts are edge-based.

**Table 11:** Interrupt Support Summary

| Interrupt/Exception | Priority     | IVT   | Event                         |
|---------------------|--------------|-------|-------------------------------|
| Sync/reset          | 0 (highest)  | 0x00  | Sync hardware signal asserted |
| Software Exception  | 1            | 0x04  | EXCAUSE software exception    |
| Memory Fault        | 2            | 0x08  | Memory protection fault       |
| Timer0 Interrupt    | 3            | 0x0C  | Timer0 expiration             |
| Timer1 Interrupt    | 4            | 0x10  | Timer1 expiration             |
| Message             | 5            | 0x14  | Message interrupt             |
| DMA0 Interrupt      | 6            | 0x18  | DMA0 completion signal        |
| DMA1 Interrupt      | 7            | 0x1C  | DMA1 completion signal        |
| WAND Interrupt      | 8            | 0x20  | Wired-AND signa               |
| User Interrupt      | 9 (lowest)   | 0x24  | User software interrupt       |

The IVT entries should be populated with 32-bit relative branch instructions that point to the appropriate interrupt handlers. The Interrupt Controller uses the following registers to manage interrupts and exceptions, providing full support for nested interrupts. The priority level of the interrupt matches the actual bit position within these registers. For example, the sync interrupt with priority is mapped to bit 0 and the DMA0 interrupt is mapped to bit 6 in the ILAT register. The IRET register is a 32 bit register while the ILAT, IMASK, and IPEND are 10 bit registers.


**Global Interrupt Disable Flag**
Bit [1] of the STATUS Register is used to globally enable or disable all interrupts from affecting the core program flow. Interrupts are disabled when an interrupt starts and when the program executes a GID instruction. Interrupts are enabled again by executing an RTI or GIE instruction. The global interrupt enable bit ensures that save-and-restore functionality and context switches can be done safely, regardless of higher priority interrupts interfering with operation. All interrupts can be enabled and disabled from software using the GIE and GID instructions. The GID instruction sets the global interrupt disable bit in the STATUS register, and GIE clears the same bit. Alternatively, individual interrupts can be disabled by writing to the IMASK register.

**User Interrupts**
Individual interrupts within the ILAT register can be set and cleared from software by writing to the ILATST and ILATCL registers using the MOVTS instruction. Software interrupts can be used to degrade the interrupt priority, thereby allowing lower-priority interrupts to get handled more quickly. For example, an interrupt handler for the core timer could write to the ILATST register to force a soft interrupt with the lowest priority level, thus allowing interrupts such as the DMA interrupt, to be serviced. If the interrupt handler would stay within the timer priority handler throughout the processing, then the DMA interrupt would not get serviced until the timer interrupt had finished all the processing.
The recommended method of handling interrupt events in software is to use the interrupt registration process provided through the Epiphany run-time library. In this way, all interrupt handling can be done using standard C programming. For more details, please refer to the Epiphany SDK reference manual.


**Interrupt Latency**

To minimize interrupt latency, two requirements should be met:

* The stack should be placed in local memory. The stack is used to save and restore variables. If the stack is placed in external memory, loading of these variables could take hundreds of clock cycles.
* Loads from other cores or external memory should be minimized. Interrupts are disabled during such loads. Figure 4 illustrates the effect of external reads on interrupt service latency.

**Figure 4:** Interrupt Latency Dependency On External Read


## Hardware Loops (LABS)**

The Epiphany core supports zero overhead loops with the LC (loop counter), LS (loop start address), and LE (loop end address) registers. The three hardware loop registers must be correctly programmed before executing the critical code section.  When the program counter (PC) matches the value in LE and the LC is greater than zero, the PC gets set to the address in LS. The LC register decrements automatically every time the program scheduler completes one iteration of the code loop defined by LS and LE.

The Epiphany hardware loop does place certain restrictions on the program:
* All interrupts must be disabled while inside a hardware loop.
* The start of the loop must be aligned on a double word boundary.
* The next-to-last instruction must be aligned on a double word boundary.
* All instructions in the loop set as 32 bit instructions using  “.l” assembly suffix 
* The minimum loop length is 8 instructions.

Example:
```
	mov  r1, %low(loop_start);
	movt r1, %high(loop_start);
	movts ls, r1;			//setting loop start address
	mov  r1, %low(loop_end);
	movt r1, %high(loop_end);
	movts le, r1;			//setting loop end address
	mov  r1, #0x10;
	movts lc, r1;			//setting loop count
	gid;					//disabling interrupts		
.balignw 8,0x01a2;			//align to 8-byte boundary	
loop_start:
	add.l r1, r1, r0;		//first instruction in loop
	add.l r2, r2, r0;		//”.l” forces 32 bit instruction
	add.l r3, r3, r0;
	add.l r4, r4, r0;
	add.l r5, r5, r0;
	add.l r6, r6, r0;
.balignw 8,0x01a2;			//align to 8-byte boundary
	add.l r7, r7, r0;
loop_end:
	add.l r8, r8, r0; 		//last instruction
	gie;					//enabling interrupts
```

## Debug Unit


**Overview:**

The debug unit in the Epiphany core supports the following debugging features:

* Halting and resuming operations by writing to the DEBUGCMD register from the host processor or from another Epiphany core.

* Monitoring the status of the by reading from the DEBUGSTATUS register.

* Halting the locally running program by including a BKPT instruction in the code being executed.
     
* Halting the locally running program and all other cores in the workgroup by including a MBKPT instruction in the code being executed.

* Single stepping code through a software implementation (see Epiphany GDB imple
mentation)

**Registers:**




## Event Timers 

**Overview**

The Epiphany core contains two event timers that can be used to sample real-time events within the system. The type of event to monitor is controlled by bits [11:4] in the CONFIG register.

* **Clk:** General-purpose clock-cycle counter. Can be used to measure time, to profile function execution time, for real-time operating systems, and for many other purposes.

* **Idle:** Counts the number of clock cycles spent in idle. Can be used to balance the load on different CPUs.

* **IALU valid:** Counts the number of IALU instructions issued.

* **FPU valid:** Counts the number of FPU instructions issued.

* **Dual issue:** Counts the number of cycles with two instructions issued simultaneously. 

* **E1 stalls:** Counts the number of pipeline stalls due to load/store register dependencies. 

* **RA stalls:** Counts all register dependency pipeline stalls.

* **Fetch stalls:** Counts the number of stall cycles due to memory-bank contention in the processor node. Can be used to uncover issues with program code placement.

* **Ext data stalls:** Counts the number of clock-cycle stalls due to a load instruction accessing external memory and stalling the pipeline. Can be used to uncover areas of the code that are accessing variables from external memory instead of local memory.

* **Mesh traffic:** Counts wait or access events on the local cMesh network node. Mesh event monitor event selection is programmed through the MESHCONFIG registers 

The CTIMER0 register contains the current value of the event being monitored. The register counts down from a high value to zero, decrementing every time the chosen event is detected. When the timer reaches zero, the counter stops counting and an interrupt is issued to the interrupt controller. The event count mode is set in the CONFIG register.

The CTIMER1 register contains the current value of the event being monitored. The register counts down from a high value to zero, decrementing every time the chosen event is detected. When the timer reaches zero, the counter stops counting and an interrupt is issued to the interrupt controller. The event count mode is set in the CONFIG register.

**Timer Registers**

NAME          | DESCRIPTION           |
--------------|-----------------------|
CONFIG[7:4]   | CTIMER0 configuration |
CONFIG[11:8]  | CTIMER1 configuration |
CTIMER0       | CTIMER0 value         |
CTIMER1       | CTIMER1 value         |

**Interrupts**

Each timer generates an interrupt when the value reaches "0". CTIMER0/CTIMER1 are related to interrupts 3/4.

**Examples**



# Chapter 6: Direct Memory Access ("EDMA")

## Overview

Each Epiphany processor node contains a DMA engine to facilitate data movement across the eMesh network. The DMA engine has two general-purpose channels, with separate configuration for source and destination.

The main features of the DMA engine are:
* Two independent DMA channels per processor node.
* Separate specification of source/destination address configuration per descriptor and channel.
* 2D DMA operation.
* Flexible stride sizes
* DMA descriptor chaining.
* Hardware interrupts flagging to local CPU subsystem.

The following table shows the kind of transfers supported by the processor node’s DMA engine.

**Table 1:** DMA Transfer Types

| Source        | Destination   | Description                                |
| --------------|---------------|--------------------------------------------|
| Local Memory  | Remote Memory | 1. Read from local memory.                 |
|               |               | 2. Write to remote memory through eMesh    |
| Remote Memory | Local Memory  | 1. Send read request NOC.                  |
|               |               | 2. Return data written to local memory     |
| Remote Memory | Remote Memory | 1. Send read request NOC.                  |
|               |               | 2. Return data written to remote memory    |

The DMA engine has two complete data transfer channels and supports data movement as a master as well as a slave device. In a slave configuration, the pace of the data transfers is controlled by an external master. In a master configuration, the DMA pushes a transaction every clock cycle if the necessary memory and interface resources are available. In the MASTER mode, the DMA generates a complete transfer transaction with a source and a destination address. In the SLAVE mode, the source address of a DMA configuration is ignored. The data is always taken from the DMAxAUTO register and transferred to the destination address. The pace of the transaction is driven by another master in the system, which could be an I/O device, a programmable core, or another DMA channel.

## Descriptors

The format of DMA descriptors is given in the table below. All descriptors should be placed in local memory and must be double-word aligned. A descriptor is brought into the DMA channel configuration register set when the startup bit is set to one or when a DMA transfer is configured in chaining mode and a new configuration should automatically be brought in at the end of a transfer.

**Table 2:** DMA Descriptors

B7 B6            | B5 B4            | B3 B2           | B1 B0            |
-----------------| -----------------|-----------------|------------------|
STRIDE-INNER-DST | STRIDE-INNER-SRC | NXT_PTR         | DMACONFIG        |
STRIDE-OUTER-DST | STRIDE-OUTER-SRC | CNT-OUTER       | CNT-INNER        |
DST ADDRESS (HI) | DST ADDRESS (LO) | SRC ADDRESS (HI)| SRC ADDRESS (LO) |

## Channel Arbitration

The two DMA channels have a fixed priority, with channel0 having a higher priority over channel1.

## Usage Restrictions
The DMA does not flag errors on incorrect usage such as: misaligned accesses or illegal memory location access. Such scenarios will cause unexpected behavior in the system and will likely result in a core or chip needing to be reset.

## Transfer Examples
The following example shows how to use the DMA to do a single-block transfer. To make transfers efficient, double-word transfers should be used, but in this case we are using byte transfers.

```
MOV  R1, 0x8         ; set the startup bit
MOVT R1, _1D_DESCR   ; put descriptor pointer in the upper 16 bits
MOVTS DMA0CONFIG, R1 ; start a DMA transfer by writing to the
                     ; DMA config register.
_1D_DESCR;
.word 0x00000003; configure in master mode and enable
.word 0x00010001; increment src/dst address by 1 byte each transaction
.word 0x00010008; transfer has 8 transactions in a single inner loop
.word 0x00000000; outer loop stride not used in this example
.word 0x00002000; set source address to 0x2000, a local address
.word 0x92000000; set destination address to an external address

```


----
Copyright 2008-2016 Adapteva Inc. All rights reserved.
(See LICENSE.md for details)



