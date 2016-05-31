Epiphany Opcodes
============================================================================
* I = immediate, MSB denoted by I#, LSBs by simple I (example I3III)
* DM = datasize[1:0]
* B3:0 = branch code
* S = store
* M = minus (subtract)
* P = postmodify
* H = high/low immediate write
* x = don't care (should be zero for for forward compatibility)
* G = mmr group
* C3:0 = emesh control mode 3:0

| INSTR     |SIZE|31:29|28:26|25:23|22:20|19:16|15:13|12:10| 9:7  |6:4  |3:0 |
| ----------|----|-----|-----|-----|-----|-----|-----|-----|------|-----|----|
|**BRANCH** |    |     |     |     |     |     |     |     |      |     |    |
| BCOND     | 16 |     |     |     |     |     |I7II |III  |IIB3  |BBB  |0000|
| BCOND     | 32 |I23II|III  |III  |III  |IIII |III  |III  |IIB3  |BBB  |1000|
|**LDR/STR**|    |     |     |     |     |     |     |     |      |     |    |
| LDST DISP | 16 |     |     |     |     |     |RD2:0|RN2:0|I2II  |D1:0S|0100|
| LDST INDEX| 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |D1:0S|0001|
| LDST PM   | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |D1:0S|0101|
| LDST DISP | 32 |RD5:3|RN5:3|PMI10|III  |IIII |RD2:0|RN2:0|III   |D1:0S|1100|
| LDST INDEX| 32 |RD5:3|RN5:3|RM5:3|00M  |0000 |RD2:0|RN2:0|RM2:0 |D1:0S|1001|
| LDST PM   | 32 |RD5:3|RN5:3|RM5:3|00M  |0000 |RD2:0|RN2:0|RM2:0 |D1:0S|1101|
| TESTSET   | 32 |RD5:3|RN5:3|RM5:3|010  |0000 |RD2:0|RN2:0|RM2:0 |100  |1001|
|**IMM-INT**|    |     |     |     |     |     |     |     |      |     |    |
| MOV IMM   | 16 |     |     |     |     |     |RD2:0|I7II |III   |  II0|0011|
| ADD IMM   | 16 |     |     |     |     |     |RD2:0|RN2:0|I2:0  |  001|0011|
| SUB IMM   | 16 |     |     |     |     |     |RD2:0|RN2:0|I2:0  |  011|0011|
| MOV IMM   | 32 |RD5:3|HI15I|III  |III  |xxxx |RD2:0|III  |III   |  II0|1011|
| ADD IMM   | 32 |RD5:3|RN5:3|xxI10|III  |IIII |RD2:0|RN2:0|III   |  001|1011|
| SUB IMM   | 32 |RD5:3|RN5:3|xxI10|III  |IIII |RD2:0|RN2:0|III   |  011|1011|
|**SHIFT**  |    |     |     |     |     |     |     |     |      |     |    |
| LSR       | 16 |     |     |     |     |     |RD2:0|RN2:0|S4:S2 |S1:00|0110|
| LSL       | 16 |     |     |     |     |     |RD2:0|RN2:0|S4:S2 |S1:01|0110|
| ASR       | 16 |     |     |     |     |     |RD2:0|RN2:0|S4:S2 |S1:00|1110|
| BITR      | 16 |     |     |     |     |     |RD2:0|RN2:0|000   |  001|1110|
| LSR       | 32 |RD5:3|RN5:3|xxx  |xxx  |0110 |RD2:0|RN2:0|S4:S2 |S1:00|1111|
| LSL       | 32 |RD5:3|RN5:3|xxx  |xxx  |0110 |RD2:0|RN2:0|S4:S2 |S1:01|1111|
| ASR       | 32 |RD5:3|RN5:3|xxx  |xxx  |1110 |RD2:0|RN2:0|S4:S2 |S1:00|1111|
| BITR      | 32 |RD5:3|RN5:3|xxx  |xxx  |1110 |RD2:0|RN2:0|000   |  001|1111|
|**REG-INT**|    |     |     |     |     |     |     |     |      |     |    |
| ADD       | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |001  |1010|
| SUB       | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |011  |1010|
| AND       | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |101  |1010|
| ORR       | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |111  |1010|
| EOR       | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |000  |1010|
| ASR       | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |110  |1010|
| LSR       | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |100  |1010|
| LSL       | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |010  |1010|
| ADD       | 32 |RD5:3|RN5:3|RM5:3|x00  | 1010|RD2:0|RN2:0|RM2:0 |001  |1111|
| SUB       | 32 |RD5:3|RN5:3|RM5:3|x00  | 1010|RD2:0|RN2:0|RM2:0 |011  |1111|
| AND       | 32 |RD5:3|RN5:3|RM5:3|x00  | 1010|RD2:0|RN2:0|RM2:0 |101  |1111|
| ORR       | 32 |RD5:3|RN5:3|RM5:3|x00  | 1010|RD2:0|RN2:0|RM2:0 |111  |1111|
| EOR       | 32 |RD5:3|RN5:3|RM5:3|x00  | 1010|RD2:0|RN2:0|RM2:0 |000  |1111|
| ASR       | 32 |RD5:3|RN5:3|RM5:3|x00  | 1010|RD2:0|RN2:0|RM2:0 |110  |1111|
| LSR       | 32 |RD5:3|RN5:3|RM5:3|x00  | 1010|RD2:0|RN2:0|RM2:0 |100  |1111|
| LSL       | 32 |RD5:3|RN5:3|RM5:3|x00  | 1010|RD2:0|RN2:0|RM2:0 |010  |1111| 
|**FPU**    |    |     |     |     |     |     |     |     |      |     |    |
| FADD      | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |000  |0111|
| FSUB      | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |001  |0111|
| FMUL      | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |010  |0111|
| FMADD     | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |011  |0111|
| FMSUB     | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |100  |0111|
| FLOAT     | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |101  |0111|
| FIX       | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |110  |0111|
| FABS      | 16 |     |     |     |     |     |RD2:0|RN2:0|RM2:0 |111  |0111|
| FADD      | 32 |RD5:3|RN5:3|RM5:3|x00  | 0111|RD2:0|RN2:0|RM2:0 |000  |1111|
| FSUB      | 32 |RD5:3|RN5:3|RM5:3|x00  | 0111|RD2:0|RN2:0|RM2:0 |001  |1111|
| FMUL      | 32 |RD5:3|RN5:3|RM5:3|x00  | 0111|RD2:0|RN2:0|RM2:0 |010  |1111|
| FMADD     | 32 |RD5:3|RN5:3|RM5:3|x00  | 0111|RD2:0|RN2:0|RM2:0 |011  |1111|
| FMSUB     | 32 |RD5:3|RN5:3|RM5:3|x00  | 0111|RD2:0|RN2:0|RM2:0 |100  |1111|
| FLOAT     | 32 |RD5:3|RN5:3|RN5:3|x00  | 0111|RD2:0|RN2:0|RN2:0 |101  |1111|
| FIX       | 32 |RD5:3|RN5:3|RN5:3|x00  | 0111|RD2:0|RN2:0|RN2:0 |110  |1111|
| FABS      | 32 |RD5:3|RN5:3|RN5:3|x00  | 0111|RD2:0|RN2:0|RN2:0 |111  |1111|
|**MOVE**   |    |     |     |     |     |     |     |     |      |     |    |
| MOVCOND   | 16 |     |     |     |     |     |RD2:0|RN2:0|x0B3  |BBB  |0010|
| MOVTS     | 16 |     |     |     |     |     |RD2:0|MM2:0|x10   |000  |0010|
| MOVFS     | 16 |     |     |     |     |     |RD2:0|MM2:0|x10   |001  |0010|
| MOVCOND   | 32 |RD5:3|RN5:3|xxx  | xxx |0010 |RD2:0|RN2:0|x0B3  |BBB  |1111|
| MOVTS     | 32 |RD5:3|MM5:3|xxx  |xG1:0|0010 |RD2:0|MM2:0|x10   |000  |1111|
| MOVFS     | 32 |RD5:3|MM5:3|xxx  |xG1:0|0010 |RD2:0|MM2:0|x10   |001  |1111|
|**JUMP**   |    |     |     |     |     |     |     |     |      |     |    |
| JR        | 16 |     |     |     |     |     |xxx  |RN2:0|x10   |100  |0010|
| JALR      | 16 |     |     |     |     |     |xxx  |RN2:0|x10   |101  |0010
| JR        | 32 |xxx  |RN5:3|xxx  | xxx |0010 |xxx  |RN2:0|x10   |100  |1111|
| JALR      | 32 |xxx  |RN5:3|xxx  | xxx |0010 |xxx  |RN2:0|x10   |101  |1111|
|**FLOW**   |    |     |     |     |     |     |     |     |      |     |    |
| WAND      | 16 |     |     |     |     |     |xxx  |xxx  |x11   |000  |0010|
| GIE       | 16 |     |     |     |     |     |xxx  |xxx  |011   |001  |0010|
| GID       | 16 |     |     |     |     |     |xxx  |xxx  |111   |001  |0010|
| NOP       | 16 |     |     |     |     |     |xxx  |xxx  |011   |001  |0010|
| IDLE      | 16 |     |     |     |     |     |xxx  |xxx  |011   |011  |0010|
| BKPT      | 16 |     |     |     |     |     |xxx  |xxx  |011   |100  |0010|
| MBKPT     | 16 |     |     |     |     |     |xxx  |xxx  |111   |100  |0010|
| RTI       | 16 |     |     |     |     |     |xxx  |xxx  |011   |101  |0010|
| SWI       | 16 |     |     |     |     |     |xxx  |xxx  |011   |110  |0010|
| TRAP      | 16 |     |     |     |     |     |xxx  |xxx  |111   |110  |0010|
| SYNC      | 16 |     |     |     |     |     |xxx  |xxx  |011   |111  |0010|

----
Copyright 2008-2016 Adapteva Inc. All rights reserved.
(See LICENSE.md for details)


