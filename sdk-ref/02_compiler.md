Epiphany Compiler ("e-gcc")
=================================================================================

This chapter gives a high level overview of the Epiphany compiler, based on the popular GNU GCC compiler. The purpose of this chapter is to summarize the important features and to document additional features that are not included in the baseline GNU distribution.

## Simple Example

The following example shows how to use the compiler to create an executable from a simple program source file without any optimization. 

**Code:**
```c
#include <stdio.h>
void main (){
printf("Hello World!\n");
}
```
**Basic Compilation:**
```sh
e-gcc hello_world.c -o hello_world.elf 
```

**Compilation for Speed:**

```sh
e-gcc my_fft.c -o my_fft.elf -O3 -ftree-vectorize -funroll-loops -mfp-mode=round-nearest -mfused-madd -ffast-math
```

## Compiler Command-line Options

The GCC compiler supports a wide range of options allowing for fine grain compilation process. The following tables summarize the compiler most commonly used options grouped by type.

Table ‎3.1: General Compiler Options
Option
Function 
-c
Compile or assemble source code, but do not link
-S
Stop after compilation proper; do not assemble
-E
Stop after the preprocessing stage
-o file
Place output in file file.
-x language
Specify explicit language of input file rather than letting GCC determine language based on file name suffix. Valid values for language are: ‘c’, ‘c++’
-std=standard
Determine the language standard. To support C99, specify “-std=c99”
-D name=definition
The contents of definition are tokenized and processed as if they appeared in a ‘#define’ directive.
-Wa,option
Pass option as an option to the assembler.
-llibrary
-l library
Search the library named library when linking. It makes a difference where in the command you write this option; the linker searches and processes libraries and object files in the order they are specified. The directories searched include several standard system directories plus any that you specify with ‘-L’.
--version
Print the version number of the compiler
-Idir
Add the directory dir to the list of directories to be searched for header files.
-Ldir
Add directory dir to the list of directories to be searched for ‘-l’.
--help
Print (on the standard output) a description of the command line options understood by gcc.
--help=class
Print (on the standard output) a description of the command line options understood by the compiler that fit into a specific class. The class can be one of ‘optimizers’, ‘warnings’, ‘target’, ‘params’, or ‘language’.
@file 
Read command-line options from file.




Table ‎3.2: Warning Options
Option
Function
-w 
Inhibit all warning messages
-Werror
Make all warnings into errors
-Werror=
Make the specified warning into an error
-fsyntax-only
Check the code for syntax errors, but don’t do anything beyond that.
-Wfatal-errors
This option causes the compiler to abort compilation on the first error occurred rather than trying to keep going and printing further error messages.
-pedantic
Issue all the warnings demanded by strict ISO C and ISO C++; reject all programs that use forbidden extensions, and some other programs that do not follow ISO C and ISO C++
-Wall
This enables all the warnings about constructions that some users consider questionable, and that are easy to avoid. We highly recommend running with this switch turned on for new code. The switch turns on the following warnings:
-Waddress	-Warray-bounds (only with ‘-O2’)
-Wc++0x-compat	-Wchar-subscripts
-Wimplicit-int	-Wimplicit-function-declaration
-Wcomment	-Wformat	
-Wmain (only for C/ObjC and unless ‘-ffreestanding’) 	
-Wmissing-braces	-Wnonnull	
-Wparentheses	-Wpointer-sign	
-Wreorder	-Wreturn-type	
-Wsequence-point	-Wsign-compare (only in C++)	
-Wstrict-aliasing	-Wstrict-overflow=1	
-Wswitch	-Wtrigraphs	
-Wuninitialized (only with ‘-O1’ and above) 	
-Wunknown-pragmas	-Wunused-function	
-Wunused-label	-Wunused-value	
-Wunused-variable

Table ‎3.3: Debug Options
Option
Function
-g
Produce debugging information in stabs format.
-pg
Generate extra code to write profile information suitable for the analysis program gprof.

Table ‎3.4: Linker Options
Option
Function
-nostartfiles
Do not use the standard system startup files when linking.
-nodefaultlibs
Do not use the standard system libraries when linking.
-nostdlib
Do not use the standard system startup files or libraries when linking.
-s
Remove all symbol table and relocation information from the executable.
-static
On systems that support dynamic linking, this prevents linking with the shared libraries. On other systems, this option has no effect.
-shared
Produce a shared object which can then be linked with other objects to form an executable.
-Xlinker option
Pass option as an option to the linker.



