# Introduction To Files
In this tutorial we will cover these topics which are essential to work with files in assembly.
- Introduction to files
- File permissions
- Special permissions
- File descriptors
- File operations and permissions in system calls   

## 1. Introduction to Files
In modern Operating systems, files are one of the most fundamental ways to store and manage data. Whether you're storing text, binaries, or configuration data, everything is saved and retrieved through files. Understanding how files work at low level is essential for reading from and writing to them efficiently in assembly.

### 1.1 What is a file?
A file is a **collection of data** stored on a disk or some other storage device, identified by a name `filename` and is accessible through various file operations. Files can contain different types of data such as text, executable code, images, etc. However, it all comes down to simple 1s and 0s.

There are different types of files such as:
- **Regular Files**: These includes text files, binary files or any other similar type of data.
- **Directories**: A special type of file that contains pointers to other files.
- **Symbolic Links**: These are files that point to another file or directory.
- **Special Files**: These represent hardware devices like `/dev/sda` for storage devices or `/dev/null` to discard unwanted output.

## 2. File Permissions
Every file on a Unix like operating system, such as Linux, has associated permissions that define what actions (read, write and execute) individual users or users of a group can perform on that file. These permissions control file access and are critical to over all security of the system.

File modes specify the permission for files. Essentially, they specify who is allowed to read, write and/or execute the file. These modes are stored as a `three-digit octal value (base 8)`.

|mode value in octal    |  Read `r` (4) | Write `w` (2) | Execute  `x` (1)  |
|-----------------------|---------------|---------------|-------------------|
|   0                   | Not permitted | Not permitted | Not permitted     |
|   1                   | Not permitted | Not permitted | `Permitted`       |
|   2                   | Not permitted | `permitted`   | Not permitted     |
|   3                   | Not permitted | `permitted`   | `Permitted`       |
|   4                   | `Permitted`   | Not permitted | Not permitted     |
|   5                   | `Permitted`   | Not permitted | `Permitted`       |
|   6                   | `Permitted`   | `Permitted`   | Not permitted     |
|   7                   | `Permitted`   | `Permitted`   | `Permitted`       |

### 2.1 Understanding file permissions structure
File permissions in linux are typically represented using a string of ten characters. When you list a file's details using `ls -l`. For example:
```bash
-rw-r--r--
```
This string can be broken down as follows:
| Character(s)  | Meaning of that character(s)              |
|---------------|-------------------------------------------|
| `-`           | Type of file (`-` for a regular file)     |
| `rw-`         | Owner's permission (`read` and `write`)   |
| `r--`         | Group's permission (`read` only)          |
| `r--`         | Other user's permission (`read` only)     |

- The first character indicate the file type:
  - `-` indicates a regular file
  - `d` indicates a directory
  - `l` indiates a symbolic link

- The **next three characters** represent the permission for the **file owner**
  - `rw-` indicates that the owner has `read` and `write` permission but no `execute` permission.

- The **following three characters** represent the permission for the **the group**
  - `r--` indicates that the owner only has `read` permission.

- The **last three characters** represent the permission for **other users**
  - `r--` indicates that other users only have `read` permission.

#### Permission Breakdown
| Symbol    | Meaning   | Octal Value   | Description                                                           |
|-----------|-----------|---------------|-----------------------------------------------------------------------|
| `r`       | Read      | 4             | Allows reading the content of a file or list files of a directory.    |
| `w`       | Write     | 2             | Allows modifying the contents of a file or add/rem files in a dir.    |
| `x`       | Execute   | 1             | Allows executing a file (if its a script/program) or entering a dir.  |

Each set of these three characters (rwx) represent the combination of these permissions. The absence of a permission is represented by `-`.

These permissions can also be represented numerically using octal notation. In octal notation, the permission bits are represented using octal values of the permission (preesnt in table given above)
- `rwxr-xr--` would be represented as `754` in octal (7 for owner, 5 for group and 4 for other users)
  - `rwx` for owner ==> 4 + 2 + 1 = 7 for owner.
  - `r-x` for group ==> 4 + 0 + 1 = 5 for group.
  - `r--` for other users ==> 4 + 0 + 0 = 4 for other users.

- `rw-r--r--` would be represented as `644` in octal (6 for owner, 4 for group and 4 for other users)
  - `rw-` for owner ==> 4 + 2 + 0 = 6 for owner.
  - `r--` for group ==> 4 + 0 + 0 = 4 for group.
  - `r--` for other usres ==> 4 + 0 + 0 =  for other users.

