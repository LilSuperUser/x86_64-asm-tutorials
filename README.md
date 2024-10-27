# Assembly Language Tutorials
Welcome to my Assembly Language Tutorials! This repository is designed for beginners and intermediate learners who want to dive into the world of assembly programming, particularly for x86_64 architecture.

## Table of Contents:
1. [Introduction to Computer Architecture](notes/001-Introduction_To_Computer_Fundamentals.md)
   - CPU
   - Memory
   - Buses
   - Endianness
   - Memory Hierarchy
   - Instruction Set Architecture (ISA)
   - Interrupts And Exceptionss
2. [Settign Up The Environment](notes/002-Setting_Up_Environment.md)
   - Installing NASM (assembler)
   - Installing ld (linker)
   - Installing gdb (debugger)
3. [Introduction to Assembly Language](notes/003-Introduction_To_Assembly.md)
   - What is Assembly?
   - Why Assembly?
   - Structure of an Assembly Program
   - Syntax of Assembly
   - Registers for Assembly
   - Addressing Modes
   - Assembly syntax/format
   - Switching syntax/format in assemblers or debugger
4. [Hello World Program in Assembly](notes/004-Hello_World_in_Assembly.md)
   - Syscall Structure
   - Source Code
   - Explanation
   - Data Definition Directives
   - Steps to execution
   - Same programs Using Labels
5. [Jumps, Calls, and Flags in Assembly](notes/005-Jumps_Calls_and_Flags.md)
   - Flags
   - Jumps
   - Calls
6. [Reading User Input And Greeting in Assembly](notes/006-Reading_Name_Greeting_User.md)
   - Source Code
   - Explanation
7. [Arithmetic and Logical Instructions in Assembly](notes/007-Arithmetic_and_Logical_Instructions.md)
   - Arithmetic Instructions
   - Logical Instructions
8. [Displaying A Digit in RAX](notes/008-Displaying_Digit_in_RAX.md)
   - Source Code
   - Explanation
9. [Calculating The Length of String During Runtime](notes/009-Calc_Len_of_String_During_Runtime.md)
   - Source Code
   - Explanation
10. [Virtual Memory in Assembly: Understanding Stack, Heap, and Segments](notes/010-Virtual_Memory_in_Assembly.md)
    - Introduction to Virtual Memory
    - Segments of Virtual Memory
    - Stack
    - Heap
11. [Macros in NASM](notes/011-Macros-in-nasm.md)
    - What are macros?
    - How to define a macro?
    - Local Labels in a macro definition
    - Defining constants using `equ`
    - Including external files using `%include`
12. [Subroutine to Print an Integer](notes/012-Subroutine_to_print_int.md)
    - Source code
    - Overview of the logic used
    - Explanation of the source code
13. [Command Line Arguments](notes/013-CLI_arguments.md)
    - What are command line arguments (CLI arguments)
    - Arguments on the stack
    - Program to print CLI arguments onto the screen.
14. [Introduction to files](notes/014-Introduction_to_files.md)
    - Introduction to files
    - File permissions
    - File descriptors
15. [Opening & closing a file](notes/015-Opening_&_closing_a_file.md)
    - Opening a file using `sys_open` system call
    - Flags used while opening a file
    - Modes used while opening a file
    - Combining multiple flags and modes
    - Closing a file using `sys_close` sysem call
    - Example of opening and closing a file
16. [Writing to an open file](notes/016-Writing_to_files.md)
    - `sys_write` system call breakdown
    - Example of writing to a file
17. [Reading from an open file](notes/017-Reading_from_files.md)
    - `sys_read` system call breakdown
    - Example of reading from a file
## Overview 
this tutorial series covers fundamental concepts in assembly programming, including:
- Basic computer architecture.
- The assembly language syntax and conventions.
- System calls.
- Writing to stdout.
- Reading from stdin.

Each tutorial contains detailed explanations and code examples to help you understand and apply the concepts effectively.

## Prerequisites
- Basic understanding of computer science concepts.
- Familiarity with system level programming.
- Familiarity with Linux command line.
- A working environment set up for assembly programming (please refer to the **Setting Up the Environment** tutorial for instructions).

## Getting Started
To get started, simply navigate to the tutorial files listed above. Each file contains detailed information and examples for the respective topic.

## Ongoing Development
This tutorial series is actively being enhanced and expanded. I am committed to continuously updating the content to cover a broader range of topics and advanced concepts in assembly programming.

Your feedback and suggestions for additional topics are welcome! Thank you for your interest and support as I develop this resource for the assembly programming community :D

## Contributions
If you would like to contribute to this repository, please fork the repository, create a new branch, make your changes, and submit a pull request.