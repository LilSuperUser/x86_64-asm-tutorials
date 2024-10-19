# Displaying A Digit In x86_64 Assembly
In this tutorial we will learn how to print a signle digit onto the screen. Before we dive into this tutorial it is necessary that you have a proper understanding about `ASCII` as well as an [ASCII table](https://www.asciitable.com/) to refer to.

```asm
    setion .data
        digit   db 0, 10            ; defining an array of two bytes, each byte with 0 and 10 respectively

    section .text
        global  _start              ; declaring a global label _start

    _start:                         ; start of the global _start label
        mov     rax, 5              ; loading the value 5 into rax
        call    printRAXDigit      ; calling the function _printRAXDigit
        jmp     exit               ; Jumping to _exit label

    printRAXDigit:
        add     rax, 48             ; incrementing the value of rax register by 48
        mov     [digit], al         ; loading value of al (8 bit version of RAX) into [digit]

        mov     rax, 1              ; loading 1 (syscall number for sys_write) into rax
        mov     rdi, 1              ; loading 1 (file descriptor for stdout) into rax
        mov     rsi, digit          ; loading effective address (pointer) of digit into rsi
        mov     rdx, 2              ; loading 2 (number of Bytes to write from loaded effected address (lea)) into rdx
        syscall                     ; invoking the syscall
        ret                         ; popping stack back into RIP

    exit:                          ; start of the 
        mov     rax, 60             ; loading 60 (syscall number for sys_exit) into rax
        xor     rdi, rdi            ; loading 0 (exit code) into rdi
        syscall                     ; invoking the syscall
```
## Explanation of the code given above

### in `data section`:
- `digit db 0, 10`
  - Defining an array {0, 10}, each element of this array takes one Byte.
  - 0 in the array would be replaced by the digit we want to print.
  - 10 is the ASCII value of newline char (\n)

### in `text section`:
In this section we are writing code to print whatever single digit RAX register currently holds.
- `mov rax 5`
  - Loads the value 5 into RAX (we will print this 5 stored in RAX)

- `call _printRAXDigit`
  - Calling the function `_printRAXDigit` which prints the digit present in RAX
  
- `jmp _exit`
  - Jump to `_exit` label

- `_printRAXDigit`
  - `ADD rax, 48` Increment the value in RAX by 48 (which is ascii value for 0)
    - After incrementing the value in RAX by 48, the resulting value in RAX would be the ASCII value of the digit initially present in RAX
  - `mov [digit], al` Overwriting initial value at effective address of digit (i.e. 0) by first 8 bits (al) of RAX
  - `mov rax, 1` Loading syscall number for sys_write.
  - `mov rdi, 1` Loading file descriptor for stdout.
  - `mov rsi, digit` Loading effective address (lea) of digit into RSI
  - `mov rdx, 2` Loading the number of bytes to write from value present in RSI
    - We are loading 2 into RDX, because we want to write 2 bytes to stdout
        1. The digit that was initially present in RAX.
        2. The new line char.
  - `syscall` Invoking the syscall
  - `ret` Popping stack back into RIP

  - `_exit` label to exit the program