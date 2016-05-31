Epiphany Instruction Set Architecture
==============================================================================

**Table 5.1:** Instruction Set Assembly Syntax

| Field  | Description                                              |
|--------|----------------------------------------------------------|
| COND   | One of 16 condition codes                                |
| RD     | Destination register. (R0-R63)                           |
| RN     | Primary source register.(R0-R63)                         |
| RM     | Secondary source register.(R0-R63)                       |
| OP2    | Secondary operand. <simm3 or simm11 or RM>               |
| DM     | Datamode. Options are B,H,L,D                            |
| OFFSET | Load/store address offset: <imm3 or imm11>               |
| IMM    | Unsigned immediate per instruction                       |
| IMM3   | Unsigned immediate value in range 0 to 7                 |
| IMM8   | Unsigned immediate value in range 0 to 255               |
| IMM11  | Unsigned immediate value in range 0 to 2047              |
| IMM16  | Unsigned immediate value in range 0 to 65,535            |
| SIMM   | Signed immediate per instruction                         |
| SIMM3  | Signed immediate value in range -4 to +3                 |
| SIMM8  | Signed immediate value in range -128 to +127             |
| SIMM11 | Signed immediate value in range -1024 to +1023           |
| SIMM24 | Signed immediate value in range -8,388,608 to +8,388,607 |
| LABEL  | Jump/Branch label resolved by assembler                  |
| INSTR.l| The “.l” suffix is used to indicate a 32 bit instruction |

**Table 5.2:** Instruction Set Summary

| Instruction                  | Assembler                              | Flags                |
|------------------------------|----------------------------------------|----------------------|
| **BRANCHING**                |                                        |                      |
| Conditional branch           | ```BCOND <SIMM8 or SIMM11>```          | None                 |
| Jump                         | ```B <SIMM8 or SIMM11>```              | None                 |
| Jump-and-link                | ```BL <SIMM8 or SIMM11>```             | None                 |
| Register jump                | ```JR RN```                            | None                 |
| Register jump-and-link       | ```JALR RN```                          | None                 |
| **LOAD/STORE**               |                                        |                      |
| Load/store, base offset      | ```{LDR⎮STR}{DM} RD,[RN,#+/-OFFSET]``` | None                 |
| Load/store, register offset  | ```{LDR⎮STR}{DM} RD,[RN,+/-RM]```      | None                 |
| Load/store, postmodify       | ```{LDR⎮STR}{DM} RD,[RN],#+/-OFFSET``` | None                 |
| Load/store, postmodify-reg   | ```{LDR⎮STR}{DM} RD,[RN],+/-RM```      | None                 |
| Test & Set                   | ```TESTSET RD, [RN,RM]```              | None                 |
| **INTEGER**                  |                                        |                      |
| Integer Add                  | ```ADD RD, RN, <OP2>```                | AN,AZ,AV,AC,AVS      |
| Integer Subtraction          | ```SUB RD, RN, <OP2>```                | AN,AZ,AV,AC,AVS      |
| Arithmetic Shift Right       | ```ASR RD, RN, <OP2>```                | AN,AZ,AV,AC,AVS      |
| Logical Shift Right          | ```LSR RD, RN, <OP2>```                | AN,AZ,AV,AC,AVS      |
| Logical Shift Left           | ```LSL RD, RN, <OP2>```                | AN,AZ,AV,AC,AVS      |
| Logical 'Or'                 | ```ORR RD, RN, RM```                   | AN,AZ,AV,AC,AVS      |
| Logical 'And'                | ```AND RD, RN, RM```                   | AN,AZ,AV,AC,AVS      |
| Logical Xor                  | ```EOR RD, RN, RM```                   | AN,AZ,AV,AC,AVS      |
| Bit Reverse                  | ```BITR RD, RN```                      | AN,AZ,AV,AC,AVS      |
| **FLOATING POINT**           |                                        |                      |
| Floating-Point Addition      | ```FADD RD, RN, RM```                  | BN,BZ,BV,BIS,BVS,BUS |
| Floating-Point Subtraction   | ```FSUB RD, RN, RM```                  | BN,BZ,BV,BIS,BVS,BUS |
| Floating-Point Multiply      | ```FMUL RD, RN, RM```                  | BN,BZ,BV,BIS,BVS,BUS |
| Floating-Point Multiply-Add  | ```FMADD RD, RN, RM```                 | BN,BZ,BV,BIS,BVS,BUS |
| Floating-Point Multiply-Sub  | ```FMSUB RD, RN, RM```                 | BN,BZ,BV,BIS,BVS,BUS |
| Floating-Point Absolute      | ```FABS RD, RN```                      | BN,BZ,BV,BIS,BVS,BUS |
| Float to Int Conversion      | ```FIX RD, RN```                       | BN,BZ,BV,BIS,BVS,BUS |
| Int to Float Conversion      | ```FLOAT RD,RN```                      | BN,BZ,BV,BIS,BVS,BUS |
| **SECONDARY INTEGER**        |                                        |                      |
| 2nd Integer Addition         | ```IADD RD, RN, RM```                  | BN,BZ,BV,BIS,BVS,BUS |
| 2nd Integer Subtraction      | ```ISUB RD, RN, RM```                  | BN,BZ,BV,BIS,BVS,BUS |
| 2nd Integer Multiply         | ```IMUL RD, RN, RM```                  | BN,BZ,BV,BIS,BVS,BUS |
| 2nd Integer Multiply-Add     | ```IMADD RD, RN, RM```                 | BN,BZ,BV,BIS,BVS,BUS |
| 2nd Integer Multiply-Sub     | ```IMSUB RD, RN, RM```                 | BN,BZ,BV,BIS,BVS,BUS |
| **REGISTER MOVES**           |                                        |                      |
| Move immediate               | ```MOV RD, <IMM8 or IMM16>```          | None                 |
| Move immediate (high)        | ```MOVT RD, <IMM16>```                 | None                 |
| Move Register                | ```MOVCOND RD, RN```                   | None                 |
| Move TO special register     | ```MOVTS RD, RN```                     | None                 |
| Move FROM special register   | ```MOVFS RD, RN```                     | None                 |
| **CONTROL**                  |                                        |                      |
| Do nothing                   | ```NOP```                              | None                 |
| Idle                         | ```IDLE```                             | None                 |
| Return from interrupts       | ```RTI```                              | None                 |
| Interrupt global disable     | ```GID```                              | None                 |
| Interrupt global enable      | ```GIE```                              | None                 |
| Breakpoint                   | ```BKPT```                             | None                 |
| Multi-breakpoint             | ```MBKPT```                            | None                 |
| Trap                         | ```TRAP```                             | None                 |
| System call                  | ```SWI```                              | None                 |
| Launch multicore interrupt   | ```SYNC```                             | None                 |
| Wired-and (barrier)          | ```WAND```                             | None                 |


