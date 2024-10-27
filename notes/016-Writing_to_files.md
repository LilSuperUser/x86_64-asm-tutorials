# Writing To A File
Previously we learned how to:
- Open a file using specific flags and modes and get a file descriptor (fd) to work with the open file. 
- Close a file descriptor to close the open file.

Now we will look at how to write to a file which has been opened previously. Here's an overview of the whole process:
1. **Opening (or creating) the file** using `sys_open` system call.
2. **Writing data** to the file using `sys_write` system call.
3. **Closing the file** using `sys_close` system call.

Since we have already covered how to open and close a file, we only need to look at how to write to a file.

## `sys_write` system call
This is the same `sys_write` we have been working with till now to print something on our screen. We will only chnage the file descriptor which is loaded in rdi to write to the open file instead of stdout (i.e. 1)

### system call breakdown:
- `rax` holds the system call number (1)
- `rdi` holds the file descriptor:
  - 1 for stdout
  - 2 for stderr
  - fd returned by sys_open to write to opened file
- `rsi` holds the pointer to the string that we want to write
- `rdx` holds the number of bytes we want to write starting from where rsi points to.

## Example of writing to a file
```asm
section .data
    filename db 'example.txt', 0        ; file name with a null terminator
    dataToWrite db 'the data we write to file', 10, 0 

section .bss
    fd resb 8                           ; reserve 8 bytes in memory to store fd

section .text
    global _start

_start:
    ; setting up parameters for sys_open (to get the fd)
    mov rax, 2                          ; load 2(system call number for sys_open) into rax
    mov rdi, filename                   ; first argument (pointer to filename)
    mov rsi, 64 | 2                     ; second argument (flag) using combination of O_CREAT and O_RDWR
    mov rdx, 0o664                      ; third argument (mode) read-write for owner and group and read for others
    syscall                             ; invoking system call, returns fd for example.txt which is now open
    mov [fd], rax                       ; load the value of rax to the memory address of fd (reserved above)

    ; preparing to write to the opened file (using fd)
    mov rsi, dataToWrite                ; load the pointer to the string that is to be written
    call getLen                         ; call sub routine to get length of the string that rsi poitns to (len in rcx)
    mov rax, 1                          ; load 1 (system call number for sys_write) into rax
    mov rdi, [fd]                       ; load the file descriptor into rdi
    mov rdx, rcx                        ; load the length from rcx into rdx
    syscall                             ; invoke the syscall to write to the file descriptor (opened file)

    ; preparing to close the opened file (using fd)
    mov rax, 3                          ; load 3 (system call number for sys_close) into rax
    mov rdi, [fd]                       ; load the value that fd holds into rdi
    syscall                             ; invoking system call, closes the fd loaded in rdi
    
    ; preparing to exit the program
    mov rax, 60                         ; syscall exit
    xor rdi, rdi                        ; exit code = 0
    syscall                             ; invoke the syscall

getLen:                                 ; sub routine to get length of a null terminated string in rcx
    xor rcx, rcx                        ; clear rcx to count length
    .getLenLoop:                        ; loop to get length
        mov al, [rsi + rcx]             ; load the value that rsi + rcx points to
        test al, al                     ; bitwise and between al and al, if result = 0, set zero flag
        jz .getLenDone                  ; if zf is set, jump to .getLenDone (termintae the loop)
        inc rcx                         ; if not, increment rcx and move to the instruction that RIP points to
        jmp .getLenLoop                 ; jump back to .getLenLoop to execute a loop
    .getLenDone:                        ; label to jump to if al = 0
        ret                             ; pop the stack back into rip
```