Table ‎3.5: Optimization Options
Option
Function
-O0
Reduce compilation time and make debugging produce the expected results. This is the default.
-O1
‘-O or –O1’ turns on the following optimization flags:
-fauto-inc-dec	-fcprop-registers
-fdce	-fdefer-pop	
-fdelayed-branch	-fdse	
-fguess-branch-probability	-fif-conversion2	
-fif-conversion	-finline-small-functions	
-fipa-pure-const	-fipa-reference	
-fmerge-constants -fsplit-wide-types
-ftree-ccp	-ftree-ch	
-ftree-copyrename	-ftree-dce	
-ftree-dominator-opts	-ftree-dse	
-ftree-fre	-ftree-sra	
-ftree-ter	-funit-at-a-time

‘-O’ also turns on ‘-fomit-frame-pointer’
-O2
GCC performs nearly all supported optimizations that do not involve a space-speed tradeoff. It turns on all optimization flags in O1 and the following additional flags:
-fthread-jumps 	-falign-functions 
-falign-jumps 	-falign-loops 	
-falign-labels 	-fcaller-saves 	
-fcrossjumping 	-fcse-follow-jumps 	
-fcse-skip-blocks 	-fdelete-null-pointer-checks 	
-fdevirtualize 	-fexpensive-optimizations 	
-fgcse 	-fgcse-lm 	
-finline-small-functions 	-findirect-inlining 	
-fipa-sra 	-foptimize-sibling-calls 	
-fpartial-inlining 	-fpeephole2 	
-fregmove 	-freorder-blocks 	
-freorder-functions 	-frerun-cse-after-loop 	
-fsched-interblock 	-fsched-spec 	
-fschedule-insns 	-fschedule-insns2 	
-fstrict-aliasing 	-fstrict-overflow 	
-ftree-switch-conversion 	-ftree-pre 	
-ftree-vrp
-O3
Optimize yet more. -O3 turns on all optimizations specified by -O2 and also turns on:
-finline-functions	-funswitch-loops	
-fpredictive-commoning	-fgcse-after-reload 	
-ftree-vectorize	-fipa-cp-clone
-Os
Enables all optimization switches of O2, except for the following:
-falign-functions 	-falign-jumps 	
-falign-loops 	-falign-labels 	
-freorder-blocks 	-freorder-blocks-and-partition 	
-fprefetch-loop-arrays 	-ftree-vect-loop-version
-funroll-loops 
Unroll loops whose number of iterations can be determined at compile time or upon entry to the loop.
--param name=value
In some places, GCC uses various constants to control the amount of optimization that is done.
max-unrolled-insns:
The maximum number of instructions that a loop should have if that loop is unrolled, and if the loop is unrolled, it determines how many times the loop code is unrolled.
max-unroll-times:
The maximum number of unrollings of a single loop.



Table ‎3.6: Floating Point Math Options
Option
Function
-fsingle-precision-constant
Treat floating point constant as single precision constant instead of implicitly converting it to double precision constant. 
-funsafe-math-optimizations
Enables ‘-fno-signed-zeros’, ‘-fno-trapping-math’, ‘-fassociative-math’
and ‘-freciprocal-math’.
-freciprocal-math
Allow the reciprocal of a value to be used instead of dividing by the value if this enables optimizations.
-ffast-math
Sets ‘-fno-math-errno’, ‘-funsafe-math-optimizations’, ‘-ffinite-math-only’, ‘-fno-rounding-math’, ‘-fno-signaling-nans’ and ‘-fcx-limited-range’. This option causes the preprocessor macro __FAST_MATH__ to be defined
-fno-trapping-math
Compile code assuming that floating-point operations cannot generate user visible traps.
-fno-signed-zeros
Allow optimizations for floating point arithmetic that ignore the signedness of zero.

Table ‎3.7: Epiphany Unique Options
Option
Function
-mprefer-short-insn-regs
Preferentially allocate registers that allow short instruction generation. This can result in increased instruction count, so if this reduces or increases code size might vary from case to case.
-mbranch-cost=num

Set the cost of branches to roughly num “simple” instructions. This cost is only a heuristic and is not guaranteed to produce consistent results across releases.
-mcmove
Enable the generation of conditional moves.
-mnops=num
Emit num nops before every other generated instruction.
-mno-soft-cmpsf

