# Hello World In X86_64 Assembly (ASM)
Before we start writing our `Hello World` program in asm, we need to look at system calls in linux.
Assembly program often uses system calls to interact witht he OS. System calls are how we communicate with the kernel to perform tasks like writing to a file, reading input from user, or even exitting the program.

## Linux x86_64 Syscall Structure
| Instruction       | Syscall #     | Return value  | arg0  | arg1  | arg2 | arg3 | arg4 | arg5 |
|-------------------|---------------|---------------|-------|-------|------|------|------|------|
|SYSCALL            | RAX           | RAX           | RDI   | RSI   | RDX  | R10  | R8   | R9   |

### Structure
- `RAX` - To store system call number and return value (if any).
- `RDI` - To store 0th argument to pass while **invoking a syscall**.
- `RSI` - To store 1st argument to pass while **invoking a syscall**.
- `RDX` - To store 2dn argument to pass while **invoking a syscall**.
- `R10` - To store 3rd argument to pass while **invoking a syscall**.
- `R8`  - To store 4th argument to pass while **invoking a syscall**.
- `R9`  - To store 5th argument to pass while **invoking a syscall**.

Throughout your journey with x86_64 assembly, you would need to refer to [Linux System Call Table for x86_64](https://blog.rchapman.org/posts/Linux_System_Call_Table_for_x86_64/).

## Hello World In Assembly (NASM) - x86_64 Linux
```asm
    section .data
        msg db 'Hello, World`, 0      ; Define a null-terminated string where each char takes one B

    section .text
        global _start

    _start:
        ; to print hello world
        mov rax, 1      ; syscall number for sys_write (1)
        mov rdi, 1      ; file descriptor 1 for stdout
        mov rsi, msg    ; address of the string to output
        mov rdx, 13     ; length of the string(13 char) Number of bytes to output from address given in rsi
        syscall         ; invoking the syscall

        ; to exit the program
        mov rax, 60     ; syscall number for sys_exit (60)
        xor rdi, rdi    ; exit code 0. could have wrote "mov rdi, 0"
        syscall         ; invoking the syscall

        ; clearly we don't have any bss section
```
### Explanation Of The Above Code (Referring By Line Number)
1. `section .data` - Defines the **data section**
    - **data section** is where we define static data, such as strings or constants, that we will use in the program.

2. `msg db 'Hello, World', 0` - Defines a string literal/constant.
   - `db`: Stands for `Define Byte`, used to allocate a sequence of bytes or a single Byte in memory.
   - `msg`: The label for the string. We refer to this label when we want to access the string. (name for the base memory address allocated by `db`).
   - `'Hello, World', 0`: The string with a null terminator (`0` in ascii and `'\0'` in char format) at the end.

3. `section .text` - Defines the **text section**
   - **text section** is where we write code (instructions that the CPU executes).

4. `global _start` - Declares the label `_start` as global.
   - `global` - Makes the `_start` label visible to the linker. without this, the label `_start` would only be visible within the assembly file (local scope), and the linker wouldn't know where to begin the execution.
   - `_start` - Is conventionally used as the starting point for an assembly program, though you could use any other label.

5. `_start:` - Definition of the `_start:` label.
   - A `label` defines a location in the code and are used to name specific points in the code.
   - The label itself **doesnt' execute anything** but the code following the label does.
   - Labels helps to jump to a block of code after certain conditions or just reference that block of code.
   - Any code palced **directly after the label, with indentation**, is associated with that label.
   - Code that is not **indented** or that **has another label**, marks the beginning of a new block of code.

6. `mov rax, 1` - Moves value 1 (system call number for `sys_write`) into rax register.

7. `mov rdi, 1` - Moves value 1 (file descriptor for `stdout`) into rdi register.
    - 0 for `stdin (standard input)`
    - 1 for `stdout (standard output)`
    - 2 for `stderr (standard error)`

8. `mov rsi, msg` - Moves the base address of the data we want to write (msg in this case) into rsi register.

9. `mov rdx, 13` - Moves the value 13 (Number of Bytes we want to write from address stored in rsi) into rdx register.
    - since we used "db", each char of the string takes one Byte. Hence, we move 13(length of the string) into rdx.

10. `syscall` - This invokes the system call, with arguments given by us to certain register(s).

11. `mov rax, 60` - Moves value 1 (system call number for `sys_exit`) into rax register.

12. `xor rdi, rdi` - Moves value (xor of value in rdi with that same value i.e. 0 `exit code`) into rdi register.
    - alternative is: `mov rdi, 0`

13. `syscall` - This invokes the system call, with arguments given by us to certain register(s).

### Assembling And Linking The Program

To assemble the assembly file `hello.asm` into object file `hello.o` for x86_64 linux
```bash
nasm -f elf64 hello.asm -o hello.o
```

To link the object file `hello.o` into an executable file `hello`
```bash
ld hello.o -o hello
```
Running this executable file `hello` would print "Hello, World" onto the screen.

## Data Definition Directives

### To Allocate Memroy And Initialize It With Some Data (In Data Section)
1. `db` (Define Byte):
   - Used to define 1 Byte (8 bits) of data in memory.
   - Example:
    ```asm 
    my_byte db 0x41
     ```
2. `dw` (Define Word):
   - Used to define 2 Bytes (16 bits) of data in memory.
   - Example:
    ```asm
    my_word dw 0x1234
    ```
3. `dd` (Define Double Word):
   - Used to define 4 Bytes (32 bits) of data in memory.
   - Example:
   ```asm
   my_double_word dd 0x12345678
   ```
4. `dq` (Define Quad Word):
   - Used to define 8 Bytes (64 bits) of data in memory.
   - Example:
    ```asm
    my_quad_word dq 0x123456789ABCDEF0
    ```
5. `dt` (Define Ten Bytes):
    - Used to define 10 Bytes (80 bits) of data in memory.
    - Example:
    ```asm
    my_ten_bytes dt 0x123456789ABCDEF1234567890AB
    ```

### To Allocate Memory For Buffer (In BSS Section)
1. `resb` (Reserve Byte):
   - Used to reserve 1 Byte (8 bits) per entry in memory, without initializing it.
   - Example:
    ```asm
    buffer resb 5      ; reserves 1 * 5 Bytes for a buffer
    ```
2. `resw` (Reserve Word):
   - Used to reserve 2 Bytes (16 bits) per entry in memory, without initializing it.
   - Example:
    ```asm
    buffer resw 5       ; reserves 2 * 5 Bytes for a buffer
    ```
3. `resd` (Reserves Double Word):
   - Used to reservse 4 Bytes (32 bits) per entry in memory, without initializing it.
   - Example:
    ```asm
    buffer resd 5       ; reserves 4 * 5 Bytes for a buffer
    ```
4. `resq` (Reserves Quad Word):
   - Used to reserve 8 Bytes (64 bits) per entry in memory, without initializing it.
   - Example:
   ```asm
   buffer resq 5        ; reserves 8 * 5 bytes for a buffer
   ```
5. `rest` (Reserves Ten Bytes):
   - Used to reserve 10 Bytes (80 bits) per entry in memory, without initializing it.
   - Example:
    ```asm
    buffer rest 5       ; reserves 10 * 5 bytes for a buffer
    ```
## Hello World In Assembly But With Labels
Before we start working with labels, we need to go over `jmp` instruction in asm
- `jmp` Instruction in assembly is a **control flow** instruction used to make unconditional jump to another part of the program. It alters the flow of execution by directly changing the **Instruction Pointer** `RIP`
```asm
    section .data
        msg db 'Hello, World', 0

    section .text
        global _start

    _start:
        jmp printHelloWorld    ; jumping to _printHelloWorld label
        jmp _exit               ; jumping to _exit label

    printHelloWorld:
        mov rax, 1
        mov rdi, 1
        mov rsi, msg
        mov rdx, 13
        syscall

    exit:                      ; defining _exit label
        mov rax, 60
        xor rdi, rdi
        syscall
```