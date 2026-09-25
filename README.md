<h1 align="center">x86-64 ASM Programming on Linux</h1>
<p align="center">
  <a href="https://github.com/LilSuperUser/x86_64-asm-tutorials">
    <img alt="Architecture: x86-64" src="https://img.shields.io/badge/Architecture-x86--64-0091BD?logo=amd&logoColor=white">
  </a>
  <a href="https://github.com/LilSuperUser/x86_64-asm-tutorials">
    <img alt="Assembler: NASM" src="https://img.shields.io/badge/Assembler-NASM-D22128">
  </a>
  <a href="https://github.com/LilSuperUser/x86_64-asm-tutorials">
    <img alt="Status: Work in Progress" src="https://img.shields.io/badge/Status-Work%20in%20Progress-yellow">
  </a>
  <br>
  <a href="./LICENSE">
    <img alt="Docs License: CC BY-SA 4.0" src="https://img.shields.io/badge/Docs%20License-CC%20BY--SA%204.0-lightgrey.svg">
  </a>
  <a href="./LICENSE-CODE">
    <img alt="Code License: GPL-3.0-or-later" src="https://img.shields.io/badge/Code%20License-GPLv3-blue.svg">
  </a>
</p>

>*A from-scratch, ground-up series on x86-64 assembly on Linux - for people who already know C and the command line but have never actually written a line of assembly.*

## About This Series

This isn't an instruction reference. Instruction references already exist, and skimming one won't teach you why the machine behaves the way it does. This series is built around a different goal:

_**"Give you an accurate mental model of what's actually happening at the CPU and OS level, and only then hand you the syntax to express it."**_

The primary target is **x86-64 (AMS64) assembly on Linux**, using **NASM** and, `ld` and GDB, with real C interoperability throughout.

There's also enough 32-bit x86 and x86 history woven in to explain **why x86-64** looks the way it does.

### Who This is For

- You want to actually understand what's happening under your programs.
- You've never actually written assembly, or have only poked at it.
- You want to understand x86-64 assembly from both a programming and systems perspective
- You want a foundation for further study in areas such as OS development, reverse engineering, binary exploitation, or CPU architecture.

### What You'll be Able to Do by the End

- Read x86-64 assembly confidently, including compiler-generated output.
- Understand how Linux system calls actually work, and use them directly.
- Write, assemble, and link your own x86-64 assembly program on Linux.
- Understand ELF, linking, and loading well enough to explain how a source file becomes a running process.
- Debug and inspect binaries with GDB, `objdump`, `readelf`, `nm`, and `strace`.
- Have a solid enough foundation to move into **OS development**, **reverse engineering**, **binary exploitation**, or **deeper CPU architecture study**.

### Prerequisites and Tools Needed

- A working knowledge of C, including:
	- variables
	- pointers
	- arrays
	- functions
	- basic memory concepts
- Familiarity with basic computer architecture concepts like:
	- Bits and bytes
	- Binary and hexadecimal numbers
	- Signed and Unsigned numbers
	- etc.
- Comfortable with **Linux and the command line**.
- A basic understanding of how programs are compiled and executed is helpful but not required.

### Tools Needed

- A **64-bit x86 Linux Machine** (or a virtual machine)
- **NASM** - assembler
- **GNU `ld`** - linker
- **GDB + pwndbg** - debugger and debugging interface used throughout this tutorial
- `objdump` - disassembler and binary inspection
- `strace` - system call tracing


