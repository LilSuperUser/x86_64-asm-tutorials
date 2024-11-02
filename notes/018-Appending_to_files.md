# Appending To A File
So far, we have learned:
- How to open a file
- How to write to a file
- How to read from a file
- How to close an open file

Now we will learn how to append to a file. Appending to a file means, adding data to the end of the file, without overwriting the pre existing data on the file. This is done by using `O_APPEND` (0x0400) flag.

## Example of appending to a file
```asm
section .data
    filename db 'example.txt', 0
    dataToAppend db 'This is the data which will be appended', 10, 0

section .bss
    fd resd 1               ; reserve 4 B for file descriptor

section .text
    global _start

_start:
    ; opening the file to append to the file
    mov eax, 2
    mov rdi, filename           ; load the pointer to filename in rdi
    mov rsi, 64 | 1024 | 2      ; open the file using combination of O_CREAT, O_APPEND, O_RDWR
    mov rdx, 0o664              ; create the file with read-write permission for owner and group and read for others
    syscall
    mov [fd], eax               ; load lower 4 B into reserved 4 bytes of fd

    ; appending the data to the file using sys_write
    mov edi, [fd]               ; load 4 B of fd into lower 4 B of rdi
    mov rsi, dataToAppend       ; load pointer to dataToAppend into rsi
    call getLen                 ; call getLen function to fetch length into rcx
    mov eax, 1                  ; load 1 for write into eax
    mov rdx, rcx                ; load the value of rcx into rdx
    syscall                     ; invoking the syscall

    ; closing the file
    mov eax, 3                  ; load 3 (syscall number for sys_close) into eax
    mov edi, [fd]               ; load fd to close into edi
    syscall                     ; invoking the syscall (close the file descriptor)

    ; exit the program
    mov eax, 60
    xor edi, edi
    syscall

getLen:                         ; sub routine to load len into rcx
    xor rcx, rcx
    .getLenLoop:
        mov al, [rsi + rcx]
        test al, al
        jz .getLenDone
        inc rcx
        jmp .getLenLoop
    .getLenDone:
        ret
```
