# Calculating The Length Of A String In x86_64 Assembly
In this tutorial, we will calculate the length of a given string during run time and store its length in `rcx`. To do so, we will combine:
- Loop
- Conditional jump
- Counter register

```asm
    section .data
        msg     db 'Hello, World', 0

    section .text
        global _start

    _start:
        mov     rsi, msg        ; loading effective address of msg into rsi
        call    _getLen         ; calling the function _getLen

        mov     rax, 1          ; loading 1 (syscall number for sys_write) into rax
        mov     rdi, 1          ; loading 1 (file descriptor for stdout) into rdi
        mov     rsi, rsi        ; loading effective address of msg into rsi
        mov     rdx, rcx        ; loading the length of the given string (number of bytes to write) into rdx
        syscall

        mov     rax, 60         ; loading 60 (syscall number for sys_exit) into rax
        xor     rdi, rdi        ; loading 0 (exit code) into rsi
        syscall                 ; invoking the syscall

    _getLen:
        xor rcx, rcx            ; initializing rcx with 0
        .loopForLen:
            mov     al, [rsi + rcx] ; loading the value of [rsi + rcx] into first 8 bits of RAX
            cmp     al, 0           ; comparing the value in al with 0 (ascii value for null char)
                je      .done       ; if value in al = 0, jump to .done label which stops the loop
            inc     rcx             ; if al != 0, increment rcx
            jmp     .loopForLen     ; jump back to the .loopForLen label to execute a loop

        .done:                      ; label to jump to if al = 0
            ret                     ; pop stack back into RIP
```
## Explanatin Of The Code
We have already covered the part of:
  - Defining a string constant/literal in `data` section.
  - Printing a string literal/constant on the screen.
  - Exitting the function.

What we have to cover is the part which calculates the length of the string during run time and then prints the string using that length.
```asm
    mov     rsi, msg
    call    _getLen

    _getLen:
        .loopForLen:
            mov     al, [rsi + rcx]
            cmp     al, 0
                je  .done
            inc     rcx
            jmp     .loopForLen
        .done:
            ret
```
The logic of _getLen function written above in `C` is:
```C
    char msg[] = "Hello, World";
    int counter = 0;
    int i = 0;
    while (msg[i] != 0){
        counter++;
        i++;
    }
```
Explanation of the asm code:
1. Loading effective address of msg into RSI

2. calling `getLen` function - Control goes into _getLen function.
   - Loading 0 into rcx (for it to count the length of the string)
   - Afer going into `getLen`, it goes into `.loopForLen` label (this serves as a loop)

3. `mov al, [rsi + rcx]`
   - Loading the value that [rsi + rcx] points to, into first 8 bits of RAX (al)

4. `cmp al, 0`
    - Comparing the value in `al` with 0 (ascii value of null char)

5. `je .done`
   - If value in `al` equal to `0`:
     - jump to `.done` label.
   - Else, move forward:
     - `inc rcx` increment the value of rcx
     - `jmp .loopForLen` jump back to `.loopForLen` creating a loop
   - This loop has a termination instruction which is `je .done`

6. Printing the string according to the length of the string
   - Loading 1 into rax and rdi
   - Loading effective address of msg (can be skipped as rsi already had effective address of msg)
   - Loading the value present in RCX into RDX (number of bytes to write)
   - Invoking the syscall