The following section contains an alphabetical listing of the Epiphany Instruction Set.

----

## ADD

**Description:**

The ADD instruction adds an integer register value (RN) with a second integer operand  (RM or SIMM3 or SIMM11). The result is written to register RD.

**Syntax:**
* ADD RD, RN, RM
* ADD RD, RN, #SIMM3
* ADD RD, RN, #SIMM11

**Operands:**
* RD: Destination register
* RN: First operand register
* RM: Second operand register
* SIMM3: 3 bit signed immediate value
* SIMM11: 11 bit signed immediate value

**Flags Updated:**
* AN	Flag
* AZ	Flag
* AV	Flag
* AC	Flag

**Operation:**
```
RD = RN + OP2;
AN = RD[31];
AC = CARRY OUT;
if ( RD[31:0] == 0 ) { AZ=1 } else { AZ=0 }
if (( RD[31] & ~RM[31] & ~RN[31] ) | ( ~RD[31] & RM[31] & RN[31] ))
{ OV=1 }
else	{ OV=0 }
AVS = AVS | AV
```

****Example:****
```
ADD R1, R1, R3		; add R3 to R1 and write result to R1
ADD R2, R1, #2		; immediate should have '#'
ADD R2, R1, #-100	 ; add signed value -100 to R1 and write result to R2
```
----

## AND

**Description:** The AND instruction performs a logical “AND” between operand RN and operand RM and places the result in register RD.

**Syntax:**
* AND RD, RN, RM

**Operands:**
* RD:	Destination register
* RN:	First operand register
* RM:	Second operand register

**Flags Updated:**
* AN	Flag
* AZ	Flag
* AV	Flag
* AC	Flag

**Operation:**
```
RD = RN & RM
AN = RD[31]
AV = 0
AC = 0
If ( RD[31:0] == 0 ) { AZ=1 } else { AZ=0 }
```

**Example:**
```
AND R2, R1, R0     ;
```

----

## ASR

**Description:**
The ASR instruction performers an arithmetic shift right of the RN operand based on the unsigned 5 bit shift value in IMM5 or RM. The result is sign extended using bit RN[31] and placed in register RD.

**Syntax:**
* ASR RD, RN, RM
* ASR RD, RN, #IMM5

**Operands:**
* RD: Destination register
* RN: First operand register
* RM: Second operand register
* IMM5:	Five bit unsigned immediate value

**Flags Updated:**
* AN	Flag
* AZ	Flag
* AV	Flag
* AC	Flag

**Operation:**
```
RD = RN >>> OP2
AN = RD[31]
AV = 0
AC = 0
if ( RD[31:0] == 0 ) { AZ=1 } else { AZ=0 }
```

**Example:**
```
ASR R0,R1,R2;
```

----

## BCOND

**Description:**
The branch instruction causes a branch to a target address based on the evaluation of one of 16 condition codes. The instruction allows conditional and unconditional branching forwards and backwards relative to the current value of the program counter. All branches are relative with respect to the current program counter.

**Syntax:**
* BCOND SIMM8
* BCOND SIMM24

**Operands:**
* COND: One of 15 conditions to evaluate before performing the jump(branch). An empty field refers to an unconditional branch.
* SIMM8: Signed 8 bit immediate value to add to current PC. The value is left shifted by 1 bit before being added to the PC.
* SIMM24: Signed 24 bit immediate value to add to current PC. The value is left shifted by 1 bit before being added to the PC.

**Flags Updated:**
* None

**Operation:**
```
IF (Passed)COND)) then
PC = PC +(SignExtend(SIMM) << 1)
```

**Example:**
```
_inf:	MOV R0,#10  ; loop 10 times
			_loopA:	ADD R1,R1#1 ; some operation
		SUB R0,R0,#1 ; decrements loop counter
		BEQ _loopA  ; branch while true
		B  _inf   ; keep executing forever 
```

----

## BL

**Description:**
The branch instruction causes the upcoming PC to be saved in the LR register followed by a branch to a target. The branch is relative with respect to the current 	program counter.

**Syntax:**
* BL SIMM8
* BL SIMM24

**Operands:**
* SIMM8: Signed 8 bit immediate value to add to current PC. The value is left shifted by 1 bit before being added to the PC.  
* SIMM24: Signed 24 bit immediate value to add to current PC. The value is left shifted by 1 bit before being added to the PC.

**Flags Updated:**
* None

**Operation:**
```
LR=next PC;
PC = PC +(SignExtend(SIMM) <<1)
```

**Example:**
```
BL _MY_FUNC  ; save PC to LR and jump to _MY_FUNC

```
     
----

## BITR

**Description:**
The BITR instruction reverses the order of the bits in the operand RN, the LSB becomes the MSB and the MSB becomes the LSB, etc. and places the result in register RD.

