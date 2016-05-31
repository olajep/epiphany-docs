Epiphany Versions
==============================================================================

This chapter documents the differences between different versions of the Epiphany architecture.

|   Register          | Epiphany-III         |  Epiphany-IV                   |
|---------------------|----------------------|--------------------------------|
|   STATUS[19:16]     | 0100=unimplemented   |  1111=unimplemented            |
|                     | 0001=swi             |  1110=swi                      |
|                     | 0010=unaligned       |  1101=unaligned                |
|                     | 0101=illegal access  |  1100=illegal access           |
|                     | 0011=fpu exception   |  0111=fpu excpetion            |
|   CONFIG[11:8]      | N/A                  |  0011=Enables 64 bit counter   |
|   MESHCONFIG[7:4]   | 1111=N/A             |  1111=Counts any access        |
|   CMESHROUTE        | N/A                  |  Available (LABS)              |
|   RDMESHROUTE       | Nk/A                 |  Available (LABS)              |
|   XMESHROUTE        | N/A                  |  Available (LABS)              |

----
Copyright 2008-2016 Adapteva Inc. All rights reserved.
(See LICENSE.md for details)

