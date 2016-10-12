Epiphany Application Binary interface
============================================================================

This chapter is intended for library developers and users who develop custom
assembly routines that can be called from the Epiphany C-compiler. The Epiphany
ABI (EABI) is an agreement between developers that ensures interoperability
between different libraries. The EABI defines a common procedure call standard
and restrictions on data types and alignment.

Some of the details defined by the ABI include:

* How the program (caller) should set up the machine state before calling a procedure.
* How the called procedure (callee) should preserve program state across the call. The right of the called procedure to alter the program state of its caller.
* Memory regions reserved by the runtime.

Conformance to this standard requires that:
* At all times, stack limits and basic stack alignment are observed.
* The routines of publicly visible interface conform to the procedure call standard.
* The data elements of publicly visible interface conform to the data layout rules. Data elements include: parameters to routines named in interfaces, static data, and all data addressed by pointer values across interfaces.


## Native Data Types

Table 8.1 shows the fundamental data types supported by the Epiphany architecture. Memory can be considered as an array of bytes, with each byte separately addressable by the software. The memory layout accepted is little-endian data. The least significant bit of an object is always bit 0. Words are defined as 32 bits, half-words as 16-bits and double words as 64-bits.

**Table 8.1: Native Types**

| C-Type             |Machine Type         |Bytes| Restriction                 |
|--------------------|---------------------|-----|-----------------------------|
| char               |Signed byte          | 1   |                             |
| unsigned char      |Unsigned byte        | 1   |                             | 
| signed char        |Signed byte          | 1   |                             |
| signed short       |Signed half-word     | 2   | Must be half-word aligned   |
| unsigned short     |Unsigned half-word   | 2   | Must be half-word aligned   |
| signed int         |Signed word          | 4   | Must be word aligned        |
| unsigned int       |Unsigned word        | 4   | Must be word aligned        |
| signed long        |Signed word          | 4   | Must be word aligned        |
| unsignedlong       |Unsigned word        | 4   | Must be word aligned        |
| signed long   long |Signed double word   | 8   | Must be double-word aligned |
| unsigned long long |Unsigned double word | 8   | Must be double-word aligned |
| float              |32b IEEE754          | 4   | Must be word aligned        |
| double             |64b IEEE754          | 8   | Must be double-word aligned |

## Composite Types

In additional to the fundamental data types described previously, the Epiphany supports composite types, which are a collection of one or more fundamental data types that can be processed as a single entity during procedure calls. Each one of the composite types may contain composite types and/or fundamental data types as members.

**Aggregates**
An aggregate is a type with members that are laid out sequentially in memory. The alignment of the aggregate shall be the alignment of its most aligned component. The size of the aggregate shall be the smallest multiple of its alignment that is sufficient to hold all of its members when they are laid out according to these rules.

**Unions**
A union is a composite type, where each of the members has the same address. The alignment of a union shall be the alignment of its most-aligned component. The size of a union shall be the smallest multiple of its alignment that is sufficient to hold its largest member. Structures and unions are laid out according to the fundamental data types of which they are composed. All members are laid out in declaration order.

**Arrays**
An array is a repeated sequence of some other type (its base type). The alignment of an array shall be the alignment of its base type. The size of an array shall be the size of the base type multiplied by the number of elements in the array.


## Procedure Call Standard
The Epiphany architecture includes 64 general word length purpose register. Table 8.2 below shows the register usage convention in the EABI. The register usage convention acts as a contract to guarantee that a caller and callee function can work together with predictable results. The EABI has 10 callee saved registers and 52 caller saved registers.

The first four registers R0-R3 (A1-A4) are used to pass argument values into a subroutine and to return a result value from a function. They may also be used to hold intermediate values within a routine (but, in general, only between subroutine calls). Typically, the registers R4-R11, R14-R15, and R32-R43 are used to hold the values of a routine’s local variables. A subroutine must preserve the contents of the registers R4-R11 and R14-R15.

Fundamental types larger than 32 bits may be passed as parameters to, or returned as the result of, function calls. A double-word sized type is passed in two consecutive registers (e.g., R0 and R1, or R2 and R3).

The base standard provides for passing arguments in core registers (r0-r3) and on the stack. For subroutines that take a small number of parameters, only registers are used, greatly reducing procedure call overhead.

**Table 8.2: Register Use Convention**

| Name   | Alias| Role                               | Saved By |
|--------|------|------------------------------------|----------|
| R0     | A1   | Argument/result/scratch register #1| Caller   |
| R1     | A2   | Argument/result/scratch register #2| Caller   |
| R2     | A3   | Argument/result/scratch register #3| Caller   |
| R3     | A4   | Argument/result/scratch register #4| Caller   | 
| R4     | V1   | Register variable #1               | Callee   |
| R5     | V2   | Register variable #2               | Callee   |
| R6     | V3   | Register variable #3               | Callee   |
| R7     | V4   | Register variable #4               | Callee   |
| R8     | V5   | Register variable #5               | Callee   |
| R9     | V6   | Register variable #6               | Callee   |
| R10    | V7   | Register variable #7               | Callee   |
| R11    | V8   | Register variable #8               | Callee   |
| R12    | IP   | Intra-procedure call scratch reg   | Caller   |
| R13    | SP   | Stack pointer                      | n/a      |
| R14    | LR   | Link register                      | Callee   |
| R15    | FP   | Frame pointer                      | Callee   |
| R27:16 | FP   | Genral Use                         | Caller   |
| R31:28 | FP   | Reserved for constants             | n/a      |
| R63:32 | FP   | Reserved for constants             | Caller   |

## Stack Management