**Syntax:**
* BITR  RD, RN

**Operands:**
* RD:	Destination register
* RN:	First operand register

**Flags Updated:**
* AN	Flag
* AZ 	Flag
* AV 	Flag
* AC 	Flag

**Operation:**
```
for(i=0;i<32;i=i+1){
	RD[i]=RN[31-i];
}
if (RD[31:0]==0) { AZ=1 } else { AZ=0}	
AN 	= RD[31]
AV 	= 0
AC   = 0
```

**Example:**
```
MOV 	R0,%low(x87654321)	;
MOVT	R0,%high(x87654321)	;
BITR R0,R0 			        ;R0 gets 0x84C2A6B1
````

----

## BKPT

**Description:**
The BKPT instruction causes the processor to halt and wait for external inputs. The instruction is only be used by the debugging tools such as GDB and should not be user software. The instruction is included here only for the purpose of reference.

**Syntax:**
* BKPT

----

## EOR

**Description:**
The EOR instruction logically XORs the operand in register RN with the operand in register RM and places the result in register RD.

**Syntax:**
* EOR RD, RN, RM

**Operands:**
* RD:	Destination register 
* RN:	First operand register
* RM:	Second operand register

**Flags Updated:**
* AN	Flag
* AZ 	Flag
* AV 	Flag
* AC 	Flag

**Operation:**
```
RD 	= RN ^ RM
AN 	= RD[31]
AV 	= 0
AC   = 0
if (RD[31:0]==0) { AZ=1 } else { AZ=0}
```

**Example:**
```
EOR R2,R0,R1	;
```

----

## FABS

**Description:**
The FABS instruction calculates the absolute value of a floating-point value in register value RN and and places the result in register RD. The operation updates the floating-point arithmetic flags.

**Syntax:**
* FABS RD, RN

**Syntax:**
* RD			Destination register
* RN			First operand register

**Flags Updated:**
* BN	Flag
* BZ 	Flag
* BV 	Flag
* BIS 	Flag
* BUS	Flag
* BVS	Flag

**Operation:**
```
RD = abs(RN)
			N = RD[31]
			if (RD[30:0]==0) { BZ=1 } else { BZ=0}
			if (UnbiasedExponent(RD) > 127) { BV=1 } else { BV=0}
			if (UnbiasedExponent(RD) < -126) { BUS=1 } else { BUS=BUS}
			if (RM or RN == NAN) { BIS=1 } else { BIS=BIS}
			BVS = BVS | BV;
