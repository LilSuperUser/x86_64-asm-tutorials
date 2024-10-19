# Sub-routine To Print An Integer
So far we have learned how to print a string, get user input, print a single digit onto the screen.
Now, In this tutorial, we will learn to write a sub routine which should print an integer like (482) onto the screen.

## Source Code
```asm
section .data
    nline           db 10               ; reserve one byte for newline char

section .bss
    intBuffer       resb 30             ; reserve 30 bytes for intBuffer

section .text
    global _start

_start:
    mov rax, 1232                       ; loading 1232 into rax
    call printRAX                       ; calling function printRAX

    mov rax, 60                         ; loading 60 into rax
    xor rdi, rdi                        ; loading 0 into rdi
    syscall                             ; invoking the syscall

printRAX:
    mov r15, intBuffer                  ; loading 0 into r15
    mov ebx, 10                         ; loading 10 into rbx (same as writing mov rbx, 10)

    .loopExtractDigits:
        xor rdx, rdx                    ; loading 0 into rdx because it will hold the remainder after div
        div rbx                         ; dividing rax by rbx, rdx holds the remainder
        add rdx, 48                     ; increment the value in rdx by 48
        mov [r15], dl                   ; moving lower byte of rdx into address pointed by r15
        inc r15                         ; increment the value in r15 by 1
        test rax, rax                   ; perform a bitwise operation between rax and rax, set zf iff rax = 0
        jz .printDigits                 ; if zf flag is set, jump to .printDigits
        jmp .loopExtractDigits          ; else jump back to .loopExtractDigits to create a loop

    ; after last increment of r15, it points to a null byte

    .printDigits:
        dec r15                         ; decrement r15 by 1
        
        mov rax, 1                      ; sys_write: syscall number is 1
        mov rdi, 1                      ; file_descriptor: stdout
        mov rsi, r15                    ; loading value of r15 into rsi
        mov rdx, 1                      ; loading 1 into rdx (1 byte to write)
        syscall                         ; invoking the syscall

        cmp r15, intBuffer              ; compare the value in r15 with address of intBuffer
        jge .printDigits                ; until r15 >= intBuffer, create a loop
        jmp .done                       ; else, jump to .done label

    .done:
        mov rax, 1                      ; sys_write to print a newline char
        mov rdi, 1
        mov rsi, nline
        mov rdx, 1
        syscall
        ret                             ; popping the stack back into rip
```

### Logic to Print an Integer
- Divide the integer by 10 repeatedly to extract the digits in the form of a remainder.
- Converting each digit to its ascii equivalent and storing it in a buffer.
- printing those ascii equivalents in reverse order (since division gives us digits in reverse order).

### Explanation of The Code
1. Section .data
   - Defining a label (nline) to one B in memory which stores 10 (ascii val of new line char)

2. Section .bss
   - Reserve 30 bytes under name `intBuffer` to store ascii value of each digit of the number to print.

3. Section .text (from _start label)
    - Loading 123 in `rax` (integer to print)
    - Calling function `printRAX` to print integer present in `rax`
    - Exitting the program using **sys_exit** syscall

#### Explanation of the function `printRAX` (line by line)
1. `mov r15, intBuffer` Load effective address (base address) of reserved memory into r15 register.
   - We are using `r15` here because it is not associated with any syscall and hence, won't be overwritten during a syscall.
   - You can use anyother register but make sure that it is nto associated to syscalls and doesn't get overwritten during the same.

2. `mov ebx, 10` Load 10 into ebx register.
   - Same as writing `mov rbx, 10` changes made into 32 bit version of a register would reflect in the whole register unlike with 16 or 8 bit version of a register.
   - We are loading 10 into rbx because we will be using this to divide and extract each digit from the integer.

3. `.loopExtractDigits:` Defines a label for extracting digits.

4. `xor rdx, rdx` Clearing rdx/loading 0 into rdx
   - We are clearing rdx because it will hold the remainder after we use `div` instruction.

