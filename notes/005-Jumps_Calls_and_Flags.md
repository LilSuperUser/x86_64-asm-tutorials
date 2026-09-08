# Flags, Jumps and Calls In X86_64 Assembly
In this tutorial we will go over `flags`, `jumps` and `calls`, which are essential for controlling the flow of assembly programs. Understanding these is critical for manipulating program behavior, making decisions, and implementing functions in low level code. 

## 1. Flags For Assembly
Flags are specific bits in `RFLAGS` register that store the result of operations (1 or 0), particularly arithmetic and logical ones. These flags are crucial for decision-making (conditional jumps) and understanding the state of computations.
These flags are set after comparisons `cmp`. The general format of a comparison is `cmp register, register/value`
### 1.1 RFLAGS Register
The `RFLAGS` register consists of several flags, each with a specific purpose. Important flags include:
- `ZF (Zero Flag)` -     Set to 1 if the result of an operation is zero.
- `CF (Carry Flag)` -    Set if there's carry out of the most significant bit during an arithmetic operation.
- `SF (Sign Flag)` -     Set if the result of an operation is negative.
- `OF (Overflow Flag)` - Set if signed overflow occurrs in an arithmetic operation.
### 1.2 Other Common Flags:
- `PF (Parity Flag)` - Set if the number of 1-bits in the result is even.
- `AF (Adjust Flag)` - Set if there's a carry or borrow in binary-coded decimal (BCD) arithmetic.
- `DF (Direction Flag)` - Controls string operations (forward/backward processing).

## 2. Jumps In Assembly
A jump transfers control form one part of a program to another. There are two primary types of jumps
- Unconditional Jumps:
  - `jmp` already covered in last tutorial
- Conditional Jumps:
  - Occur based on the outcome of a **previous comparison** or **arithmetic operation**, relying on the state of the flags register. These jumps enable decision-making in assembly.
  - We would mostly be working with `cmp register, regsiter/value` before using these conditional jumps.
  - Table of conditional jumps is given below:

|Jump Symbol (signed)   | Jump Symbol (unsigned)    | Result of `cmp a, b`      | State of flags                |
|-----------------------|---------------------------|---------------------------|-------------------------------|
|   `je`                |   `je`                    |   a = b                   |   `ZF` = 1                    |
|   `jne`               |   `jne`                   |   a != b                  |   `ZF` = 0                    |
|   `jg`                |   `ja`                    |   a > b                   |   `ZF` = 0 and `SF` = `OF`    |
|   `jge`               |   `jae`                   |   a >= b                  |   `SF` = `OF`                 |
|   `jl`                |   `jb`                    |   a < b                   |   `SF` != `OF`                |
|   `jle`               |   `jbe`                   |   a <= b                  |   `ZF` = 1 or `SF` = `OF`     |
|   `jz`                |   -                       |   a = 0                   |   `ZF` = 1                    |
|   `jnz`               |   -                       |   a != 0                  |   `ZF` = 0                    |
|   `jo`                |   -                       |   Overflow occurred       |   `OF` = 1                    |
|   `jno`               |   -                       |   Overflow didn't occurr  |   `OF` = 0                    |
|   `js`                |   -                       |   Jump if signed          |   `SF` = 1                    |
|   `jns`               |   -                       |   Jump if not signed      |   `SF` = 0                    |

Jumps can also be used as conditionals or loops. 
A combination of conditional and unconditional jumps can be used to implement finite loops. For example:
A simple loop that iterates 5 times:
```asm
.global _start
_start:
	xor rax, rax        ;zeroing out the rax register to be used as index of the 
	                    ;program
	
loop:
	cmp rax, 5          ;compares the value of rax with 5
	je exit             ;if the value is 5, the program jumps to exit
	inc rax             ;adds 1 to rax
	jmp loop            ;jumps back to the start of the loop
	
exit:
	mov rdi, 0          ;an exit status signifying success
	mov rax, 60         ;exit syscall number
	syscall             ;making the syscall
``` 
This is a simple loop that does nothing except iterate 5 times. You can add more code to make it do tasks repetitively(like printing hello world from the previous lesson multiple times on the screen).

Conditional jumps can be used to implement the control flow in a program. Here's a simple assembly program that implements conditional control flow inside a loop and checks if a password is correct using by comparing each character:
```asm
.global _start

;data section
msg db "passwd",0

_start:
	xor rcx, rcx
	mov rdi, [rsp+16]         ;loads argv[1] into rdi from the stack

check:
	mov al, [rdi+rcx]     ;puts a single byte char from the stack into al
	cmp al, [msg+rcx]     ;compares that with one byte of the password
	jne fail              ;if bytes/chars don't match, jumps to fail
	
	cmp al, 0             ;checks for null character
	je success            ;jumps to success if string ends
	
	inc rcx               ;rcx+1 to move to the next char
	jmp check             ;loop back to the start to verify the next char
	
success:
	mov rdi, 0               ;an exit code 0 signifying program succeeded
	mov rax, 60              ;syscall number or exit()
	syscall                  ;making the syscall

fail:
	mov rdi, 1               ;an exit code 1 signifying failure
	mov rax, 60
	syscall
```


## 3. Calls In Assembly
```asm
<function name>:
    [statements under function]
    ret     ; pops the inital value of RIP back into RIP
```
Structure of a function

The `call` does the following:
  - Push `return address` onto the stack (we'll discuss stack later): 
    - `Return address` is the value that `RIP` holds.
  - Moves the control to the function (move the address of function into RIP)

Once the function body is executed successfuly and it encounters `ret`, initial value of `RIP` is popped back into `RIP`, which moves the control back and continues exeuction after `call` instruction.
Let's take a look at this through an example:
```asm
section .text
    global _start
_start:
    ;   suppose we had a few statements before this. Let RIP currently have value x
    ;   let function "func" have address = y
    call func   ; address of this instruction = x, RIP = x + 1
                ; since we encountered call, the address of func(y) will be stored in RIP
                ; initial value of RIP i.e x + 1 would be pushed onto the stack
                ; since the value of RIP changed, the control would move to the current value of RIP

    func:
        [statements]
        ret     ; ret pops the stack back into RIP ==> RIP has value x+1
                ; control moves back to the instruction present after call instruction
```