# Opening & Closing A File
In this tutorial we will learn how to open a file using `sys_open` system call and then close the file using `sys_close` system call.

## 1. More on `sys_open` system call
The `sys_open` system call is used to open a file to either read from it or to write to that file. The `sys_open` takes three arguments and **returns a file descriptor** to the file which was opened. 

### 1.1 `sys_open` system call breakdown
- System call number for sys_open:
  - The system call number for `sys_open` is 2 which is loaded into `rax`

- The three arguments that sys_open takes are:
  - `filename` in `rdi` (pointer to path of the file to open)
    - must be null terminated.
  - `flags` in `rsi` (To control the behavior of the open operation)
  - `mode` in `rdx` (Permissions for file if a new file is created)

- Return value:
  - On **success**, it returns a file descriptor in `rax`.
  - On **error**, it returns -1 in `rax` and sets the `errno` to describe the error.

### 1.2 Flags for `sys_open`
The `flags` argument controls the behavior of the open operation. Common flags include:

#### Table of flags:
|S. NO. | Flag Name     | Flag Value in Hex     | Flag Value in Decimal     | Flag Value in log with base 2 |
|-------|---------------|-----------------------|---------------------------|-------------------------------|
| 1     | `O_RDONLY`    |   0x0000              |   0                       |   N/A                         |
| 2     | `O_WRONLY`    |   0x0001              |   1                       |   0                           |
| 3     | `O_RDWR`      |   0x0002              |   2                       |   1                           |
| 4     | `O_CREAT`     |   0x0040              |   64                      |   6                           |
| 5     | `O_EXCL`      |   0x0080              |   128                     |   7                           |
| 6     | `O_TRUNC`     |   0x0200              |   512                     |   9                           |
| 7     | `O_APPEND`    |   0x0400              |   1024                    |   10                          |
| 8     | `O_NONBLOCK`  |   0x0800              |   2048                    |   11                          |
| 9     | `O_SYNC`      |   0x101000            |   16842752                |                               |
| 10    | `O_DSYNC`     |   0x1000              |   4096                    |   12                          |
| 11    | `O_RSYNC`     |   0x401000            |   4198400                 |                               |
| 12    | `O_DIRECTORY` |   0x20000             |   131072                  |   16                          |
| 13    | `O_NOFOLLOW`  |   0x100000            |   16777216                |                               |
| 14    | `O_CLOEXEC`   |   0x80000             |   524288                  |                               |
| 15    | `O_TMPFILE`   |   0x410000            |   4259840                 |   22                          |
| 16    | `O_PATH`      |   0x2000000           |   2097152                 |   21                          |
| 17    | `O_LARGEFILE` |   0x0100              |   256                     |   8                           |

#### Flags in detail:
1. `O_RDONLY` (0x0000 or 0)
   - Open the file in **read mode**.
   - No writing is allowed.
2. `O_WRONLY` (0x0001 or 1)
   - Open the file in **write mode**.
   - No reading is allowed.
 3. `O_RDWR` (0x0002 or 2)
    - Open the file in in **read and write mode**.
    - Allows both, reading from and writing to the file.
4. `O_CREAT` (0x0040 or 64)
   - Create the file if it doesn't already exists.
   - If the file already exists, this flag doesn't have any effect, unless combined with `O_EXCL`.
5. `O_EXCL` (0x0080 or 128)
   - When combined with `O_CREAT`, it causes `sys_open` to fail if the file already exists.
   - This is useful to prevent accidentally overwriting to an existing file.
6. `O_TRUNC` (0x0200 or 512)
   - If the file **already exists** and is opened for writing, its **length is truncated to zero**.
   - This essentially clears the contents of a file.
7. `O_APPEND` (0x0400 or 1024)
   - Append data to the end of the file
   - This flag ensures that all write operations add data to the end of the file, regardless of the file position.
8. `O_NONBLOCK` (0x0800 or 2048)
   - **Non-Blocking mode**
   - Open the file in non-blocking mode, meaning operations like `read` or `write` will return immediately even if they can't complete, instead of waiting.
9. `O_SYNC` (0x101000)
   - Synchronize writes.
   - Writes are performed synchronously; the system call waits until the data is physically written to the disk. It's slower but ensures that the data is safely stored.
10. `O_DSYNC` (0x1000 or 4096)
    - Similar to `O_SYNC`, but only **file data is syn**, not metadata like access times.
11. `O_RSYNC` (0x401000)
    - File read operations are syn with data writes, ensuring that you read the most recent data after a write.
12. `O_DIRECTORY` (0x20000)
    - If the file is not a directory, then `sys_open` will fail.
    - Useful to ensure we are only working with directories.
13. `O_NOFOLLOW` (0x100000)
    - If the filename is a **symbolic link**, `sys_open` will fail.
    - This prevents following symbolic links and is often used for security purposes.