5. `div rbx` Divides the value present in rax by the value in rbx.
   - After division :
     - quotient is returned back in `rax`
     - remainder is stored in `rdx` 
   - Since here, we are dividing by the value is `rbx` i.e. 10
   - It would give us the last digit of the integer (remainder) and remove that last digit from integer
   - After first div, rax holds 12 and rdx has the last digit i.e. 3
   - After second div, rax holds 1 and rdx has the last digit i.e. 2
   - After third div, rax holds 0 and rdx has the last digit i.e. 1

6. `add rdx, 48` After each division, add 48 (ascii val of 0) into rdx
   - Doing this, the digit present in rdx is replaced by the ascii value of that digit.

7. `mov [r15], dl` Load lower byte of rdx register (dl) into memory pointed by r15.
   - r15 holds the base address of the reserved memory.
   - Hence, we can say that r15 points to the base address of the reserved memory.
   - with the 7th instruction we are overwriting that memory address by the value present in the lower byte of the rdx.

8. `inc r15` Increment the value in r15 by 1
   - Incrementing the value in r15 by 1 so that the pointer to base address of reserved memory can move up by 1B.
   - We are doing this so that we can write a new value to a new memory address with each iteration.

9. `test rax, rax` Perform a bitwise and operation on rax
   - We could've also wrote `cmp rax, 0` but it woudl have taken more size and hence slower.
   - The result of 9th instruction would only be zero if and only if rax holds 0.
   - If the result is zero, `zf` flag is set.
   - rax would hold 0 if all the digits from integer have been extracted.

10. `jz .printDigits` If the `zf` flag is set, jump to .printDigits label to print the values present in intBuffer in **reverse order**.

11. `jmp .loopExtarctDigit` Jump to `.loopExtractDigit` label if the zf flag is not set, this sets up a loop.

12. `.printDigits` Defines a label to print the values present in the reserved buffer but in reverse order.
    - We are printing the values present in the reserved buffer in a reverse order because we extraced digits in reverse order (using div)
    - Since we extraced digits in reverse order, we also wrote the ascii value of those digits into reserved memory in reverse order.
    - To print the same given integer, we must print the values in reserved memory in reverse order.

13. `dec r15` Decrement the value in r15 by 1.
    - If you notice, in the label of `.loopExtractDigit` we are incrementing r15 first and then checking if the value that is written to reserved memory is 0 or not.
    - Hence, r15 would be pointing to a null byte after last iteration of  `.loopExtractDigit`
    - Therefore, we are decrementing the value in r15 at the start so that it points to the last value that was stored in the reserved memory.

14. `mov rax, 1` Loading 1 (syscall number for sys_write) into rax.

15. `mov rdi, 1` Loading 1 (file descripter for stdout) into rdi.

16. `mov rsi, r15` Loading value preseint in r15 into rsi.
    - Since r15 is a pointer to the value that is to be printed.
    - We can say that we are loading the pointer of the value that is to be printed in rsi via r15.

17. `mov rdx, 1` Loadin 1 (number of bytes to write to stdout starting from poitner loaded in rsi)

18. `syscall` Invoking the syscall

19. `cmp r15, intBuffer` Comparing the value present in r15 with the base addr of reserved memory.

20. `jge .printDigits` As long as the value in r15 is greater than or equal to intBuffer, jump back to .printDigits to create a loop
    - We are decrementing the value in r15 with each iteration so that it can point to a new value after each iteration.
    - Since we are doing that, we must first print and then stop when r15 points to the base addr of reserved memory (where we stored our first val)

21. `jmp .done` Once value in r15 < base address of reserved memory, jump to `.done` label.
    - When that happens, it means we have printed all the value we stored in the reserved memory.

22. `.done:` Defines a label to which we jump after printing all the values we stored in buffer(reserved memory).
    - Prints the new line char using `sys_write` syscall.
    - Pop the stack back into RIP to continute the execution.