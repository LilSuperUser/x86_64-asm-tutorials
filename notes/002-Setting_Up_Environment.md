# Setting Up The Environment For Assembly (ASM) programming
Before we start coding in assembly language, we will need to set up our development environment.
This section will guide you through the process of installing the necessary tools required to write and assemble an asm program.
We will use `NASM Assembler` and `ld linker` throughout this tutorial.

## 1. Installing The NASM Assembler
The **[Netwide Assembler (NASM)](nasm.us)** is a popular assembler for assembling `x86` and `x86_64` assembly code into object code.
Here's how to install it:
```bash
sudo apt-get update -y && sudo apt-get upgrade -y
sudo apt-get install nasm -y
```
After installation, verify the installation using:
```bash
 nasm -v
 ```
## 2. Installing a C compiler And a Linker
To convert your object code into an executable binary, you need a linker. Most linux distributions come with `ld` as part of **binutils** package. You will also need a **C compiler** to link object code with system libraries.
Verify the installation of `gcc` C compiler and `ld` linker using:
```bash
gcc -v
ld -v
```
if it is not installed, install it using :
```bash
sudo apt-get update -y && sudo apt-get upgrade -y
sudo apt-get install gcc -y
sudo apt-get install binutils -y
```

## 3. Installing a Debugger To Debug Assembly Programs
It's really helpful to use a debugger like `gdb` for debugging assembly programs. Here's how you can install it:
```bash
sudo apt-get install gdb -y
```
Verify the installation using:
```bash
gdb -v
```

## 4. Text Editors For Assembly Programming
You can use any text editor/IDE for writing assembly programs. Some of them are:
- SASM
- Vim/Neovim or Emacs with syntax highlighting for assembly.
- Kate
- Visual studio code (with nasm extension)