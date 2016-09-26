5.1 Overview

The Epiphany linker ‘e-ld’ combines a number of objects and archives, relocates their data and resolves symbol references. The following section gives a brief overview of the operations of the linker.

5.2 Simple Examples

The following example shows how to use the linker to create an elf executable from an object file using the default linker file for simulation using the Epiphany instruction set simulator.

$ e-ld my_object.o -o exec.elf 

The following example shows how to use the linker to create an elf executable for the Epiphany multicore evaluation kit.

$ e-ld –T $EPIPHANY_HOME/bsps/zedboard/fast.ldf \
    my_object.o -o exec.elf 


5.3 Command Line Options


Table ‎5.1: Linker Command Line Options

Option
Function
-Lsearchdir
--library-path=searchdir
Adds directory paths to the list of paths that ‘e-ld’ will search for archive libraries and linker control scripts. The option can be used multiple times, in which case directories are searched in the order in which they are specified on the command line. If searchdir begins with =, then the = will be replaced by the sysroot prefix, a path specified when the linker is configured.
-e entry
--entry=entry
The entry option can be used to specify the explicit symbol for beginning execution of your program, rather than the default entry point. If there is no symbol named entry, the linker will try to parse entry as a number, and use that as the entry address. (Numbers without a prefix will be interpreted in base 10; numbers starting with 0x will be interpreted as base 16.)
-M
--print-map
Prints a link map to the standard output, including information about: Object file memory placement, Common symbol, allocation, Symbol value assignments
-o output
--output=output
Specifies name of the output file of the link process. Without this option, the default output name is ‘a.out’.
-s
--strip-all
Omit all symbol information from the output file.
-S
--strip-debug
Omit debugger symbol information (but not all symbols) from the output file.
-T scriptfile
--script=scriptfile

Replaces the default linker script file with scriptfile. If script file does not exist in the current directory, the linker looks for it in the directories specified by any `-L' options. Multiple `-T' options accumulate.
-u symbol
--undefined=symbol

Forces symbol to be entered in the output file as an undefined symbol.
@file
Read command-line options from file. Options in file are separated by whitespace. The file itself may contain additional file may @file options, allowing for modular linker configuration.

5.4 Linker Script Overview

The link process is controlled by a linker script written in the GNU linker command language. The purpose of the linker script is to describe how the sections in the input files should be mapped into the output file, and to control the memory layout of the output file. If you do not supply a linker file to ‘e-ld’, it will use a default linker file.

Executables compiled with the default linker will only execute correctly using the ‘e-run’ instruction set simulator and will not work correctly when loaded on specific hardware targets. To correctly link for specific hardware targets, you should use the ‘-T’ option to specify one of the board specific linker files that come with the board support package (BSP) or your own custom linker file. 

The following list highlights some of the key concepts of the linker: 
The assembler emits an object file (partial program) with an assumed start at address 0. The linker then reads one or more object files and combines their contents to form a runnable program with no program overlap and all addresses completely resolved.
The linker script contains a number of defined input sections and output sections. These section names can be used within the source code to assist in fine grained code and data placement as explained in the following sections.
Every object file has a list of symbols, known as the symbol table. A symbol may be defined or undefined. Each symbol has a name, and each defined symbol has an address.
When compiling a C program into an object file, you will get a defined symbol for every defined function and global or static variable. Every undefined function or global variable which is referenced in the input file will become an undefined symbol.


5.5 Explicit Code and Data Memory Management 

The linker description files that come with the different Epiphany Board Support Packages have a number of key words that allow fine grained management of code and data placement from within the C/C++ source code. The keywords gives the programmer and support libraries complete control of the placement of data and code within the memory system on a per-symbol, per-file, and per-object library. The keywords are derived from section names within the linker descriptor file and can be augmented by the user at his discretion. Table 5.2 gives a summary of the memory placement keywords available in all Epiphany BSPs that can be used within your C code.
Table ‎5.2:  Memory Management Linker Symbols
Keyword
Meaning
__core_row__
The row ID of the core
__core_col__
The column ID of the core
__stack_start__
Stack starting point
__heap_start__
Heap starting point
__heap_end__
Heap ending point


