ESDK Macros
==============================================================================

The following can be used to access the absolute memory location of hardware registers.

## General Purpose Registers
```c
typedef enum
{
    E_REG_R0,    E_REG_R8,    E_REG_R16,    E_REG_R24,
    E_REG_R1,    E_REG_R9,    E_REG_R17,    E_REG_R25,
    E_REG_R2,    E_REG_R10,   E_REG_R18,    E_REG_R26,
    E_REG_R3,    E_REG_R11,   E_REG_R19,    E_REG_R27,
    E_REG_R4,    E_REG_R12,   E_REG_R20,    E_REG_R28,
    E_REG_R5,    E_REG_R13,   E_REG_R21,    E_REG_R29,
    E_REG_R6,    E_REG_R14,   E_REG_R22,    E_REG_R30,
    E_REG_R7,    E_REG_R15,   E_REG_R23,    E_REG_R31,
    E_REG_R32,   E_REG_R40,   E_REG_R48,    E_REG_R56,
    E_REG_R33,   E_REG_R41,   E_REG_R49,    E_REG_R57,
    E_REG_R34,   E_REG_R42,   E_REG_R50,    E_REG_R58,
    E_REG_R35,   E_REG_R43,   E_REG_R51,    E_REG_R59,
    E_REG_R36,   E_REG_R44,   E_REG_R52,    E_REG_R60,
    E_REG_R37,   E_REG_R45,   E_REG_R53,    E_REG_R61,
    E_REG_R38,   E_REG_R46,   E_REG_R54,    E_REG_R62,
    E_REG_R39,   E_REG_R47,   E_REG_R55,    E_REG_R63,
} e_gp_reg_id_t;
```

## Special Hardware Registers
```c
typedef enum
{
	E_REG_CONFIG,
	E_REG_IRET,
	E_REG_STATUS,
	E_REG_IMASK,
	E_REG_FSTATUS,
	E_REG_ILAT,
	E_REG_PC,
	E_REG_ILATST,
	E_REG_DEBUGSTATUS,
	E_REG_ILATCL,
	E_REG_DEBUGCMD,
	E_REG_IPEND,
	E_REG_LC,
	E_REG_LS,
	E_REG_LE,
	E_REG_DMA0CONFIG,
	E_REG_DMA1CONFIG,
	E_REG_DMA0STRIDE,
	E_REG_DMA1STRIDE,
	E_REG_DMA0COUNT,
	E_REG_DMA1COUNT,
	E_REG_DMA0SRCADDR,
	E_REG_DMA1SRCADDR,
	E_REG_DMA0DSTADDR,
	E_REG_DMA1DSTADDR,
	E_REG_DMA0AUTODMA0,
	E_REG_DMA1AUTODMA0,
	E_REG_DMA0AUTODMA1,
	E_REG_DMA1AUTODMA1,
	E_REG_DMA0STATUS,
	E_REG_DMA1STATUS,
	E_REG_CTIMER0,
	E_REG_CTIMER1,
	E_REG_MEMPROTECT,
	E_REG_MESH_CONFIG,
	E_REG_COREID,
	E_REG_CORE_RESET,
} e_core_reg_id_t;
```