### 2.2 Special permissions
File permissions in linux are set with four octal values. The least 3 significant octal values are for:
- file owner's permission
- group's permission
- other users's permission

The most significant octal value is reserved for special permissions. Usually we don't need to use these.

1. `setuid` (Set User ID):
   - When applied to an executable file, this permission allows users to run file with file owner's priviliges.
   - Represented as a `s` in the owner's execute position.
     - eg: `rwsr-xr-x`
   - `setuid` bit in octal is `4000`

2. `setgid` (Set Group ID):
   - When applied to an executable file, it runs with the group owner's priviliges. In directories, new files inherit the group of the directory.
   - Represented as a `s` int he group's execute position.
     - eg: `rwxr-sr-x`
   - `setgid` bit in octal is `2000`

3. `sticky bit`:
   - Used mainly on directories.
   - When set, only the **owner of file(s)**, **owner of directory** or the **root user** can delete or rename the file(s) within that directory, regardless of other user's or group's permissions.
   - Represented as a `t` in the other user's execute permission.
     - eg: `rwxrwxrwt`
   - `sticky bit` in octal is `1000`

## 3. File Descriptors
In most Unix-like operating systems (such as Linux), a **file descriptor** (FD) is a simple integer that uniquely identifies an open file (or other I/O resources like sockets, pipes, etc.) within a process. They are used by the OS to manage I/O operations and represent every open file or I/O stream.

### 3.1 What is a file descriptor?
A file descriptor is an **integer value** that a process receives when it opens a file or I/O stream.. Each process gets its own set of file descriptors. This integer serves as a reference to an open file or I/O stream, so operations like reading from a file or writing to a file are through these file descriptors.

### 3.2 Standard file descriptors
The **first three** file descriptors are reserved for **Standard Input/Output (I/O)**:
- `0` for `stdin`: Used for standard input (from the keyboard).
- `1` for `stdout`: Used for standard output (to the terminal or console).
- `2` for `stderr`: Used for standard error (error messages) (to the terminal or console).

Some common examples:
- `read (0, buf, size)` reads from stdin into buf
- `write (1, buf, size)` writes to stdout from buf
- `write(2, buf, size)` writes to stderr from buf (useful for error reporting and debugging)

### 3.3 How file descriptors work in the kernel?
When you open a file usign `open` syscall, the kernel performs the following steps:
- Find the file on the disk.
- Loads its metadata into the memory.
- Returns a file descriptor that acts as a handle to the file for your program.

The file descriptor returned by `open` syscall is a small integer that refers to the open file.

### 3.4 File descriptors for other I/O resrources
File descriptors are not just for regular files present on disk or I/O streams. They can represent many types of I/O including:
- `Pipes` for inter-process communication `IPC`.
- `Sockets` for network communication.
- `Devices` files representing devices like `/dev/null`, hardware devices, or pseudo terminals.

### 3.5 File descriptor table
Each process in Linux has its own **file descriptor table**, which is a table of pointers to entries in the **system-wide file table**.
- The file descriptor table contains the file descriptors for all open files in a process.
- Each file descriptor points to an entry in the **system-wide file table** that contains metadata like the current file offset, the mode (read/write), etc.
- The system-wide file table points to the **inode table**, which contains information about the actual file on disk.

### 3.6 File descriptor inheritance
When a new process is spawned (eg via `fork()`), it inherits the file descriptors of the parent process. This allows both parent and child processes to share access to open files. However, some file descriptors might be marked with the `close-on-exec` flag, meaning they are closed when executing a new program via `exec()`.

## 4. System calls we will use to work with files
To read from a file and write to a file, we would need to work with `sys_read`, `sys_write`, `sys_open` and `sys_close` system calls.

Let's take a look at the x86_64 linux syscall table.

| System Call | `rax` syscall number  | `rdi` 1st argument    | `rsi` 2nd argument  | `rdx` 3rd argument  |
|-------------|-----------------------|-----------------------|---------------------|---------------------|
| sys_read    | 0                     | unsigned int `fd`     | char *buf           | size_t count        |
| sys_write   | 1                     | unsigned int `fd`     | char *buf           | size_t count        |
| sys_open    | 2                     | const char  *filename`| int `flags`         | int `mode`          |
| sys_close   | 3                     | unsigned int `fd`     | N/A                 | N/A                 |
