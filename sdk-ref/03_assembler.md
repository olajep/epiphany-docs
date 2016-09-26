Epiphany Assembler ("e-as")
============================================================================

## Overview

The Epiphany assembler 'e-as' is based on the GNU 'as' parses a file of assembly code to produce an object file for use by the linker ‘e-ld’.

## Simple Example

The following example shows how to use the assembler to create an object file.

```sh
e-as my.S -o my-object-file.o 
```

## Command Line Options

Table ‎4.1: Assembler Command Line Options
Option
Function 
-o file
Create object file with name file.
-W
Suppress warning messages
--version
Print the version number of the assembler
-Idir
Add the directory dir to the list of directories to be searched for .include files
-g
Generate debugging information
-L
Keep local symbols
--help
Print (on the standard output) a description of the command line options

4.4 General Syntax

The ‘e-as’ assembler syntax closely follows the “AT&T” style of assembly programming.
Whitespace is one or more blanks or tabs, in any order. Whitespace is used to separate symbols, and to make programs neater for people to read.

There are four ways of rendering comments to ‘e-as’:
Anything from ‘/*’ through the next ‘*/’ is a comment. There is no nesting of comments
Anything to the right of the character ‘;’ is a comment
Anything to the right of the character ‘#’ is a comment
Anything to the right of ‘//’ is a comment

A symbol is one or more characters chosen from the set of all letters (both upper and lower case), digits and underscore ‘_’.

A statement ends at a newline character (‘\n’) or line separator character. The newline or separator character is considered part of the preceding statement. Newlines and separators within character constants are an exception: they do not end statements. It is an error to end any statement with end-of-file: the last character of any input file should be a newline.

You may write a statement on more than one line if you put a backslash (‘\’) immediately in front of any newlines within the statement. When ‘e-as’ reads a backslashed newline both characters are ignored. You can even put back-slashed newlines in the middle of symbol names without changing the meaning of your source program.

A constant is a number, written so that its value is known by inspection, without knowing any context, as shown in the examples below:

.byte 74, 0112, 092, 0x4A, 0X4a, ’J, ’\J # All the same value.

.ascii "Ring the bell\7" # A string constant.

.octa 0x123456789abcdef0123456789ABCDEF0 # A bignum.

.float 0f-31415926535E-10 # (-PI), a flonum.

A string is written between double-quotes. It may contain double-quotes or null characters. The way to get special characters into a string is to escape these characters: precede them with a backslash ‘\’ character. For example ‘\\’ represents one backslash: the first ‘\’ is an escape which tells as to interpret the second character literally as a backslash (which prevents ‘e-as’ from recognizing the second ‘\’ as an escape character).

Integer values can be expressed in multiple formats for ease of use:
A binary integer is ‘0b’ or ‘0B’ followed by zero or more of the binary digits ‘01’.
An octal integer is ‘0’ followed by zero or more of the octal digits (‘01234567’).
A decimal integer starts with a non-zero digit followed by zero or more digits (‘0123456789’).
A hexadecimal integer is ‘0x’ or ‘0X’ followed by one or more hexadecimal digits chosen from ‘0123456789abcdefABCDEF’.

Integers have the usual values. To denote a negative integer, use the unary minus operator ‘-’.

## Assembler Syntax Reference

Table ‎4.2: Assembler Control Directives
Directive
Description
Syntax
Example
.include
Include a file
.include “file”
.include “bsp0.inc”
.balignw
Halfword align the following code to alignment byte boundary (default=4). Fill skipped words with fill (default=0).
.balignw {alignment} {,fill}
.balignw 8,0x01a2
.balignl
Word align the following code to alignment byte boundary (default=4). Fill skipped words with fill (default=0).
.balignl {alignment} {,fill}
.balignl 8,0x01a2
.end
Marks the end of the assembly file. Data following this directive is not processed.
.end
.end
.fail
Generates errors or warnings during assembly. 
.fail expr
.fail 1
.err
Generate an error during assembly.
.err
.err
.print
Print a string to standard output during assembly.
.print string
.print “Assembly error”
.section
Tell the assembler to assemble the following in section expr . expr can be either .text, .data, .bss, or any symbol described in the linker description file
.section expr
.section data
.text
Tells assembler to process the following as ’text’ and tells linker to place it in data memory area
.text {subsection}
.text
.data
Tells assembler to process the following as ‘data’ and tells linker to place it in data memory area
.data {subsection}
.data
.bss
Tells assembler to process the following as ‘bss ‘and to place it in bss memory area
.bss {subsection}
.bss
.struct
Tells assembler to assemble the following in an absolute section
.struct expr
.struct 0
field1:
.struct field1+4
field2:
.org
Following code is inserted at the start of the specified section plus the offset specified as new-lc.
.org new-lc
.org 0x2000