Table 5.3 shows configurations of the three basic linker descriptor files. The ‘legacy’ scenario is to be used for bringing up code quickly but will run slowly since all data and code is placed in external memory. The ‘fast’ scenario is used to place user code internally and standard library `code externally. The ‘internal’ scenario can be used for to effectively place all code and data in the local memory by default. The three descriptor files effectively determine the default placement of all sections and symbols within the objects. The user can override these settings on an individual basis from the C/C++ source code using the attributes defined in Table 5.2 to specify that certain variables and/or functions should be placed in specific program output sections. Note that with all of the predefined LDF’s, the heap is allocated externally. This means that use of stdio library will render the program very slow.



Table ‎5.3: Memory Management Scenarios
File
USER CODE &
DATA
STANDARD LIBRARY
STACK
NOTE
legacy.ldf
External SDRAM
External SDRAM
External SDRAM
Use to run any legacy code with up to 1MB of combined code and data.
fast.ldf
Internal SRAM
External SDRAM
Internal SRAM
Places all user code and static data in local memory, including the stack. Use to implement fast critical functions. It is the user’s responsibility to ensure that the code fits within the local memory.
internal.ldf
Internal SRAM
Internal SRAM
Internal SRAM
Places all code and static data in local memory, including the stack. Use to implement fastest applications. It is the user’s responsibility to ensure that the code fits within the local memory.


Table ‎5.4: Linker Sections
Section
User Controllable Sections
.text
Application code, read only
.data
Application data (global variables that are not constant)
.rodata
Application data, read only (constants, strings)
.bss
Static variables initialized to zero
.text_bank0
Starts at end of reserved section in bank0
.text_bank1
Starts at the beginning of bank1
.text_bank2
Starts at the beginning of bank2
.text_bank3
Starts at the beginning of bank3
.data_bank0
End of .text_bank0
.data_bank1
End of .text_bank1
.data_bank2
End of .text_bank2
.data_bank3
End of .text_bank3
.code_dram
Code section in external memory
.shared_dram
Data section in external memory
.heap_dram
Heap section in external memory



5.6 Memory Management Examples

The Epiphany SDK gives the programmer complete control over data and code placement through section attributes that can be embedded in the source code. Memory management attributes placed in the source code will be ignored by the standard Linux GCC compiler.

The following examples illustrate some attributes that can be placed inside the source code or in a stub ‘*.c’ file that gets compiled with the rest of the application source file. The general attribute should be placed outside the main routine.

1. How to specify the core where the executable will run:
asm(".global __core_row__;");
asm(".set __core_row__,0x20;");
asm(".global __core_col__;");
asm(".set __core_col__,0x24;");

2. How to specify where the stack should start:
asm(".global __stack_start__;");
asm(".set __stack_start__,0x1ff0;");

3. How to force memory placement of a static variable:
float data[N] __attribute__ ((section (“.data_bank3”)));

4. How to force memory placement of a function in declaration:
int my_fft(int *ptr) __attribute__ ((section (“.text_bank0”)))

5. How to force a long jump attribute on a function situated in external SDRAM:
int dump_memory(int *ptr) __attribute__ ((long call));


6. ELF Utilities
6.1 Overview
The Epiphany SDK includes several utilities that can be used to effectively manipulate binary object files. This chapter provides a brief overview of these utilities.
6.2 Utility Summary
Table 6.1 explains the major binary manipulation utilities within the Epiphany SDK and their respective arguments. All utilities use the --help switch to print out a complete set of arguments.
Table ‎6.5 ELF Manipulation Programs
Utility
Note
Usage + Arguments
e-ar
Creates and manipulates archive content
-r Replace existing or insert new files into archive

e-nm
Lists the symbols in an object file

e-objcopy
Copies a binary file, possibly transforming it in the process
e-objcopy [options] in-file [out-file]
-S Remove all symbol and relocation information
-g Remove all debugging information
--srec-forceS3 Generate srec type output
--gap-fill <val> Fill gaps between sections with <val>
--set-start <addr> Set the start address to <addr>
-W <name> Force <name> symbol to be marked weak
--strip-unneeded Remove all unneeded symbols
--prefix-sections <prefix> Add prefix to section names
--prefix-symbols <prefix> Add prefix to symbol names
e-objdump
Displays information about the content of object files
e-objdump [options] file
-x Display the contents of all headers
-d Display content of all executable sections
-D Display content of all sections
-t Display content of of the symbol tabe
-T Display content of dynamic symbol table
-r Display relocation entries in file
---section=NAME Only display section NAME 
e-size
Lists the section sizes within an object file
e-size [options] file
-o|-d|-x Display numbers in octal, decimal, or hex
e-strip
Strips symbols from object files
e-strip [options] file
--remove-section=<name> Remove section <name>
-g Remove all debug symbols and sections
-s Remove all symbols and relocation information
-o <file> Place stripped output into <file>