>[!note]
>pwndbg is a GDB extension designed to make debugging binaries easier and more informative. Learn more at [pwndbg.re](https://pwndbg.re).
>
>This tutorial series will use pwndbg alongside GDB for debugging, register inspection, memory inspection, stack analysis, etc.

---
## Table of Contents

*This series is under active development. What's below is the roadmap of what each section/note will eventually cover - not links to finished content yet. As a concept gets written up, its entry will turn into a hyperlink to the actual note.*

*In addition to that, the structure and grouping may also get reorganized over time if a clearer sequence turns up - treat the numbering as current-best-guess, not final.*

- [1. Foundations](#1-foundations)
- [2. History of x86](#2-history-of-x86)
- [3. Assembly Environment](#3-assembly-environment)
- [4. First Assembly Program](#4-first-assembly-program)
- [5. Core Instructions](#5-core-instructions)
- [6. Control Flow](#6-control-flow)
- [7. Strings and Memory Access](#7-strings-and-memory-access)
- [8. Virtual Memory](#8-virtual-memory)
- [9. The Stack and Functions](#9-the-stack-and-functions)
- [10. The Heap](#10-the-heap)
- [11. Program Startup and Termination](#11-program-startup-and-termination)
- [12. NASM Code Organization](#12-nasm-code-organization)
- [13. C and Assembly](#13-c-and-assembly)
- [14. Linux System Interface](#14-linux-system-interface)
- [15. ELF and the Toolchain](#15-elf-and-the-toolchain)

### 1. Foundations

1. **Computer Architecture Fundamentals**
	- CPU
	- ALU
	- Control Unit
	- Registers
	- Memory
	- Buses
	- Instruction execution

2. **Memory Hierarchy**
	- Registers
	- Cache
	- RAM
	- Storage
	- Locality
	- Why the hierarchy exists

3. **Interrupts and Exceptions**
	- Interrupts
	- Exceptions
	- Hardware vs Software interrupts
	- Synchronous vs Asynchronous events
	- Basic CPU response

4. **From Source Code to Machine Code**
	- High-level language
	- Compiler
	- Assembly
	- Assembler
	- Object files
	- Linker
	- Executable
	- Loader

5. **Instruction Set Architecture (ISA)**
	- What an ISA is
	- ISA vs microarchitecture
	- Instructions
	- Registers
	- Memory model

6. **ABI and Calling Conventions**
	- What an ABI is
	- ABI vs API
	- Calling conventions
	- Binary compatibility

7. **RISC and CISC**
	- RISC
	- CISC
	- Design philosophies
	- Trade-offs

8. **Character & Data Representation**
	- ASCII
	- Unicode overview
	- Characters as integers
	- Strings as byte sequences

9. **Endianness**
	- Little-endian
	- Big-endian
	- Multi-byte values in memory
	- Why x86 uses little-endian

### 2. History of x86

#### 2.1 Origins

10. **The 8086 and the Origins of x86**
	- 8086
	- 16-bit architecture
	- General-purpose registers
	- Segmentation
	- Early x86 design

11. **x86 Real Mode**
	- Real mode
	- Segment:offset addressing
	- 20-bit addresses
	- 1 MiB address space
	- BIOS-era execution

#### 2.2 32-bit x86

12. **The 80286 and Protected Mode**
	- 80286
	- Protected mode
	- Privilege levels
	- Segmentation
	- Protection

13. **The 80386 and 32-bit x86**
	- 32-bit registers
	- 32-bit addressing
	- Flat memory model
	- Paging
	- Virtual memory foundations

14. **The Evolution of 32-bit x86**
	- 386 -> 486 -> Pentium
	- Major architectural changes
	- Growing ISA extensions

#### 2.3 x86-64

15. **The 64-bit Transition**
	- Why 64-bit?
	- AMD64
	- Intel's original IA-64 approach
	- Why AMD64 became x86-64

16. **x86-64 Architecture**
	- 64-bit registers
	- Expanded register set
	- Larger address space
	- Long mode
	- Compatibility mode
	- Legacy support

17. **x86 ISA Extension**
	- MMX
	- SSE family
	- AVX family
	- AVX-512
	- Other important extensions
	- Feature detection

### 3. Assembly Environment

18. **Setting up the Assembly Environment**
	- NASM
	- `ld`
	- GDB + pwndbg
	- `objdump`
	- Basic build commands

19. **Introduction to Assembly Language**
	- Assembly vs machine code
	- Why assembly?
	- Assembly language structure
	- Instructions
	- Operands
	- Directives
	- Comments

20. **Intel (NASM) Syntax vs AT&T Syntax**
	- Intel and AT&T Syntax
	- Nasm syntax
	- Instruction format
	- Operand order and sizes
	- Constants
	- Labels
	- Directives

21. **Assembly Program Anatomy**
	- `.text` / `.data` / `.bss`
	- Labels
	- Entry point
	- Instructions vs directives

22. **x86-64 Registers and Sub-registers**
	- General-puprose registers: `RAX` - `R15`
	- `RIP` / `RSP` / `RSP` /  `RBP`
	- `RFLAGS`
	- Sub-registers
	- Register naming

23. **Addressing Modes**
	- Immediate
	- Register
	- Memory
	- Base
	- Index
	- Scale
	- Displacement
	- Effective address

24. **Introduction to Linux System Calls**
	- User space and Kernel space
	- What a systemcall is
	- Why programs need system calls
	- System call numbers
	- Basic syscall convention on x86-64 Linux
	- Passing arguments in registers
	- The `syscall` instruction
	- Return values

### 4. First Assembly Program

25. **Hello World in Assembly**
	- `write` system call
	- Source code
	- Loading syscall arguments into registers
	- Invoking `syscall`
	- Return value
	- Building and Linking
	- Execution

26. **Data Definition Directives**
	- `db` / `dw` / `dd` / `dq`
	- `resb` / `resw` / `resd` / `resq`
	- Defining strings and buffers

### 5. Core Instructions

27. **Data Movement Instructions**
	- `mov` / `movzx` / `movsx` / `movsxd`
	- Operand sizes
	- Memory/register movement

28. **LEA and Effective Address Calculation**
	- `lea`
	- Address calculation
	- Arithmetic uses of `lea`
	- Difference between `lea` and `mov`

29. **Arithmetic Instructions**
	- `add` / `sub`
	- `inc` / `dec`
	- `neg`
	- `mul` / `imul`
	- `div` / `idiv`

30. **Logical and Bitwise Instructions**
	- `and` / `or` / `xor` / `not`
	- Bit masks
	- Common bitwise patterns

31. **Shift and Rotate Instructions**
	- `shl` / `shr` / `sar`
	- `rol` / `ror`
	- Logical vs Arithmetic shifts

32. **Example: Displaying a Digit in RAX**

### 6. Control Flow

33. **The FLAGS Register**
	- `RFLAGS`, `EFLAGS`, `FLAGS`
	- Purpose of the Flags register
	- Status flags: `CF` / `ZF` / `SF` / `OF` / `PF` / `AF`
	- Control flags: `DF`
	- When each flag is set (carrry vs overflow)
	- Which instructions modify which flags
	- Which instructions leave flags unchanged
	- Reading flags with `pushfq` / `pop`
	- Inspecting flags with pwndbg
	- Clearing and Setting flags: `clc` / `stc` / `cld` / `std`

34. **Comparisons and TEST**
	- `cmp` and `test`
	- `cmp` as subtraction without storing the result
	- `test` as AND without storing the result
	- How `cmp` and `test` affect FLAGS
	- Signed vs Unsigned comparison semantics

35. **Labels and Unconditional Jumps**
	- Labels
	- `jmp`
	- Relative jumps
	- Jump targets
	- Basic control-flow structure

36. **Conditional Jumps**
	- `je` / `jne`
	- `jg` / `jge`  / `jl` / `jle`
	- `jz` / `jnz`
	- Other common `jcc` instructions
	- Signed vs Unsigned conditions
	- How conditional jumps read FLAGS
	- `cmp` -> FLAGS -> `jcc`

37. **Loops in Assembly**
	- Loop structure
	- `loop`
	- Counter-based loops
	- Conditional-jump loops
	- Translating C `for` / `while` / `do-while` loops

38. **Function CALL and RET**
	- Functions / Subroutines
	- `call` / `ret` instructions and what they do
	- Return addresses
	- Stack interaction (will be covered in detail later)
	- Direct vs Indirect calls
	- Basic subroutine control flow

### 7. Strings and Memory Access

39. **Working with Strings**
	- Strings in memory
	- Null termination
	- String pointers
	- Traversing strings

40. **String Instructions**
	- `movs` / `stos` / `lods` / `scas` / `cmps` / `rep`
	- Direction flag

41. **Example: Reading User Input and Greeting the User**
	- `read` system call
	- Input buffers in `.bss`
	- Buffer lengths

42. **Example: Calculating String Length at Runtime**
	- Pointer traversla
	- Counting bytes

### 8. Virtual Memory

43. **Virtual Memory and Process Address Space**
	- Virtual memory vs Physical memory
	- Virtual addresses
	- Process address spaces
	- User space vs Kernel space
	- Why each process has its own virtual address space

44. **Process Memory Regions**
	- Typical x86-64 Linux process memory layout
	- `.text` / `.rodata` / `.data` / `.bss`
	- Heap
	- Memory-mapped region
	- Shared libraries
	- Stack
	- Purpose of each region
	- Read / write / execute permissions
	- File-backed vs anonymous memory
	- Private vs shared mappings
	- Which regions grow and in which direction

45. **Inspecting Process Memory**
	- `/proc/<pid>/maps`
	- Memory mappings
	- Virtual address ranges
	- Permissions
	- File-backed mappings
	- Shared libraries
	- Inspecting memory regions with pwndbg

46. **Virtual Memory Concepts**
	- Pages and page frames
	- Page boundaries
	- Virtual pages vs physical frames
	- Memory protection
	- Demand paging
	- Page faults
	- Copy-on-write
	- ASLR
	- Introduction to `mmap`

### 9. The Stack and Functions

47. **The Stack**
	- Purpose/Use/Lifetime of the stack
	- Growth of the stack
	- Use of `RSP` and `RBP`
	- Stack instructions: `push` and `pop`
	- Inspecting the stack with pwndbg

48. **Stack Frames**
	- What is a stack frame
	- Function prologue and epilogue
	- General layout of a stack frame
	- Frame base, stack pointer
	- Local variables, return address
	- Inspecting the stack frames with pwndbg

49. **System V AMD64 Calling Convention**
	- Why calling conventions exists
	- Integer and pointer arguments
	- Argument registers
	- Return values
	- Caller-saved registers
	- Callee-saved registers
	- Register preservation
	- Stack-passed arguments

50. **Stack Alignment and the Red Zone**
	- 16-byte stack alignment
	- Stack alignment at function calls
	- Why alignment matters
	- The 128-byte red zone
	- When the red zone can be used

51. **Floating-Point and Variadic Arguments**
	- XMM registers
	- Floating-point arguments
	- Floating-point return values
	- Variadic functions
	- `AL` and variadic calls

52. **32-bit CDECL vs System V AMD64**
	- 32-bit x86 calling conventions
	- Stack-based arguments
	- Register-based arguments
	- Caller-saved and Callee-saved registers
	- Differences between 32-bit and 64-bit calling conventions

53. **Example: Subroutine to Print an Integer**

### 10. The Heap

54. **The Heap**
	- Purpose/Use/Lifetime of the heap
	- The heap in the process address space
	- Static vs Dynamic memory
	- Growth of heap
	- Heap vs Stack
	- `brk` and `sbrk`

55. **Dynamic Memory Allocation**
	- `malloc` / `calloc` / `realloc` / `free`
	- What a memory allocator actually does
	- Allocation and Deallocation
	- Allocation metadata
	- Memory alignment

56. **Heap Internals**
	- Heap chunks
	- Chunk metadata
	- Free lists
	- Splitting and coalescing
	- Internal fragmentation
	- External fragmentation
	- Relationship between `malloc` and `mmap`

57. **Inspecting Heap Memory**
	- Heap mappings
	- Examining heap memory with pwndbg
	- Tracking allocations
	- Examining allocator metadata
	- Observing heap growth

58. **Common Heap Memory Errors**
	- Memory leaks
	- Use-after-free
	- Double-free
	- Heap buffer overflows
	- Dangling pointers

### 11. Program Startup and Termination

59. **Program Startup**
	- What happens when a program is executed
	- Loading an executable
	- Entry point
	- `_start`
	- Initial register state
	- Initial stack
	- How the kernel initializes the process
	- `_start` vs `main`

60. **argc, argv, and envp**
	- `argc` / `argv` / `envp`
	- Initial stack layout
	- Argument strings
	- Environment strings
	- Walking `argv` and `envp`

61. **Example: Command-Line Arguments**
	- Accessing `argc` and `argv` from assembly
	- Walking the argument array
	- Accessing individual arguments
	- Argument pointers vs Argument strings

62. **Program Termination**
	- Normal program termination
	- `exit` system call
	- Exit status
	- Returning from `main`
	- `_exit`
	- What happens when a process terminates

### 12. NASM Code Organization

63. **NASM Macros**
	- `%macro`
	- Parameters
	- Expansion
	- Local labels

64. **NASM Constants and Conditional Assembly**
	- `equ` / `%define`
	- `%ifdef`
	- `%ifndef`
	- Conditional assembly

65. **NASM Include Files and Code Organization**
	- `%include`
	- Shared definitions
	- Reusable assembly components
	- Organizing larger projects

### 13. C and Assembly

66. **Reading Compiler-Generated Assembly**
	- Assembly from `gcc`: `gcc -S`
	- Optimization levels
	- Mapping C -> assembly

67. **Variables and Pointers in Assembly**
	- C variables
	- Addresses
	- Pointers
	- Dereferencing

68. **Arrays in Assembly**
	- Array layout
	- Indexing
	- Pointer arithmetic
	- Element sizes

69. **Structures in Assembly**
	- Struct layout
	- Member offsets
	- Padding
	- Alignment

70. **Calling C from Assembly**
	- System V ABI in practice
	- External symbols
	- Linking against libc
	- Calling C functions

71. **Calling Assembly from C**
	- External assembly functions
	- Object files
	- Linking
	- ABI compatibility

72. **Switch Statements and Jump Tables**
	- `switch`
	- Jump tables
	- Indirect jumps
	- Compiler-generated control flow

### 14. Linux System Interface

73. **User Mode and Kernel Mode**
	- Privilege levels
	- User space
	- Kernel space
	- Protection boundaries

74. **Linux System Calls**
	- Syscall interface
	- Syscall numbers
	- Argument registers
	- Return values
	- `syscall` and `sysret`

75. **File Descriptors**
	- File descriptors
	- `stdin` / `stdout` / `sterr`
	- Descriptor tables
	- File descriptions

76. **Introduction to Files**
	- Files
	- File metadata
	- Permissions
	- Paths

77. **Opening and Closing Files**
	- `open` and `close` system calls
	- Flags and Modes
	- Combining multiple flags and modes
	- Example: Opening and closing a file on disk

78. **Writing to a File**
	- `write` system call
	- Buffers and Return values
	- Example: Writing to a file on disk

79. Reading From a File
	- `read` system call
	- Buffers, EOF, and Return values
	- Example: Reading from a file on disk

80. **Appending to a File**
	- `O_APPEND`
	- File offsets
	- Example: Appending to a file on disk

### 15. ELF and the Toolchain

81. **From Object File to Process**
	- Assembler
	- Object file
	- Linker
	- Executable
	- Loader

82. **ELF Object Files**
	- ELF header
	- Sections
	- Symbols
	- Relocations

83. **ELF Program Headers and Segments**
	- Program headers
	- Loadable segments
	- Section vs Segment

84. **ELF Symbols and Relocations**
	- Symbol tables
	- Symbol resolution
	- Relocations
	- Relocation types

85. **Static Linking**
	- Static libraries
	- Link-time symbol resolution
	- Static executables

86. **Dynamic Linking**
	- Shared libraries
	- Dynamic loader
	- Runtime symbol resolution

87. **PLT and GOT**
	- PLT - Procedure Linkage Table
	- GOT - Global Offest Table
	- Lazy binding
	- Function calls through the PLT

88. **Position Independent code**
	- RIP-relative addressing
	- PIC
	- Shared libraries

89. **PIE and ASLR**
	- PIE
	- ASLR
	- Load addresses
	- Relationship between them

---
## Ongoing Development

This tutorial series is actively being enhanced and expanded. I am committed to continuously updating the content to cover a broader range of topics and advanced concepts in assembly programming.

Your feedback and suggestions for additional topics are welcome! Thank you for your interest and support as I develop this resource for the assembly programming community :D

## License

This repository is dual-licensed by content type:

- **Tutorial text and diagrams** (everything in `notes/` and `assets/`) are licensed under **[CC BY-SA 4.0](./LICENSE)**.
- **Example source code** (`.asm`, `.c`, build scripts, and any other code embedded in the tutorials) is licensed under **[GPL-3.0](./LICENSE-CODE)**.

GitHub's license badge in the sidebar will only pick up the `LICENSE` file (CC BY-SA 4.0) since it doesn't support dual-licensed repos natively. **This section is the source of truth for the code split**.

## Contributions

If you spot an error, have a suggestion, or want to contribute a note, feel free to open an issue or a pull request. Fork the repo, create a branch, make your changes, and submit a PR.
