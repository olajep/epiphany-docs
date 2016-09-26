Simulator ("e-run")
==============================================================================
7.1 Overview
The Epiphany Instruction Set Simulator (ISS) is an accurate and fast functional representation of the Epiphany Instruction Set Architecture. The simulator accurately models the instruction set and register map of a single Epiphany core, but does not model pipeline behavior or any of the non-CPU hardware mechanisms such as the eMesh Network-On-Chip, DMA, or timers. The simulator runs in a host Linux environment, takes a binary ELF file as an input and supports standard I/O. To simplify program debugging and profiling, the simulator supports outputting program traces.

7.2 Simple Example
The following example shows how to simulate the execution of an Epiphany elf executable using the ISS within a Linux host platform.

$ e-run hello_world.elf

The simulator will print out “Hello World!”
To get an instruction trace of the executed program, use the ‘-t’ option before the hello_world.elf argument as follows:

$ e-run -t hello_world.elf

7.3 Command Line Options

Note that the elf file should be the last argument given at the command line.

Table ‎7.6: Simulator Command Line Options
Option	
Function 
-t, --trace
Output simulated instruction trace to scree
--memory-region ADDRESS,SIZE
Defines a memory region as valid for simulator. Default is to allow 0x01MB
--help
Prints help
