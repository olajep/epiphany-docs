Debugger
=========================================================================
9.1 Overview

The Epiphany debugger (e-gdb) is based on the popular GNU GDB debugger. It allows the programmer to see what is going on inside a program while it executes. Some of the powerful debug features enabled by the debugger include: 
Interactive program load
Stopping program on specific conditions (usually a breakpoint placed in source code)
Examine complete state of machine and program once program has stopped.
Continuing program one instruction at a time or until the next stop condition is met.

The Epiphany debugger supports program debugging using the functional simulator as a target or the hardware platform as a target using the ‘e-server’. The only difference between the two modes of debugging is the argument specified with the ‘target’ command within the debugger client. The simulator only supports debugging programs running on a single Epiphany CPU core and is not multi-core aware.

Note: In order to enable program debugging, the compiler should be invoked with the –g option.

9.2 Simple Examples

The following example shows how to debug a simple “Hello World” program with the Epiphany instruction set simulator.

In a Linux shell, start an e-gdb session using your executable.

$ e-gdb hello_world.elf

Inside e-gdb, connect to the instruction set simulator debugging target.

(gdb) target sim

Load the program to the core memory.

(gdb) load

Place a breakpoint at the main function entry point.

(gdb) b main

Run the functional simulator.

(gdb) run

Continue program execution from breakpoint.

(gdb) c

Program then runs until completion and displays.

“Hello World!”

Exit debugger

(gdb) q


The following example shows how to debug a program running on an Epiphany based hardware target.

Make sure that a connection has been established with the hardware using the e-server program:

$ e-server –hdf ${EPIPHANY_HOME}/bsps/emek3/emek3.xml -test-memory

In a Linux shell, start a e-gdb session using your executable (same as for the simulator).

$ e-gdb hello_world.elf

Inside e-gdb, connect to the TCP/IP socket connected to core that you want to debug. 

(gdb) target remote:51000

Load the program the core memory.

(gdb) load

Place a breakpoint at the main function entry point.

(gdb) b main

Continue program execution from breakpoint.

(gdb) c

Program then runs until completion and displays.

“Hello World!”

Exit debugger

(gdb) q
9.3 Command Line Options

Invoke the debugger by running the program ‘e-gdb’. Once started, ‘e-gdb’ reads commands from the terminal until you tell it to exit. You can also run ‘e-gdb’ with a variety of arguments and options, to specify more of your debugging environment at the outset.

The most common way to start ‘e-gdb’ is to simply specify the program as the only argument:
$ e-gdb program.elf

Table ‎9.8: Debugger Command Line Options
Option
Function 
-x file
Execute gdb commands from file file.
-d directory
Add directory to the path to search for source files.
-quiet | q
-silent 
“Quiet”. Do not print the introductory and copyright messages.
-batch
Run in batch mode. Exit with status 0 after processing all the command files specified with ‘-x’ (and all commands from initialization files, if not inhibited with ‘-n’). Exit with nonzero status if an error occurs in executing the gdb commands in the command files.
-nowindows
-nw
“No windows”.
-windows
-w
If gdb includes a GUI, then this option requires it to be used if possible.
-async
Use the asynchronous event loop for the command-line interface. gdb processes all events, such as user keyboard input, via a special event loop.
-version
This option causes gdb to print its version number and exit


9.4 Quitting GDB

To quit ‘e-gdb’, enter ‘q’ or ‘quit’ at the ‘e-gdb’ command line. An interrupt (often Ctrl-C) does not exit from ‘e-gdb’, but rather terminates the action of any ‘e-gdb’ command that is in progress and returns to the ‘e-gdb’ command level.

9.5 Shell I/O

If you need to execute occasional shell commands during your debugging session, there is no need to leave or suspend e-gdb. You can just use the shell command:

shell command string

You may want to save the output of e-gdb commands to a file. There are several commands
To control e-gdb’s logging, use: 	

set logging [on/off]

To control name of current logfile:

set logging file

To overwrite existing logfile instead of appending:

set logging overwrite [on|off]

To specify that log should only go to file:

set logging redirect [on|off]


9.6 Getting Help

You can use help (abbreviated h) with no arguments to display a short list of named classes of Commands.

9.7 Command Syntax

An e-gdb command is a single line of input. There is no limit on how long it can be. It starts with a command name, which is followed by arguments whose meaning depends on the command name. Any text from “#” to the end of the line is a comment, which can be useful in command files.

9.8 Command Summary