For single-precision floating point comparisons, emit an fsub instruction and test the flags. This is faster than a software comparison, but can get incorrect results in the presence of NaNs, or when two different small numbers are compared such that their difference is calculated as zero. The default is -msoft-cmpsf, which uses slower, but IEEE-compliant, software comparisons.
-mno-round-nearest
Make the scheduler assume that the rounding mode has been set to truncating. The default is -mround-nearest.
-mlong-calls
If not otherwise specified by an attribute, assume all calls might be beyond the offset range of the b / bl instructions, and therefore load the function address into a register before performing a (otherwise direct) call. This is the default.
-mshort-calls
If not otherwise specified by an attribute, assume all direct calls are in the range of the B/BL instructions, so use these instructions for direct calls. The default is -mlong-calls.
-msmall16
Assume addresses can be loaded as 16 bit unsigned values. This does not apply to function addresses for which -mlong-calls semantics are in effect.
-mfp-mode=mode
Set the prevailing mode of the floating point unit. This determines the floating point mode that is provided and expected at function call and return time. Making this mode match the mode you predominantly need at function start can make your programs smaller and faster by avoiding unnecessary mode switches. 
mode can be set to one the following values: 
`caller'
Any mode at function entry is valid, and retained or restored when the function returns, and when it calls other functions. This mode is useful for compiling libraries or other compilation units you might want to incorporate into different programs with different prevailing FPU modes, and the convenience of being able to use a single object file outweighs the size and speed overhead for any extra mode switching that might be needed, compared with what would be needed with a more specific choice of prevailing FPU mode. 
`truncate'
This is the mode used for floating point calculations with truncating (i.e. round towards zero) rounding mode. That includes conversion from floating point to integer. 
`round-nearest'
This is the mode used for floating point calculations with round-to-nearest-or-even rounding mode. 
`int'
This is the mode used to perform integer calculations in the FPU, e.g. integer multiply, or integer multiply-and-accumulate. 
-mno-postmodify
Code generation tweaks that disable, respectively, splitting of 32 bit loads, generation of post-increment addresses, and generation of post-modify addresses. The defaults are msplit-lohi, -mpost-inc, and -mpost-modify.
-mnovect-double
Change the preferred SIMD mode to SImode. The default is -mvect-double, which uses DImode as preferred SIMD mode.
-max-vect-align=num

The maximum alignment for SIMD vector mode types. num may be 4 or 8. The default is 8.
-m1reg-reg

Specify a register to hold the constant −1, which makes loading small negative constants and certain bitmasks faster. Allowable values for reg are r43 and r63, which specify to use that register as a fixed register, and none, which means that no register is used for this purpose. The default is -m1reg-none.


For the Epiphany architecture, some useful optimization options are ‑falign‑loops=8 and ‑falign‑functions=8. These options direct the compiler to generate code, where the first instructions in the body of a loop or in a function are double-word aligned. Thus, the processor’s alignment buffer is kept full from the first fetch after a branch (pipeline flush). ‑ffast‑math can make math calculation faster by omitting some floating-point calculation restrictions and relaxing the standard conformance.

3.4 GNU Function Attributes

In GCC, you declare certain things about functions called in your program which help the compiler optimize function calls and check your code more carefully. The keyword __attribute__ allows you to specify special attributes when making a declaration. This keyword is followed by an attribute specification inside double parentheses:

alias	aligned	alloc_size
always_inline	artificial	cold
const	constructor	deprecated
destructor	error	externally_visible
flatten	format	format_arg
gnu_inline	hot	malloc
noreturn	returns_twice	noinline
nonnull	nothrow	pure
sentinel	no_instrument_function	section
used	unused	weak
warn_unused_result	warning

3.5 Epiphany Specific Compiler Attributes

The Epiphany compiler also supports a number of attributes used by the linker descriptor file for easy and explicit memory management. These attributes can be found in the linker chapter of this manual.

## Standard Support

The GCC compiler supports the following versions of C/C++:

ISO/IEC 9899:1990 (C89)
ISO/IEC 9899:1999 (C99)
ISO/IEC 9899:2011 (C11) (partial)
ISO/IEC 14882:1998 (C++98)
ISO/IEC 14882:2011 (C++11) (partial)
ISO/IEC 14882:2014 (C++14) (partial)

Support for C99 is substantial but not 100% complete. For a review of the minor C99 features not supported in GCC, please refer to:
http://gcc.gnu.org/c99status.html.

Similarly, C11 and C++11 are new standards and their support is partial.

More info here: http://gcc.gnu.org/onlinedocs/gcc/Standards.html.