The stack is a contiguous area of memory that may be used for storage of local variables and for passing additional arguments to subroutines when there are insufficient argument registers available. The stack implementation is full-descending, with the current extent of the stack held in the register SP (R13). The stack will, in general, have both a base and a limit though in practice an application may not be able to determine the value of either.

The stack may have a fixed size or be dynamically extendable (by adjusting the stack-limit downwards).The rules for maintenance of the stack are divided into two parts: a set of constraints that must be observed at all times, and an additional constraint that must be observed at a public interface. At all times the following basic constraints must hold:

Stack-limit < SP <= stack-base. The stack pointer must lie within the extent of the stack.

(SP mod 4)=0. The stack pointer must at all times be aligned to a double word boundary (where SP is the value of register R13).

A process may only access (for reading or writing) the closed interval of the entire stack delimited by [SP, (stack-base – 1)].

## Subroutine Calls

The Epiphany includes ‘BL’ and ‘JALR’ instructions for calling subroutines. These instructions transfer the sequentially next value of the program counter—the return address —into the link register (LR) and the destination address into the program counter (PC). The result is to transfer control to the destination address, passing the return address in LR as an additional parameter to the called subroutine. Control is returned to the instruction following the BL/JALR when the return address is loaded back into the PC using the JR/RTS instruction.


## Subroutine Return Values

The manner in which a result is returned from a procedure is determined by the type of the result:

* A data type that is smaller than 4 bytes is zero or sign-extended to a word and returned in r0.
* A word-sized data type (e.g., int, float) is returned in r0.
* A double-word sized data type (e.g., long long, double) is returned in r0 and r1.
* A Composite Type not larger than 4 bytes is returned in r0. The format is as if the result had been stored in memory at a word-aligned address and then loaded into r0 with an LDR instruction. Any bits in r0 that lie outside the bounds of the result have unspecified values.
* A Composite Type larger than 4 bytes, or whose size cannot be determined statically by both caller and callee, is stored in memory at an address passed as an extra argument when the function was called. 

## Reserved memory

The `0x0 - 0x100` memory region is reserved by the Epiphany runtime.  

This includes:

* IVT entries (architecture)
* Workgroup configuration (e-lib)
* External memory configuration (e-lib)
* Loader configuration (runtime)


### Interrupt Vector Table (0x00 - 0x27)

Each IVT entry contains a branch instruction to a Interrupt Service Routine.
See section 4.10 in the Epiphany Architecure Reference Manual for a full
description of the Epiphany interrupt controller.


```c
struct ivt_table {
    uint32_t sync;                  // 0x00
    uint32_t swexp;                 // 0x04
    uint32_t memfault;              // 0x08
    uint32_t timer0;                // 0x0C
    uint32_t timer1;                // 0x10
    uint32_t message;               // 0x14
    uint32_t dma0;                  // 0x18
    uint32_t dma1;                  // 0x1C
    uint32_t wand;                  // 0x20
    uint32_t user;                  // 0x24
};
```

### Workgroup configuration (0x28-0x4F)

The `e_group_config` structure is used e-lib to determine the workgroup
configuration. It is populated by e-hal loader.

```c
typedef struct {
	e_objtype_t  objtype;           // 0x28
	e_chiptype_t chiptype;          // 0x2c
	e_coreid_t   group_id;          // 0x30
	unsigned     group_row;         // 0x34
	unsigned     group_col;         // 0x38
	unsigned     group_rows;        // 0x3c
	unsigned     group_cols;        // 0x40
	unsigned     core_row;          // 0x44
	unsigned     core_col;          // 0x48
	unsigned     alignment_padding; // 0x4C
} e_group_config_t;

```

### External memory configuration (0x50-0x57)

The `e_emem_config` structure is used e-lib to find the base of external
memory. It is populated by e-hal loader.

```c
typedef struct {
	e_objtype_t objtype;            // 0x50
	unsigned    base;               // 0x54
} e_emem_config_t;
```

### Loader configuration (0x58-0x67)

The loader configuration structure is used by the loader to instruct the
runtime (crt0) how it should start the program.

```c
struct loader_cfg {
    uint32_t flags;                 // 0x58
    uint32_t __pad1;                // 0x5C
    struct loader_args *args_ptr;   // 0x60
    uint32_t __pad2;                // 0x64
};
```

```c
/* Loader arguments */
struct loader_args {
    uint32_t r0;
    uint32_t r1;
    uint32_t r2;
    uint32_t r3;
    uint32_t function_ptr;
    uint32_t function_ptr_hi32;    // upper 32 bits
    uint32_t stack_spill_size;     // (8-byte aligned)
    uint32_t __pad;                // Reserved, must be 0
    uint32_t stack_spill_ptr;      // (8-byte aligned)
    uint32_t stack_spill_ptr_hi32; // upper 32 bits
} __attribute__((packed)) __attribute__((aligned(4)));
```

```c
// Loader flags for crt0
#define LOADER_BSS_CLEARED_FLAG 1
#define LOADER_CUSTOM_ARGS_FLAG 2
```

#### LOADER_BSS_CLEARED_FLAG

Instruct runtime **not** to clear the `.bss` section. Clearing the .bss section
at runtime can introduce race conditions for threaded programs.

#### LOADER_CUSTOM_ARGS_FLAG

Instead of calling the `main` function the runtime will set up the argument
registers and stack according to the `loader_args` structure pointed to by
`loader_cfg`, and call the function pointed to by `function_ptr`.
See the Procedure Call Standard section above for more details.


----
Copyright 2008-2016 Adapteva Inc. All rights reserved.
(See LICENSE.md for details)