Table ‎9.9: Debugger Commands
Command
Function 
info
Give information about specific keyword
breakpoint
Sets a breakpoint.
break function
Sets a breakpoint at entry to function
break -offset break +offset
Sets a breakpoint some lines forward or back from position
break linenum
Set a breakpoint at line linenum in the current source file. The current source file is the last file whose source text was printed.
break filename:linenum
Set a breakpoint at line linenum in source file filename.
break filename:function
Set a breakpoint at line linenum in source file filename.
break *address
Set a breakpoint at address address.
break
When called without any arguments, break sets a breakpoint at the next instruction to be executed in the selected stack frame
info breakpoints [n]
info break [n]
info watchpoints [n]
Print a table of all breakpoints, watchpoints, and catchpoints set and not deleted, with the following columns for each breakpoint:
watch expr
Set a watchpoint for an expression.
clear
Delete any breakpoints at the next instruction to be executed in the selected stack frame
clear function
clear filename:function
Delete any breakpoints set at entry to the function function.
clear linenum
clear filename:linenum
Delete any breakpoints set at or within the code of the specified line.
delete [breakpoints] [range...]
Delete the breakpoints, watchpoints, or catchpoints of the breakpoint ranges specified as arguments. If no argument is specified, delete all breakpoints
disable [breakpoints] [range...]
Disable the specified breakpoints—or all breakpoints, if none are listed. A disabled breakpoint has no effect but is not forgotten.
enable [breakpoints] [range...]
Enable the specified breakpoints (or all defined breakpoints). They become effective once again in stopping your program.
continue [ignore-count]
c [ignore-count]
fg [ignore-count]
Resume program execution, at the address where your program last stopped; any breakpoints set at that address are bypassed. The optional argument ignore-count allows you to specify a further number of times to ignore a breakpoint at this location; its effect is like that of ignore
step
Continue running your program until control reaches a different source line, then stop it and return control to gdb. Warning: If you use the step command while control is within a function that was compiled without debugging information, execution proceeds until control reaches a function that does have debugging information. Likewise, It will not step into a function which is compiled without debugging information. To step through functions without debugging information, use the stepi command, described below
step count
Continue running as in step, but do so count times.
next [count]
Continue to the next source line in the current (innermost) stack frame. This is similar to step, but function calls that appear within the line of code are executed without stopping.
set step-mode on
The set step-mode on command causes the step command to stop at the first instruction of a function which contains no debug line information rather than stepping over it.
set step-mode off
Causes the step command to step over any functions which contains no debug information. This is the default.
finish
Continue running until just after function in the selected stack frame returns.
until
Continue running until a source line past the current line, in the current stack frame, is reached. This command is used to avoid single stepping through a loop more than once.
stepi
stepi arg
si
Execute one machine instruction, then stop and return to the debugger.	
nexti
nexti arg
ni
Execute one machine instruction, but if it is a function call, proceed until the function returns.
handle signal keywords
The keywords allowed by the handle command can be abbreviated. Their full names are:
nostop gdb should not stop your program when this signal happens. It may still print a message telling you that the signal has come in.
stop gdb should stop your program when this signal happens. This implies the print keyword as well. print gdb should print a message when this signal happens. noprint gdb should not mention the occurrence of the signal at all. This implies the nostop keyword as well. Pass noignore gdb should allow your program to see this signal; your program can handle the signal, or else it may terminate if the signal is fatal and not handled. pass and noignore are synonyms. Nopass ignore gdb should not allow your program to see this signal. nopass and ignore are synonyms.
b
backtrace
Print a backtrace of the entire stack: one line per frame for all frames in the stack.
backtrace n
bt n
Similar, but print only the innermost n frames.
frame n
f n
Select frame number n. Recall that frame zero is the innermost (currently executing) frame, frame one is the frame that called the innermost one, and so on. The highest-numbered frame is the one for main.
list linenum
Print lines centered around line number linenum in the current source file.
set listsize count
Make the list command display count source lines
list first,last
Print lines from first to last.
disassemble
disassemble /m
disassemble /r
This specialized command dumps a range of memory as machine instructions. It can also print mixed source + disassembly by specifying the /m modifier and print the raw instructions in hex as well as in symbolic form by specifying the /r. The default memory range is the function surrounding the program counter of the selected frame.
print expr
print /f expr
expr is an expression (in the source language). By default the value of expr is printed in a format appropriate to its data type;
x/nfu addr
x addr
x
Use the x command to examine memory. n, f, and u are all optional parameters that specify how much memory to display and how to format it; addr is an expression giving the address where you want to start displaying memory. If you use defaults for nfu, you need not type the slash ‘/’. For example, ‘x/3uh 0x54320’ is a request to display three halfwords (h) of memory, formatted as unsigned decimal integers (‘u’), starting at address 0x54320. All the defaults for the arguments to x are designed to make it easy to continue scanning memory with minimal specifications each time you use x. For example, after you have inspected three machine instructions with ‘x/3i addr’, you can inspect the next seven with just ‘x/7’.
compare-sections [section-name]
Compare the data of a loadable section section-name in the executable file of the program being debugged with the same section in the remote machine’s memory, and report any mismatches.
display expr
Add the expression expr to the list of expressions to display each time your program stops.
$_
The variable $_ is automatically set by the x command to the last address examined
info all-registers
Print the names and values of all registers including floating-point and vector registers (in the selected stack frame).
info registers regname ...
Print the relativized value of each specified register regname. There are four standard registers in GDB: pc,sp,fp,ps(status). Whenever possible, these four standard register names are available on your machine even though the machine has different canonical mnemonics, so long as there is no conflict.
info mem
Print a table of all defined memory regions, with the following columns for each region:
find [/sn] start_addr, +len, val1 [, val2, ...]
find [/sn] start_addr, end_addr, val1 [, val2, ...]
Search memory for the sequence of bytes specified by val1, val2, etc. The search begins at address start addr and continues for either len bytes or through to end addr inclusive.
set var symb=lvalue
Setting a symbol value from GDB
jump linespec
jump location
Resume execution at line linespec or at address given by location. Execution stops again immediately if there is a breakpoint there.
target remote host:port
target remote tcp:host:port
Debug using a TCP connection to port on host. The host may be either a host name or a numeric ip address; port must be a decimal number. The host could be the target machine itself, if it is directly connected to the net, or it might be a terminal server which in turn has a serial line to the target.
target sim
Builtin Epiphany CPU simulator.
set debug monitor
Enable or disable display of communications messages between the debugger and the remote monitor.
detach
When you have finished debugging the remote program, you can use the detach command to release it from debugger control.
disconnect
The disconnect command behaves like detach, except that the target is generally not resumed.


9.9 Epiphany GDB Limitations

The Epiphany implementation of GDB currently lacks support for:
1.	Tracing
2.	Hardware assisted watchpoints