14. `O_CLOEXEC` (0x80000)
    - Close-on-exec
    - The file descriptor is automatically closed when executing a new program via `execve` or some other related system calls.
15. `O_TMPFILE` (0x410000)
    - Create a `temporary file` that is not linked to any directory.
    - The file is automatically deletd when it is closed.
16. `O_PATH` (0x200000)
    - Open the file but do not access it.
    - This flag allows opening a file descriptor for operations like `fstat` without actually opening the file for reading or writing.
17. `O_LARGEFILE` (0x0100)
    - Allows large files (> 2GB) on systems where this opertions is required.
    - Modern systems usually handle this by default.

### 1.3 Modes for `sys_open`
File modes in `sys_open` specify the permission when creating a file (i.e. when `O_CREAT` is set).
These permission determine the level of access for the file owner, group, and others (other users). File modes are represented as octal numbers (as discussed earlier).

#### File mode values (octal)
1. Owner Permissions:
   - `S_IRUSR` (0o400) Read permission for the owner.
   - `S_IWUSR` (0o200) Write permission for the owner.
   - `S_IXUSR` (0o100) Execute permission for the owner.
2. Group Permission:
   - `S_IRGRP` (0o040) Read permission for the group.
   - `S_IWGRP` (0o020) Write permission for the group.
   - `S_IXGRP` (0o010) Execute permission for the group.
3. Other User's Permission:
   - `S_IROTH` (0o004) Read permission for other users.
   - `S_IWOTH` (0o002) Write permission for others.
   - `S_IXOTH` (0o001) Execute permission for others.
4. Special Permissions:
   - `S_ISUID` (0o4000): Set User Id (SUID)
     - When the permission is set on an executable file, the file will run with the file owner's privileges rather than the expecting user's privileges.
   - `S_ISGID` (0o2000): Set Group Id (SGID)
     - Similar to SUID, this allows the file to run with the file group's priviliges.
   - `S_ISVTX` (0o1000): Sticky bit
     - Typically used on directories, only the owner/root uesr can delte/rename files within the directory.

### 1.4 Combining multiple flags and modes together
Combing flags and modes in system calls like `sys_open` lets you set specific behaviors and permissions for file handling in assembly.

#### Bitwise **or** (`|`)
The **bitwise or** operator (`|`) is commonly used to combine flags and modes. For example:
- combining both `O_CREAT` (0x0040) and `O_WRONLY` (0x0001) results in `O_CREAT | O_WRONLY` (0x41), allowing both the flags to be active.
- combining read-write for the owner, read only for group and others, `0o644` as mode.
- combining read-write-execute for the owner, read for group and neither for others, `0o740` as mode.

#### using shifts and masks for more complex combinations:
`(S_IRUSR | S_IWUSR | S_IXUSR) | (S_IROTH | S_IXOTH)` as would give `read-write-execute` permission for owner and `read-execute` for other users.

it would still work without parentheses, it just increases the readability.

## 2. More on `sys_close` system call:
The `sys_close` system call is used to close a file descriptor (fd), releasing any associated resources. It's a simple and essential part of file handling, particularly after you're done reading from or writing to a file

### 2.1 `sys_close` system call breakdown:
- System call number for sys_close:
  - The system call number for `sys_open` is 3 which is loaded into `rax`
- Takes only one argument (unsigned fd in rdi)
  - `file descriptor` in `rdi` (file descriptor which is to be closed)
- Return value:
  - On **success**, it returns 0 in `rax`
  - On **error**, it returns -1 in `rax` and sets the `errno` to describe the error.

## 3. Example of using `sys_open` and `sys_close`
```asm
section .data
   filename db 'example.txt', 0           ; file name with a null terminator

section .text
   global _start

_start:
   ; setting up parameters for sys_open
   mov rax, 2                             ; load 2(system call number for sys_open) into rax
   mov rdi, filename                      ; first argument (pointer to filename)
   mov rsi, 64 | 2                        ; second argument (flag) using combinatoin of O_CREAT and O_RDWR
   mov rdx, 0o664                         ; third argument (mode) read-write for owner and group and read for others
   syscall                                ; invoking system call, returns fd for example.txt which is now open
   push rax                               ; storing the fd into top of the stack, decrements the stack pointer (rsp)

   ; we have stored fd at the top of the stack
   ; we can use that fd to either read from or write to the open file.

   mov rax, 3                             ; load 3 (system call number for sys_close) into rax
   pop rdi                                ; pop the stack into rdi (load fd into rdi)
   syscall                                ; invoking system call, closes the fd loaded in rdi

   mov rax, 60                            ; syscall exit
   xor rdi, rdi                           ; exit code = 0
   syscall                                ; invoke the syscall
```