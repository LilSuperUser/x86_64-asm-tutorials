# Reading From A File
Previously we learned how to:
- Open a file using specific flags and modes and get a file descriptor (fd) to work with the open file.
- Write to the opened file.
- Close a file descriptor to close the open file.

Now we will look at how to read from a file which has been opened previously. Here's an overview of the whole process:
1. **Opening the file** using `sys_open` system call.
2. **Reading data** from the file using `sys_read` system call.
3. **Closing the file** using `sys_close` system call.

Since we have already covered how to open and close a file, we only need to look at how to read from a file.

## `sys_read` system call
This is the same `sys_read` we have been working with till now to read from stdin. We will only change the file descriptor which is loaded in rdi to read from the file instead of stdin (i.e. 0)

### system call breakdown:
- `rax` Holds the system call number (0)
- `rdi` Holds the file descriptor:
  - `0` To read from stdin.
  - fd Returned from `sys_open` read from opened file.
- `rsi` Pointer to the buffer where the data will be stored.
- `rdx` Number of Bytes to read from the given fd.
- It returns the number of bytes read in `rax`.

## Example of reading from a file
```asm
section .data
    filename db 'example.txt', 0        ; null terminated filename string

section .bss
    fd  resd 1                          ; reserve 4 bytes for file descriptor
    buffer resq 16                      ; reserve 16 * 8 = 128 bytes of memory to read from file

section .text
    global _start

_start:
    ; opening a file to read from it
    mov rax, 2                          ; load 2 (system call number for sys_open)
    mov rdi, filename                   ; load the pointer to the file name into rdi
    mov rsi, 0                          ; load 0 into rdi (flag to open the file in readonly)
    mov rdx, 0o0                        ; load 0 into rdx (modes aren't useful if we don't create a new file)
    syscall                             ; invoking the syscall, returns a file descriptor

    mov [fd], eax                       ; load the lower 4 B of rax into [fd]

    ; reading from the file and storing the data into bufer ()
    mov rax, 0                          ; load 0 (system call number for sys_read)
    mov edi, [fd]                       ; load the file descriptor into lowe 4 B of rdi
    mov rsi, buffer                     ; load the pointer to the buffer (where we store data) into rsi
    mov rdx, 128                        ; load 128 (max number of B to read) into rdx
    syscall                             ; invoking the system call, returns the number of bytes read into rax

    push rax                            ; push the number of bytes read onto the stack (decrements rsp)

    ; writing the data which was read to stdout
    mov rax, 1                          ; load 1 into rax (system call for sys_write)
    mov rdi, 1                          ; load 1 into rdi (fd for stdout)
    mov rsi, buffer                     ; load the pointer to the buffer (to write from) into rsi
    pop rdx                             ; pop the number of bytes read from file, into rdx
    syscall                             ; invoking the system call, writes the data read to stdout

    ; closing the file
    mov rax, 3                          ; load 3 into rax (system call for sys_close)
    mov edi, [fd]                       ; load the fd into lower 4 B of rdi
    syscall                             ; invoking the systemcall

    ; exitting the program
    mov eax, 60                         ; load 60 (sys call number for sys_exit) into eax
    xor edi, edi                        ; clear edi (exit code)
    syscall                             ; invoking the syscall
```