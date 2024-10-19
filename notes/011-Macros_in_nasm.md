# Macros In Nasm
In this tutorial, we will go over macros in `NASM` (syntax of macros differs by assemblers).
- What are macros?
- How to define macros?

A macro is a single instruction that expands into a pre-defined set of instructions to perform a particular task.
They help reduce the code duplication and improves readibility.

## How to Define a Macro?
syntax to define a macro in nasm:
```asm
    ; macro is defined outside the text section
    %macro <name> <argc>

        <macro_body>

    %endmacro
```
Here:
- `<name>` name of the macro
  - To use a macro, we have to
- `<argc>` number of arguments the macro body will take
  - Arguments are inputs that can be passed into the macro.
  - Within the macro body, these inputs are referenced using:
    - `%1` for the first input
    - `%2` for the second input
    - and so on..
- `<macro_body>` the definition of the macro
  - These are the statements which are replaced by the macro name.

Example of a simple macro:
```asm
    %macro add_numbers 2    ; macro to add two numbers
        mov rax, %1         ; load first argument into rax
        add rax, %2         ; increment rax by second argument
        mov [result], rax   ; load value of rax into memory address of result
    %endmacro

    %macro exit 0           ; macro to exit the program
        mov rax, 60
        mov rdi, 0
        syscall
    %endmacro

    section .data
        num1 dq 5
        num2 dq 60
        result dq 0

    section .text
        global _start

    _start:
        add_numbers num1, num2      ; call the macro with two parameters
        exit                        ; call the macro with 0 parameters
```
## Local Labels in Macros
As we have learned, macros are expanded upon assembly of the source code, into pre-defined code. If that code contains a label, this can potentially cause duplicate label error if the macro is used more than once.

This problem can be solved by using `%%` before label names within a macro. This will make it so that the label is unique every time it es expanded.

## Defining Values With `EQU`
`EQU` is used to define constants for future use.
- It doesn't allocate any space in memory.
- It simply assigns a name to a constant.

### Summary of Key Differences between EQU and DB:
| Feature   | `EQU`                   | `DB`                                |
|-----------|-------------------------|-------------------------------------|
|Definition | Creates a constant name | Allocates memory and initializes it |
|Storage    | Does not allocate memory| Reserves some memory                |
|Usage      | For constants           | For variables                       |
|Example    | `PI EQU 3.14159`        | `array db 1, 2, 0, 5`               |

## Including External Files
A single assembly program can be broken up into multiple files and then those files can be included into a program using `include`. It will load an external files's code and insert it into the position in which it is included upon the assembly.
Macros and EQU (constants) are often defined inside of included files.
`%include "filename.asm"` is how you include an external asm file