Table ‎4.3: Assembler Symbol Directives
Directive
Description
Syntax
Example
.equ | .set
Set value of symbol equal to expr
.set symbol , expr
.set Version, “0.1”
.equiv
Sets the value of symbol equal to expr and generates an error if it was previously defined.
.equiv symbol, expr
.equiv Version, “0.1”
.global
Makes symbol visible to linker
.global symbol
.global MyFunc

Table ‎4.4: Assembler Constant Directives
Directive
Description
Syntax
Example
.byte
Define byte expr (8 bits) in memory
.byte expr{,…}
.byte 25,0x11,031
.hword | .short
Define hword expr (32 bits) in memory
.hword expr{,…}
.hword 0x5
.word | .int | .long
Define word expr (32 bits) in memory
.word expr{,…}
.word 0x32
.ascii
Define string, non-zero terminated array of bytes.
.ascii expr {….}
.ascii “Hello”
.asciz | .string
Define string, zero terminated array of bytes.
.string expr {….}
.string “Hello World!\”
.float
Define 32bit IEEE number in memory
.float expr
.float 0f3.14,0f359.1
.double
Define 64bit IEEE number in memory
.double expr
.double 0f2e1
.fill
Generate repeat copies of value that is of size size and
.fill repeat {,size } {,value}
.fill 32,4,0xffffffff


Table ‎4.5: Assembler Looping Directives
Directive
Description
Syntax
Example
.rept
Repeat the sequence of lines between .rept end .endr count number of times 
.rept count
.rept 10
.endr
Ends .rept sequence
.endr
.endr


Table ‎4.6: Assembler Conditional Directives

Directive
Description
Syntax
Example
.if
Assembles if absolute expression is not zero
.if {absolute_expr}
.if(2+4)
.elseif
Used in conjunction with .if
.elseif {absolute_expr}
.elseif(2+3)-5
.else
Used in conjunction with .if
.else
.else
.endif
Ends an .if block
.endif
.endif
.ifdef
Assembles if symbol exists
.ifdef symbol
.ifdef _my_test_
.ifndef
Assembles if symbol does not exists
.ifndef symbol
.ifndef _my_test_
.ifc
Assemble if strings are the same
.ifc string1,string2
.ifc “str1”,”str2”
.ifnc
Assemble if strings are not the same
.ifnc string1,string2
.ifnc “str1”,”str2”

Table ‎4.7: Assembler Macro Directives

Directive
Description
Syntax
Example
.macro
Defines a macro.
A macro can be defined without arguments
Arguments can be accessed by name
Macros can be accessed as ordered list or by reference arguments
..macro name {args}

Definition:
.macro ArgMacro arg1,arg2

Use:
ArgMacro 10,11
ArgMacro arg2=11,arg1=10
.endm
Marks the end of a macro
.endm
.endm
\@
Pseudo variable that contains the macro number . Can be used to generate a unique number on every macro definition
\@
MyLable@

Table ‎4.8: Assembler Digit Encoding

Type
Base
Prefix
Digits
Example
Decimal Integer
10

0-9
67
Hexadecimal Integer
16
0x or 0X
0-9,A-F
0x43
Octal Integer
8
0
0-7
083
Binary Integer
2
0b or 0B
0-1
0b01000011
Floating Point Number
10
0f or 0F
0-9
0f0.67e+2
Character
n/a
‘
ASCII Symbol
‘C
String
n/a
“ and “
ASCII Symbol(s)
“Sixty Seven”


Table ‎4.9: Assembler Expression Operators

Operation
Symbol
Precedence
Negate
-

Highest














Lowest
Compliment
~

Multiplication
*

Division
/

Remainder
%

Left Shift
<< or <

Right Shift
>> or >

Bitwise OR
|

Bitwise AND
&

Bitwise XOR
^

Bitwise OR-NOT
!

Addition
+

Subtraction
-


## Examples