```
**Example:**
```
FABS R2,R1;
```

----

## FADD

**Description:**
The FADD instruction adds two 32-bit floating-point operands together and places the result in a third register. The operation updates the floating point arithmetic flags.

**Syntax:**
* FADD RD, RN, RM

**Operands:**
* RD	Destination register
* RN	First operand register
* RM	Second operand register

**Flags Updated:**
* BN	Flag
* BZ 	Flag
* BV 	Flag
* BIS 	Flag
* BUS	Flag
* BVS	Flag

**Operation:**
```
RD=RN + RM
BN = RD[31]
if (RD[30:0]==0) { BZ=1 } else { BZ=0}
if (UnbiasedExponent(RD) > 127) {B OV=1 } else { BV=0}
if (UbiasedExponent(RD) < -126) { BUS=1 } else { BUS=BUS}
if (RM or RN == NAN) { BIS=1 } else { BIS=BIS}
BVS = BVS | BV;
```
**Example:**
```
FADD R2,R2,R0;
```

----

## FIX

**Description:**
These FIX instruction converts the floating-point RN operand to a 32-bit fixed-point signed integer result. The floating-point operand is rounded or truncated. The result is placed in register RD. A NAN input returns a floating-point all ones result. All underflow results, or input which are zero or denormal, return zero. Overflow result always returns a signed saturated result: 0x7FFFFFFF for positive, and 0x80000000 for negative.

**Syntax:**
* FIX RD, RN

**Operands:**
* RD	Result register for converted fixed point result
* RN	Floating-point operand register to convert.


**Flags Updated:**
* BN	Flag
* BZ 	Flag
* BV 	Flag
* BIS 	Flag
* BUS	Flag
* BVS	Flag

**Operation:**
```
RD = fix(RN)
N = RD[31]
if (RD[30:0]==0) { BZ=1 } else { BZ=0}
if (UnbiasedExponent(RD) > 127) { BV=1 } else { BV=0}
if (UbiasedExponent(RD) < -126) { BUS=1 } else { BUS=BUS}
if (RM or RN == NAN) { BIS=1 } else { BIS=BIS}
BVS = BVS | BV;
```
**Example:**
```
FIX R2,R1;
```

----

## FLOAT

**Description:**
The FLOAT instructions convert the fixed-point operand in RN to a floating-point result. The final result is placed in register RD. Rounding is to nearest (IEEE) or by truncation, to a 32-bit boundary, as defined by the rounding mode. Overflow returns ±infinity (round-to-nearest),underflow returns ±zero.

**Syntax:**
* FLOAT RD, RN

**Operands:**
* RD	Result register for converted fixed point result
* RN	Floating-point operand register to convert.


**Flags Updated:**
* BN	Flag
* BZ 	Flag
* BV 	Flag
* BIS 	Flag
* BUS	Flag
* BVS	Flag

**Operation:**
```
RD = float(RN)
N = RD[31]
if (RD[30:0]==0) { BZ=1 } else { BZ=0}
if (UnbiasedExponent(RD) > 127) { BV=1 } else { BV=0}
if (UbiasedExponent(RD) < -126) { BUS=1 } else { BUS=BUS}
if (RM or RN == NAN) { BIS=1 } else { BIS=BIS}
BVS = BVS | BV;
```
**Example:**
```
FLOAT R2,R1;
```

----

## FMADD

**Description:**
The FMADD instruction multiplies one floating-point register value (RM) with a second 	floating-point register value (RN), adds the result to a third register(RD) and writes and places the result in register RD. The operation updates the floating-point arithmetic flags.

**Syntax:**
* FMADD RD, RN, RM;

**Operands:**
* RD:	Accumulation register for fused multiply add instruction
* RN:	First operand register
* RM:	Second operand register


**Flags Updated:**
* BN	Flag
* BZ 	Flag
* BV 	Flag
* BIS 	Flag
* BUS	Flag
* BVS	Flag

**Operation:**
```
RD = RD + RN * RM
BN = RD[31]
if (RD[30:0]==0) { BZ=1 } else { BZ=0}
if (UnbiasedExponent(RD) > 127) { BV=1 } else { BV=0}
if (UbiasedExponent(RD) < -126) { BUS=1 } else { BUS=BUS}
if (RM or RN == NAN) { BIS=1 } else { BIS=BIS}
BVS = BVS | BV;
```
**Example:**
```
FMADD R2,R1,R0;
```

----

## FMUL

**Description:**
The FMUL instruction multiplies one floating-point register value (RM) with a second floating-point register value (RN) and places the result in register RD. The operation updates the floating-point arithmetic flags.

**Syntax:**		FMUL RD, RN, RM;

RD			Destination register
RN			First operand register
RM			Second operand register


**Flags Updated:**	BN	Flag
			BZ 	Flag
			BV 	Flag
			BIS 	Flag
			BUS	Flag
			BVS	Flag

**Operation:**		RD=RN * RM
			N = RD[31]
			if (RD[30:0]==0) { BZ=1 } else { BZ=0}
			if (UnbiasedExponent(RD) > 127) { BV=1 } else { BV=0}
			if (UbiasedExponent(RD) < -126) { BUS=1 } else { BUS=BUS}
			if (RM or RN == NAN) { BIS=1 } else { BIS=BIS}
			BVS = BVS | BV;

**Example:**		FMUL R2,R1,R0;

----

## FMSUB

**Description:**
The FSUB instruction multiplies one floating-point register value (RM) with a second floating-point register value (RN), subtracts the result from a third register(RD) and writes and places the result in register RD. The operation updates the floating-point arithmetic flags.

**Syntax:**
* FMSUB RD, RN, RM

**Operands:**
* RD	Accumulation register for fused multiply sub instruction
* RN	First operand register
* RM	Second operand register


**Flags Updated:**
* BN	Flag
* BZ 	Flag
* BV 	Flag
* BIS Flag
* BUS	Flag
* BVS	Flag

**Operation:**
```
RD = RD - RN * RM
N = RD[31]
if (RD[30:0]==0) { BZ=1 } else { BZ=0}
if (UnbiasedExponent(RD) > 127) { BV=1 } else { BV=0}
if (UbiasedExponent(RD) < -126) { BUS=1 } else { BUS=BUS}
if (RM or RN == NAN) { BIS=1 } else { BIS=BIS}
BVS = BVS | BV;
```

**Example:**
```
FMSUB R2,R1,R0;
```

----

## FSUB

**Description:**
The FSUB instruction subtracts one floating-point register value(RM) from another floating 	point register value(RN) and places the result in a third destination register(RD). The operation updates the floating-point arithmetic flags.

**Syntax:**
* FSUB RD, RN, RM

**Operands:**
* RD	Destination register
* RN	First operand register
* RM	Second operand register

**Flags Updated:**
* BN	Flag
* BZ 	Flag
* BV 	Flag
* BIS	Flag
* BUS	Flag
* BVS	Flag

**Operation:**
```
RD=RN - RM
BN = RD[31]
if (RD[30:0]==0) { BZ=1 } else { BZ=0}
if (UnbiasedExponent(RD) > 127) { BV=1 } else { BV=0}
if (UbiasedExponent(RD) < -126) { BUS=1 } else { BUS=BUS}
if (RM or RN == NAN) { BIS=1 } else { BIS=BIS}
BVS = BVS | BV;
```

**Example:**
```
FSUB R2,R1,R0;
```

----

## GID

**Description:**
Disables all interrupts

**Syntax:**
* GID

**Flags Updated:**
* None

**Operation:**
```
STATUS[1]=1
```

**Example:**
```
GID		;
```

----

## GIE

**Description:**
Enables all interrupts in ILAT register, dependent on the per bit settings in the IMASK register.

**Syntax:**
* GIE

**Flags Updated:**
* None

**Operation:**
```
STATUS[1]=0
```

**Example:**
```
GIE		;
```

----

## IADD

**Description:**
The IADD instruction adds two 32-bit signed integer operands together and places the result in a third register.

**Syntax:**
* IADD RD, RN, RM

**Operands:**
* RD:	Destination register
* RN:	First operand register
* RM:	Second operand register


**Flags Updated:**
* BN Flag
* BZ Flag
		

**Operation:**
```
RD=RN + RM
BN = RD[31]
if (RD[30:0]==0) { BZ=1 } else { BZ=0}
```					

**Example:**
```
IADD R2, R2, R0;
```

----

## IMADD

**Description:**
The IMADD instruction multiplies one signed integer register value (RM) with a second signed integer register value (RN), adds the result to a third register(RD) and writes and places the result in register RD.

**Syntax:**
* IMADD RD, RN, RM;

**Operands:**
* RD:	Accumulation register
* RN:	First operand register
* RM:	Second operand register


**Flags Updated:**
* BN	Flag
* BZ 	Flag
			
**Operation:**
```
RD = RD + RN * RM
N = RD[31]
if (RD[30:0]==0) { BZ=1 } else { BZ=0}
```			

**Example:**
```
IMADD R2, R1, R0
```

----

## IMSUB

**Description:**
The IMSUB instruction multiplies one signed integer register value (RM)	with a second signed integer register value (RN), subtracts the result to a third register (RD) and writes and places the result in register RD.

**Syntax:**
* IMSUB RD, RN, RM

**Operands:**
* RD:	Accumulation register
* RN:	First operand register
* RM:	Second operand register

**Flags Updated:**
* BN	Flag
* BZ 	Flag
					
**Operation:**
```
RD = RD - RN * RM
N = RD[31]
if (RD[30:0]==0) { BZ=1 } else { BZ=0}
```			

**Example:**
```
IMSUB R2, R1, R0;
```

----

## IMUL

**Description:**
The IMUL instruction multiplies two signed integer register value (RN and RM ) and places the result in register RD. 

**Syntax:**
* IMUL RD, RN, RM;

**Operands:**
* RD:	Destination register
* RN:	First operand register
* RM:	Second operand register

**Flags Updated:**
* BN	Flag
* BZ 	Flag


**Operation:**
```
RD=RN * RM
N = RD[31]
if (RD[30:0]==0) { BZ=1 } else { BZ=0}
```
		
**Example:**
```
IMUL R2, R1, R0;
```

----

## ISUB

**Description:**
The ISUB instruction subtracts one signed integer register value (RM) from another signed integer register value (RN) and places the result in a third destination register (RD). 

**Syntax:**
* ISUB RD, RN, RM

**Operands:**
* RD:	Destination register
* RN:	First operand register
* RM:	Second operand register

**Flags Updated:**
* BN	Flag
* BZ 	Flag
	
**Operation:**
```
RD=RN - RM
BN = RD[31]
if (RD[30:0]==0) { BZ=1 } else { BZ=0}
```			

**Example:**
```
ISUB R2, R1, R0;
```

----

## IDLE

**Description:**
The instruction places the core in an idle state. The PC is halted and no more instructions are fetched until an interrupt wakes up the core.

**Syntax:**
* IDLE

**Flags Updated:**
* None

**Operation:**
```
STATUS[0]=0
while(!ILAT){
PC=PC;
}
```
**Example:**
```
IDLE	;
```

----

## JALR

**Description:**
The register-and-link jump instruction causes an unconditional jump to absolute address contained in Rn. Before jumping to the compute address, the next PC is saved in the link register (LR). The instruction allows for efficient support for subroutines and allows for jumping to any address supported by the architecture.

**Syntax:**
* JALR RN

**Operands:**
* RN: 	Register with absolute address to jump to.

**Flags Updated:**
* None

**Operation:**
```
LR = PC;
PC = RN;
```

**Example:**
```
MOV R0,#_labA	;move label into register
JALR R0		    ;save PC in LR and jump to labA
```

----

## JR

**Description:**
The register jump instruction causes an unconditional jump to the absolute address in register RN. This allows for jumping to any address supported by the architecture. 

**Syntax:**
* JR RN;

**Operands:**
* RN:	Any one of the general-purpose registers.

**Flags Updated:**
* None

**Flags Updated:**
* None

**Operation:**
* PC = RN;

**Example:**
```
MOV R0,#_labA	;move label into register
JR R0;		;jump to _labA
```

----

## LDR (DISPLACEMENT)

**Description:**
The displacement mode LDR instruction loads a data from memory to a general-purpose register (RD). The memory address is the sum of the base register value (RN) and an immediate index offset. The base register is not modified by the load operation. The instruction supports loading of byte, short, word, and double data. Data must be aligned in memory according to the size of the data. For double data loads, only even RD registers can be used.

**Syntax:**
* LDRSIZE RD, [RN, #+IMM3]
* LDRSIZE RD, [RN, #+/-IMM11]

**Operands:**
* SIZE	Byte(B), Half(H), Word(), or Double(D)
* RD	Destination register for the data loaded from memory.
* RN	Register containing the base address for the load instruction.
* IMM3 	Usigned 3 bit offset value left shifted by SIZE.
* IMM11	Usigned 11 bit offset value left shifted by SIZE.  
* '-'	Specifies that immediate value should be subtracted from the base. Subtraction only available with IMM11 option

**Flags Updated:**
* None

**Operation:**
```
address= RN +/- IMM << (log2(size_in_bits/8)) ;
RD=memory[address];
```

**Example:**
```
LDRB R31,[R2] 	;loads byte
LDR R0,[R2,#1] 	;loads word
```

----

## LDR (INDEX)

**Description:**
The index mode LDR loads data from memory to a general-purpose register (RD). The memory address is the sum of the base register (RN) and an index register (RM). The base register is not modified by the load operation. The instruction supports loading of byte, short, word, and double data. Data must be aligned in memory according to the size of the data. For double data loads, only even RD registers can be used.

**Syntax:**
* LDRSIZE RD, [RN, +/-RM]

**Operands:**
* SIZE	Byte(B), Half(H), Word(), or Double(D) option
* RD	Destination register for the word loaded from memory
* RN	Register containing the base address for the load instruction
* RM	Register containing the index address to add to the base address
* '-'   Specifies that RM should be subtracted from RN


**Flags Updated:**
* None

**Operation:**
```
address= RN +/- RM;
RD=memory[address];
```
**Example:**
```
LDRB R31,[R2,R1] 	;loads byte
LDR R0,[R2,R1] 	    ;loads word
```

----

## LDR (POSTMODIFY)

**Description:**
The post-modify mode LDR loads data from memory to a general purpose register (RD). The memory address used is the value of the base register (RN). After loading the data from memory, the base value register (RN) is updated with the sum of the initial base value and the index value (RM). The instruction supports loading of byte, short, word, and double data. Data must be aligned in memory according to the size of the data. For double data loads, only even numbered RD registers can be used.

**Syntax:**		LDRSIZE RD, [RN], +/-RM

**Operands:**
* SIZE	Byte(B), Half(H), Word(), or Double(D)
* RD	Destination register for the word loaded from memory.
* RN	Register containing the base address for the load instruction.
* RM	Register containing the index address for the load instruction.
* '-'	Specifies that RM should be subtracted from RN

**Flags Updated:**
* None

**Operation:**
```
address= RN;
RD=memory[address];
RN=RN +/- RM;
```

**Example:**
```
LDRH R31,[R2],R1 	;loads short, updates R2
LDRD R0,[R2],R1 	;loads double, updates R2
```

----

## LDR (DISPLACEMENT-POSTMODIFY)

**Description:**
The post-modify mode LDR allows a word to be loaded from memory to a general-purpose register (RD). The memory address used is the value of the base register (RN). After loading the data from memory, the base value register (RN) is updated with the sum/subtraction of the initial base value and the immediate index value (IMM11). The instruction supports loading of byte, short, word, and double data. Data must be aligned in memory according to the size of the data. For double data loads, only even RD registers can be used.

**Syntax:**		LDRSIZE RD, [RN], #+/-IMM11

**Operands:**

* SIZE	Byte(B), Half(H), Word(), or Double(D)
* RD	Destination register for the data loaded from memory
* RN	Register containing the base address for the load instruction
* IMM11	Usigned 11 bit offset value left shifted by 0, 1, 2 or 3 depending on SIZE.
* '-'	Specifies that RM should be subtracted from RN

**Flags Updated:**
* None

**Operation:**
```
address= RN;
RD=memory[address];
RN=RN +/- IMM11 << (log2(size_in_bits/8));
```

**Example:**
```
LDRH R31,[R2],#1 	;loads half-word, updates R2 address
LDRD R0,[R2],#4 	;loads double, updates R2 address
```

----

## LSL

**Description:**
The LSL instruction performs a logical shift left of the RN operand based on  5 bit unsigned value (IMM5 or RM) Zeros fill the bit positions vacated by the shifted RN word and the result is placed in register RD.

**Syntax:**
* LSL RD, RN, RM
* LSL RD, RN, #IMM5

**Operands:**
* RD	Destination register
* RN	First operand register
* RM	Second operand register
* IMM5  Five bit unsigned immediate value

**Flags Updated:**
* AN
* AZ
* AV
* AC

**Operation:**
```
RD = RN << OP2
AN = RD[31]
AV = 0
AC = 0
if (RD[31:0]==0) { AZ=1 } else { AZ=0}
```

**Example:**
```
LSL	R0,R1,R2	;	
LSL R0,R1,#3	;
```

----

## LSR

**Description:**
The LSR instruction performs a logical shift right of the RN operand based on the 5 bi unsigned shift value (IMM5 or RM). Zeros fill the bit positions vacated by the shifted RN word. The result is placed in register RD.

**Syntax:**
* LSR RD, RN, RM;
* LSR RD, RN, #IMM5;

**Syntax:**
* RD	Destination register
* RN	First operand register
* RM	Second operand register
* IMM5	Five bit unsigned immediate value

**Flags Updated:**
* AN
* AZ
* AV
* AC

**Operation:**
```
RD = RN >> OP2
AN = RD[31]
AV = 0
AC = 0
if (RD[31:0]==0) { AZ=1 } else { AZ=0}
```

**Example:**
```
LSR	R0,R1,R2	;
LSR R0,R1,#3	;
```

----

## MBKPT (LABS)

**Description:**
The MBKPT instruction sends a halt signal to all cores in the system. The propagation of the MBKPT signal across the chip can be blocked by setting the appropriate edge bits in the MESHCONFIG register.

**Syntax:**
* MBKPT

**Flags Updated:**
* None
			
**Operation:**
```
for (i=0;i<all_cores;i++){
DEBUGSTATUS[0]=1;
}
```

----

## MOVCOND

**Description:**
The MOV instruction conditionally copies the contents of the source register (RN) into the destination register (RD). The condition codes are the same as those of the conditional branch instructions. A MOV without any condition field moves register RN to register RD regardless of the state of the flags.

**Syntax:**
* MOVCOND RD, RN

**Operands:**
* COND	One of the 15 condition codes. If COND is " ", copy is unconditional.
* RD	Destination register
* RN	Source register for move operation.

**Flags Updated:**
* None

**Operation:**
```
IF (COND==TRUE) then
RD = RN
```

**Example:**
```
MOVEQ	R2,R0 ;copies R0 to R2 if EQ==TRUE
MOV		R3,R1 ;copies R1 to R3
```

----

## MOV (IMMEDIATE)

**Description:**
The MOV immediate instruction copies an unsigned immediate constant in the destination register (RD).

**Syntax:**
* MOV RD, #IMM8;
* MOV RD, #IMM16;

**Operands:**
* RD	Destination register for move operation
* IMM8	8-Bit unsigned immediate value
* IMM16	16-Bit unsigned immediate value

**Flags Updated:**
* None

**Operation:**
```
RD=imm
```

**Example:**
```
MOV R0,#25		;Sets R0 to 25
```

----

## MOVT (IMMEDIATE)

**Description:**
The MOVT immediate instruction copies an unsigned immediate constant in the destination register (RD).

**Syntax:**
* MOVT RD, #IMM16;

**Operands:**
* RD		Destination register for move operation.
* IMM16		16-Bit unsigned immediate value.

**Flags Updated:**
None

**Operation:**
```
RD=Rd(low) | (IMM16 << 16)
```

**Example:**
```
MOV R0,%low(0x90000000)		;sets lower 16 bits to value, zeroes out upper bits
MOVT R0,%high(0x90000000)	;sets upper 16-bits, keeps lower 16 bits intact
```

----

## MOVFS

**Description:**
The MOVFS instruction copies a value from a special core control register to a general-purpose register.

**Syntax:**
* MOVFS RD, MMR;

**Operands:**
* MMR		Memory mapped "special" register to copy
* RD		General-purpose destination register for move operation

**Flags Updated:**
* None

**Operation:**
```
RD =MMR
```

**Example:**
```
MOVFS R0, CONFIG	;copies CONFIG value to R0
```
----

## MOVTS

**Description:**
The MOVTS instruction copies a value from a general purpose register file to a core control registers.

**Syntax:**		MOVTS MMR, RN

**Operands:**
* MMR	Special Register to copy value into
* RN	General-purpose source register for move operation

**Flags Updated:**
* None

**Operation:**
```
MMR = RN
```

**Example:**
```
MOVTS CONFIG,R0	;copies R0 to CONFIG register
```

----

## NOP

**Description:**
The instruction does nothing, but holds an instruction slot.

**Syntax:**
* NOP

**Flags Updated:**
* None

**Operation:**
```
no operation
```

**Example:**
```
NOP		;
```

----

## ORR

**Description:** The ORR instruction logically 'OR's the RN and RM operands and places the result in register RD.

**Syntax:**
* ORR RD, RN, RM

**Operands:**
* RD	Destination register 
* RN	First operand register
* RM	Second operand register

**Flags Updated:**
* AN	Flag
* AZ 	Flag
* AV 	Flag
* AC 	Flag

**Operation:**
```
RD 	= RN | RM
AN 	= RD[31]
AV 	= 0
AC   = 0
if (RD[31:0]==0) { AZ=1 } else { AZ=0}
```

**Example:**
```
ORR R2,R1,R0	;
```

----

## RTI

**Description:**
The RTI instruction causes the address in the IRET register to be restored to the PC register, a clearing of the corresponding bit in the IPEND register. All actions are carried out as a single atomic operation.

**Syntax:**		RTI;

**Flags Updated:**	None

**Operation:**		IPEND[i]=0; where i is the current interrupt level being serviced
			STATUS[1]=0;
			PC=IRET;
			<execute instruction at PC>

**Example:**		RTI	;




----

## RTS (alias instruction)

**Description:**
This is an alias instruction for JR LR. When branching to a subroutine using the BL or JALR instruction, the next instruction PC is saved in register R14 (LR). It is used to return from a subroutine/function in the program.

**Syntax:**
* RTS;

**Flags Updated:**
* None

**Operation:**
```
PC=R14;
```

**Example:**
```
RTS	;
```

----

## SUB

**Description:**
The SUB instruction subtracts an integer register value (OP2) from an integer value in register (RN). The OP2 operand can be an immediate value (SIMM3 | SIMM11) or register value (RM).

**Syntax:**
* SUB RD, RN, RM
* SUB RD, RN, #SIMM3
* SUB RD, RN, #SIMM11

**Operands:**
* RD: Destination register
* RN: First operand register
* RM: Second operand register
* SIMM3: 3 bit signed immediate value
* SIMM11: 11 bit signed immediate value

**Flags Updated:**
* AN	Flag
* AZ	Flag
* AV	Flag
* AC	Flag

**Operation:**
```
RD = RN - OP2
AN = RD[31]
AC = BORROW
if (RD[31:0]==0) { AZ=1 } else { AZ=0}
if ((RD[31] & ~RM[31] & RN[31]) | (RD[31] & ~RM[31] & RN[31]) ) 		{ OV=1 }
else { OV=0 }
AVS = AVS | AV
```

**Example:**
```
SUB R2,R1,R0	;
```

----

## STR (DISPLACEMENT)

**Description:**
The displacement mode STR stores a word to memory from a general purpose register (RD). The memory address is the sum of the base register value (RN) and an immediate index offset. The base register is not modified by the store operation. The instruction supports storing of byte, short, word, and double data. Data must be aligned in memory according to the size of the data. For double data stores, only even RD registers can be used.

**Syntax:**
* STRSIZE RD, [RN, #+IMM3]
* STRSIZE RD, [RN, #+/-IMM11]

**Operands:**
* SIZE	Byte(B), Half(H), Word(), or Double(D)
* RD	Destination register for the data loaded from memory.
* RN	Register containing the base address for the load instruction.
* IMM3 	Usigned 3 bit offset value left shifted by SIZE.
* IMM11	Usigned 11 bit offset value left shifted by SIZE.  
* '-'	Specifies that immediate value should be subtracted from the base. Subtraction only available with IMM11 option

**Flags Updated:**
* None

**Operation:**
```
address= RN +/- IMM << (log2(size_in_bits/8));
memory[address]=RD;
```

**Example:**
```
STRB	R31,[R2,#1]	  ;stores byte to addr in R2
STR 	R0,[R2,#0x4]  ;stores word to addr in R2
```

----

## STR (INDEX)

**Description:**
The index mode STR stores a word to memory from a general-purpose register (RD). The memory address is the sum of a base register (RN) and an index register.(RM) The base register is not modified by the store operation. The instruction supports loading of byte, short, word, and double data. Data must be aligned in memory according to the size of the data. For double data loads, only even numbered RD registers can be used.

**Syntax:**
STRSIZE RD, [RN, +/-RM]

**Operands:**
* SIZE	Byte(B), Half(H), Word(), or Double(D)
* RD	Destination register for the data loaded from memory.
* RN	Register containing the base address for the load instruction.
* RN	Register containing the base address for the store instruction.
* '-'	Specifies that immediate value should be subtracted from the base. Subtraction only available with IMM11 option

**Flags Updated:**
* None

**Operation:**
```
address= RN +/- RM;
memory[address]=RD;
```

**Example:**
```
STRB	R31,[R2,R1]	;stores byte to addr in R2
STR 	R0,[R2,R1]	;stores word to addr in R2
```


----

## STR (POSTMODIFY)

**Description:**
The postmodify STR instruction stores a word in memory from a general purpose register (RD). The memory address used is the value of the base register (RN). After storing the the data in memory, the base value register (RN) is updated with the sum of the initial base value(RN) and the index value in (RM). The instruction supports loading of byte, short, word, and double data. Data must be aligned in memory according to the size of the data. For double data loads, only even RD registers can be used.

**Syntax:**
* STRSIZE RD, [RN], +/-RM

**Operands:**
* SIZE	Byte(B), Half(H), Word(), or Double(D)
* RD	Source register for the word stored to memory.
* RN	Register containing the base address for the store instruction.
* RM	Register containing the index address for the store instruction.
* -		Specifies that RM should be subtracted from RN

**Flags Updated:**
* None

**Operation:**
```
address= RN;
memory[address]=RD;
RN=RN +/- RM;
```

**Example:**
```
STRH	R31,[R2], R1	;stores half word to addr in R2
STRD	R0,[R2], R3	;stores double to addr in R2
```

----

## STR (DISPLACEMENT-POSTMODIFY)

**Description:**
The postmodify STR instruction stores a word in memory from a general purpose register (RD). The memory address used is the value of the base register (RN). After storing the the data in memory, the base value 	register (RN) is updated with the sum of the initial base value(RN) 	and the index value in (IMM11). The instruction supports loading of byte, short, word, and double data. Data must be aligned in memory according to the size of the data. For double data loads, only even RD registers can 	be used.

**Syntax:**
* STRSIZE RD, [RN], +/-IMM11

**Operands:**

* SIZE	Byte(B), Half(H), Word(), or Double(D)
* RD	Source register for the data stored to memory
* RN	Register containing the base address for the load instruction
* IMM11	Usigned 11 bit offset value left shifted by 0, 1, 2 or 3 depending on SIZE.  
* '-'	Specifies that RM should be subtracted from RN

**Flags Updated:**
* None

**Operation:**
```
address= RN;
memory[address]=RD;
RN=RN +/- IMM11 << (log2(size_in_bits/8));
```

**Example:**
```
STRH	R31,[R2],#2	;stores half word to addr in R2
STRD	R0,[R2],#1	;stores double to addr in R2
```

----

## SYNC (LABS)

**Description:**
The SYNC instruction sends an interrupt to all processors SYNC input signals connected to the core asserting the SYNC. 

**Syntax:**
* SYNC


**Flags Updated:**
* None
			
**Operation:**
* Sets the ILAT[0] of all cores within a work group to “1”.

----

## TRAP

**Description:**
The TRAP instruction causes the processor to halt and wait for external inputs. The immediate field within the instruction opcode is not processed by the hardware but can be used by software such as a debugger or operating system to find out the reason for the TRAP instruction.

**Syntax:**
* TRAP IMM5

**Operands:**
* IMM5		An unsigned 5 bit value indicating what action to take.

**Codes:**
* 0-2 	= reserved
* 3 	= program exit indicator
* 4 	= indicates success, can be used to indicate “test passed”
* 5 	= indicates assertion, test “failed”
* 6 	= reserved
* 7 	= initiates system call

In the case of TRAP 7, a system call is initiated. In this case, a sub argument needs to be passed in R3 indicating what further action to take, based on the following table. Arguments to the system calls are passed in Register R0-R2.


|Function     |  R0               |  R1            |  R2           | R3 |
|-------------|-------------------|----------------|---------------|----|
| File Open   | Path Name Pointer | 0              | 0             | 2  |
| File Close  | File Descriptor   | 0              | 0             | 3  |
| Read        | File Descriptor   | Buffer Pointer | Buffer Length | 4  |
| Write       | File Descriptor   | Buffer Pointer | Buffer Length | 5  |
| File Lseek  | File Descriptor   | File Offset    | Whence        | 6  |
| File Unlink | Path Name Pointer | 0              | 0             | 7  |
| Fstat       | Path Name Pointer | Status Pointer | 0             | 10 |
| Stat        | File Descriptor   | Status Pointer | 0             | 15 |

**Flags Updated:**
* None

**Operation:**
```
Halts processor; //puts it in debug halt mode
```
**Example:**
```
TRAP 0	;Halt processor to prepare for write
```
----

## TESTSET

**Description:**
The TESTSET instruction does an atomic “test-if-not-zero”, then conditionally writes a value to any memory location within the Epiphany architecture. The absolute address used for the test and set instruction must be located within the on-chip local memory and must be greater than 0x00100000. The instruction tests the value of a specific memory location and if that value is zero, writes in a new value from the local register file. If the value at the memory location was already set to a non-zero value, then the value is returned to the register file, but the memory location is left unmodified.

**Syntax:**
* TESTSET RD, [RN, +/-RM];

**Flags Updated:**
* None
			

**Operation:**
```
if ([RN+/-RM]) {
  RD= ([RN+/-RM])
}
else{
 ([RN+/-RM])=RD
 RD=0;	
}
```
	
**Example:**
```
/*example of trying to lock on value in memory*/
_loop: MOV R2, R3          ; value to write
TESTSET R2, [R0, R1]; test-set
SUB R2, R2, #0      ; check result
BNE_loop            ; keep trying
```

----

## WAND (LABS)

**Description:**
The WAND instruction sets flag bit [3] in the STATUS register and moves	on to the next instruction. When all cores in a group have set their respective wand bits, then an interrupt is generated on the WAND interrupt line. The instruction can be used to create fast distributed barriers.

**Syntax:**
* WAND

**Flags Updated:**
* None
			
**Operation:**
```
STATUS[3]=1;
```

**Example:**
```
WAND;
IDLE; /*wait for every core in the group to execute WAND*/
```

----
Copyright 2008-2016 Adapteva Inc. All rights reserved.
(See LICENSE.md for details)

