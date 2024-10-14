# Reading User Input And Greeting In X86_64 Assembly
In this tutorial, we will learn how to:
- Take user's name as input fromt the user.
- Store that input into a buffer.
- Output a greeting message with user's name.

```asm
        section .data
        prompt      db 'Enter your name: ', 0
        greeting    db 'Hello, ', 0

    section .bss
        name    resb 32         ; reserving 32 bytes for name given by user

    section .text
        global  _start          ; making the _start label global

    _start:                     ; start of the global label
        call    _putPrompt      ; calling function _putPrompt
        call    _readName       ; calling function _readName
        call    _putGreeting    ; calling function _putGreet
        call    _putName        ; caling function _putName
        jmp     _exit           ; jumping to label _exit

    _putPrompt:                 ; start of _putPrompt function
        mov     rax, 1          ; load syscall number for sys_write (1) into rax
        mov     rdi, 1          ; load file descriptor for stdout (1) into rdi
        mov     rsi, prompt     ; load pointer to source buffer to write from, into rsi
        mov     rdx, 17         ; load number of bytes to write from source buffer, into rdx
        syscall                 ; invoking the syscall
        ret                     ; popping stack back into RIP

    _readName:                  ; start of _readName function
        mov     rax, 0          ; load syscall number for sys_read (0) into rax
        mov     rdi, 0          ; load file descriptor for stdin (0) into rdi
        mov     rsi, name       ; load pointer to memory buffer where the input will be stored, into rsi
        mov     rdx, 32         ; load number of bytes to store in the buffer, into rdx
        syscall                 ; invoking the syscall
        ret                     ; popping stack back into RIP

    _putGreeting:               ; start of _putGreet function
        mov     rax, 1          ; load syscall number for sys_write (1) into rax
        mov     rdi, 1          ; load file descriptor for stdout (1) into rdi
        mov     rsi, greeting   ; load pointer to source buffer to write from, into rsi
        mov     rdx, 7          ; load number of bytes to write from source buffer, into rdx
        syscall                 ; invoking the syscall
        ret                     ; popping stack back into RIP

    _putName:                   ; start of _putName function
        mov     rax, 1          ; load syscall number for sys_write (1) into rax
        mov     rdi, 1          ; load file descriptor for stdout (1) into rdi
        mov     rsi, name       ; load pointer to source buffer to write from, into rsi
        mov     rdx, 32         ; load number of bytes to write from source buffer, into rdx
        syscall                 ; invoking the syscall
        ret                     ; popping stack back into RIP

    _exit:                      ; start of _exit label
        mov     rax, 60         ; load syscall number for sys_ext (60) into rax
        xor     rdi, rdi        ; load 0 into rdi
        syscall                 ; invoking the syscall
```
## Explanation of the code

### Overview of the code
In the above code, we have:
- Four functions (`ret` is present in a function body):
  - `_putPrompt`
  - `_readName`
  - `_putGreeting`
  - `_putName`
- One global label:
  - `_start`
- One local label:
  - `_exit`

1. `section .data` - Defining data section
      - Defining two labels (prompt and greting) for two strings

2. `section .bss` - Defining bss section
   - Reserving 32 bytes for reading input from user and storing it there.

3. `section .text` - Defining text section
   - calling `_putPrompt`, `_readName`, `_putGreeting`, `_putName` and jumping to `_exit`

4. Going over functions and labels defined in the program:
   - `_putPrompt` - Function to print the string `prompt`
   
   - `readName` - Function to read input from the user
     - `mov rax, 0` - Loads value 0 (syscall number for sys_read) into rax
     - `mov rdi, 0` - Loads value 0 (file descriptor for stdin) into rdi
     - `mov rsi, name` - Loads the base memory address which would store the data given by user.
     - `mov rdx, 32` - Loads maximum number of bytes to read into rdx.
     - `syscall` - Invoking the syscall
   
   - `_putGreeting` - Function to print the string `greeting`
   
   - `_putName` - Function to print the name given by user, we load the pointer to buffer into rsi
   
   - `_exit` - Label to exit the program