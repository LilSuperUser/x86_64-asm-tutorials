# Introduction To Computer Architecture
In this tutorial, we will go over the basics of computer architecture, starting from fundamental concepts like:
- Processors (CPU - Central Processing Unit)
- Memory
- Buses
- Endianness

It is essential to understand these concepts before diving into assembly (asm) language programming.

## 1. What Is Computer Architecture?
Computer architecture refers to the structure and behaviour of a computer system.
It defines the system's capabilities, performance, and interaction between hardware components.
The three key components are:
- CPU (central processing unit)
- Memory (RAM and Storage)
- Input/Output (I/O)

### 1.1 CPU (Central Processing Unit)
The **CPU** or **processor** is the brain of the computer. It performs all the communications and executes instructions.
Key components of a processor are:
- **Control Unit (CU)** -> Directs the operations of a processor.
- **Arithmetic Logic Unit (ALU)** -> Hanles arithmetic and logical operations.
- **Registers** -> Small, fast, expensive storage locations in the CPU used for immediate data access.

### 1.2 Memory
**Memory** in a system is categorized into two types:
- **Primary Memory (RAM)**:
  - Temporarily stores data and programs that are being used.
  - Really fast to access (slower than registers and faster than storage devices)
  - It is volatile in nature.
  - All the data that is loaded into the memory will be lost once the system is turned off.
  - `when we say "memory" we often refer to this primary memory`
- **Secondary Memory (Storage)**:
  - Stores data permanently.
  - Really slow to access.
  - It is non-volatile in nature.
  - All the data persists even when the system is turned off.
  - All the data that is currently being used is loaded into RAM from secondary storage.
  - examples: HDD, SSD, USB Drives, etc.

### 1.3 Buses
A **bus** is a communication system that transfers data between components inside or outside a computer.
There are three main types of buses:
- **Data Bus** -> Carries actual data, that is to be written or loaded.
- **Address Bus** -> Carries information on where the data is to be written or loaded from.
- **Control Bus** -> Carries signals related to control operatoins (example: read or write signals)

### 1.4 How Do Buses Work?
1. **Address Bus**
   - The CPU places the memory address it wants to access on the address bus (target memory address). This bus carries the address of the memory location where the data is to be read from or written to.
2. **Control Bus**
   - The CPU sends a control signal over the control bus to indicate whether it wants to perform a read or write operation.
     - Read -> The control bus will instruct the memory bus to send data back to the CPU.
     - Write -> The control bus will instruct the memory bus to accept data from the CPU.
3. **Data Bus**
   - Carries the actual data that is to be written to the memory or read from the memory.
     - Read operation -> The value on the target memory address is placed on the data bus which is then read by CPU.
     - Write operation -> The value that is to be written on the target memory address is placed on data bus.


## 2. What Is Endianness?
**Endianness** refers to the order in which the Bytes (B or 8 bits (b)) are stored or transmitted in memory.
It is crucial for understanding how data is represented in assembly and how the CPU accesses multi-byte data types.
There are two main types of endianness:
1. **Big-Endian**
   - The least significant byte (the "small end") is stored at the highest address.
   - The most significant byte (the "big end") is stored at the smallest memory address.
   - For example: if we have a 32 bit int `0x12345678` it would be stored in the memory as:

        | memory address | value stored at the address |
        |----------------|-----------------------------|
        |0x00            | 12                          |
        |0x01            | 34                          |
        |0x02            | 45                          |
        |0x03            | 56                          |
        |0x04            | 78                          |

2. **Little-Endian**
   - The least significatnt byte (the "small end") is stored at the smallest memory address.
   - The most significant byte (the "big end") is stored at the highest memory address.
   - For example: if we have a 32 bit int `0x12345678` it would be stored in the memory as:

        | memory address | value stored at the address |
        |----------------|-----------------------------|
        |0x00            | 78                          |
        |0x01            | 56                          |
        |0x02            | 34                          |
        |0x03            | 12                          |

### 2.1 Implications Of Endianness
- Data Interpretation:
  - The endianness affects how data is interpreted when read from memory.
  - A program reading data in one endianness may misinterpret the data if it was written in some other endianness.
- Network Protocols:
    - Many network protocols use big-endian format (AKA `Network Byte Order`) to ensure consistency across different systems.
    - When transmitting data over networks, systems must convert their native endianness to big-endian to communicate correctly.
  - Cross-Platform Compatibility:
    - Endianness can cause issues in cross-platform software development.
    - If one system uses little-endian and another uses big-endian, then data structures must be converted accordingly when shared between systems.

### 2.2 Checking Endianness In Programming
**using C**
``` C
#include <stdio.h>
int main(){
  unsigned int x = 1;
  char *c = (char*)&x;

  if (*c)
    printf("Little endian\n");
  else
    printf("Big endian\n");

  return 0;
}
```

**using Python**
```Python
import sys
if sys.byteorder == "little":
  print("Little endian")
else:
  print("Big endian")
```

## 3. Memory Hierarchy
The **memory hierarchy** referts to the different level of memory storage in a computer, each with varying speeds and capacity to store data.

### 1. **Registers**
   - **Function**:
     - Stores immediate data and instructions that the CPU is currently processing.
     - They provide the quickest access to data needed for arithmetic, logical operations, control flow and data manipulation.
   - **Location** -> Within the CPU.
   - **Speed** -> Fastest type of memory.
   - **Size** -> Very small (typically a few B).
   - **Categorization** -> Primary

### 2. **Cache Memory**
   - **Function**:
      - Stores frequently accessed data and instrutions to reduce time to access data from RAM.
      - Further organized into:
        - **L1 Cache** -> Smallest and fastest. built into the CPU core.
        - **L2 Cache** -> Larger and slightly slower than L1, can be on CPU chip or nearby.
        - **L3 Cache** -> Even larger and slower, shared among multiple CPU cores.
   - **Location** -> Close to the CPU (usually on the same chip or nearby chip)
   - **Speed** -> Faster than RAM but slower than Registers.
   - **Size** -> Small (typically a few KB to a few MB).
   - **Categorization** -> Primary.

### 3. **Main Memory (RAM)**
   - **Function**:
     - Data, instructions that are curretn*ly being used are loaded into RAM.
   - **Location** -> Exteral to the CPU.
   - **Speed** -> Slower than cache but faster than secondary storage.
   - **size** -> Typicaly a few GB.
   - **Categorization** -> Primary.

### 4. **Secondary Storage**
   - **Function**:
     - Used to retain data permanently.
     - Used for storing applications, files, and other data that do not require immediate access.
  - **Location** -> External to the CPU.
  - **Speed** -> Slower than RAM
  - **size** -> Typically a few hundred GB to several TB
  - **Categorization** -> Secondary.

### 5. **Tertiary Storage**
   - **Function**:
     - Used for backup and archiving data.
   - **Location** -> External to primary storage devices.
   - **Speed** -> Slowest type of memory.
   - **Size** -> Can be very large (few hundred GB to several TB)
   - **Categorization** -> Secondary.

## 4. What Is Instruction Set Architecture (ISA)?
**Instruction Set Architecture (ISA)** refers to the part of a computer architecture that defines the set of instructions that a processor can execute. It serves as an interface between software and hardware, enabling the development of programs that can interact with the CPU.

### 4.1 Key Components Of Instruction Set Architecture (ISA)
#### 4.1.1 **Instruction Set**
  - The collection of instructions that a CPU can execute, including operations for arithmetic, logic, control flow, data movement, and more.
  - Instructions are often categorized into these types:
    - **Data Movement Instructions**: Moves data between registers, memory, and I/O devices. (eg: `LOAD`, `STORE`)
    - **Arithmetic Instructions**: Performs mathematical operations. (eg: `ADD`, `SUB`, `MUL`, `DIV`)
    - **Logical Instructions**: Executes bitwise operations. (eg: `AND`, `OR`, `NOT`, `XOR`)
    - **Control Flow Instructions**: Alter the flow of executions of instructions. (eg: `JUMP`, `CALL`, `RETURN`)
    - **Input/Output Instructions**: Manages data transfer to and from peripheral devices.
#### 4.1.2 **Addressing Modes**
  - The techniques used to specify operands (data) for instructions. Common addressing modes include:
    - **Immediate Addressing**: The operand is specified directly in the instruction.
    - **Direct Addressing**: The address of the operand is given directly.
    - **Indirect Addressing**: The address of the operand is speicifed by a register or memory location.
    - **Indexed Addressing**: The operand's address is generated by adding a constant value to a register value.
    - **Register Addressing**: The operand is located in a specific register.
#### 4.1.3 **Registers**
  - Small, fast storage locations within the CPU used to hold data temporarily during instruction execution.
  - Different types of registers include:
    - **General-Purpse Registers**: Used for various data operations.(eg: `R0`, `R1`, `R2`, etc)
    - **Special-Purpse Registers**: Used for specific functions.(eg: `RSP`, `RBP`, etc)
#### 4.1.4 **Instruction Formats**
  - The structure of an instruction, typically includes these fields:
    - **OpCode**: Specifies the operation that is to be performed.
    - **Operands**: Specifies the data to be processed, which may include registers or immediate values or memory addresses.
    - **Addressing Mode**: Indicates how to interpret the operands.
#### 4.1.5 **Control Signals**
  - The signals generated by the control unit (CU) of the CPU to manage the execution of the instructions, including reading from or writing to memory, activating the arithmetic logic unit(ALU), and controlling flow.

### 4.2 Types Of Instruction Set Architectures
#### 4.2.1 **CISC (Complex Instruction Set Architecture)**
  - Contains a large number of instructions, including complex instructions that can perform multiple operations in a single instruction.
  - example: `x86 architecture (Intel, AMD)`
#### 4.2.2 **RISC (Reduced Instruction Set Architecture)**
  - Contains a smaller number of instructions, each designed to execute in a single cycle.
  - Emphasizes a load/store architecture where memory access is limited to specific load and store instructions.
  - example: `ARM architecture`, `MIPS architecture`
#### 4.2.3 **VLIW (Very Long Instruction Word)**
  - Uses long instruction words that bundles multiple operations together.
  - The compiler is responsible for scheduling instructions to be executed together, in parallel.
  - example: `Itanium architecture`
#### 4.2.4 **EPIC (Explicitly Parallel Instruction Computing)**
  - Designed to exploit instruction-level parallelism by explicitly specifying parallel operation in the instructin set.
  - example: `Intel's Itanium`

### 4.3 Importance of ISA
- **Compatibility**: Determines software compatibility; programs written for one ISA may not run on another without recompilation or emulation.
- **Performance**: Influences the performance characteristic of the CPU and the overall system.
- **Programming**: Guides compiler design and programming language development, as it defines how high level instructions translate into machine code.

## 5. Interrupts and Exceptions
Interrupts and exceptions are mechanisms that are used by a processor to handle the events that disrupts the normal execution flow of a program. Both allows the CPU to respond to events like hardware signals or errors. But they differ in their causes and handling. Here's a detailed overview:

### 5.1 Interrupts
Interrupts are signal sent by hardwares or softwares to indicate that an event needs immediate attention. The CPU temporarily pauses it's current task to handle the interrupt and then resumes the task afterward.
### 5.1.1 Types Of Interrupts
#### 1. **Hardware Interrupts**
  - Generated by external devices (like a keyboard, mouse or network card) to signal the CPU when attention is needed.
  - For example, pressing a key on the keyboard sends an interrupt to the CPU, allowing it to process a keystroke.
  - Common uses are:
    - I/O operations
    - Timers
    - System events
#### 2. **Software Interrupts**
  - Triggered by executing a special instruction in the program, often for system calls or special functions.
  - An example is a system call to request service from the OS, such as reading or writing files.

### 5.1.2 Interrupt Handling Process
When an interrupt occurrs, the CPU performs the following steps:
  1. **Save the current state**:
     - The CPU saves the states (registers and program counter) of the currently running task.
  2. **Interrupt service routine (ISR)**:
     - The CPU jumps to a special function called an `interrupt service routine (ISR)` or `interrupt handler`, which is responsible for handlingthe interrupt.
  3. **Handle the event**:
     - The ISR processes the event, such as reading data from an I/O device or handling a timer event.
  4. **Restore the state**:
     - After the ISR is complete, the CPU restores the saved state back and resumes the interrupted task.

### 5.1.3 Characteristics Of Interrupts:
- **Asynchronous**: Can happen at any time during the execution of a program.
- **Prioritized**: Interrupts can have different priority levels, meaning higher-priority interrupts can interrupt lower-priority ones.

### 5.2 Exceptions
**Exceptions** are the events that occur as a direct result of the execution of instructions, often due to an error or special condition that the CPU or OS needs to handle.
### 5.2.1 Types Of Exceptions:
1. Traps:
   - Generated intentionally by software, typically through a system call or other instruction designed to invoke the OS.
   - For example: A program might use a trap to request a service from the OS, like memroy allocation or file handling.
2. Faults:
   - Occur due to errors in instruction exection, such as accessing an invalid memory address(segmentation fault) or dividing by zero.
   - The CPU attempts to correct the issue. if it can, the program may resume. if not, the program is terminated, often with an error message.
 3. Aborts:
    - Generated by severe hardware or system-level errors, such as memory parity errors or hardware malfunctions.
    - Typically unrecoverable, leading to program termination or system failure.

### 5.2.2 Exception Handling Process
When an exception occurrs, the follwing steps are typically performed:
1. **Save the current state**: the CPU saves the state of the program (registers, program counter, etc).
2. **Exception handler**: The CPU jumps to an exception handler, which deals with the specific exception.
3. **Handle the exception**: The handler may try to resolve the issue.
4. **Resume or terminate**: Depending on the exception type, the program may either resume execution or be terminated.

### 5.2.3 Characteristics Of Exceptions:
- **Synchronous**: Exceptions are directly tied to the execution of specific instructions, meaning they occurr in response to particular operations.
- **Non-maskable**: Unlike some interrupts, exceptions usually cannot be ignored by the CPU an must be handled immediately.

### 5.2.4 Key Differences Between Interrupts and Exceptions:
  | Feature       | Interrupt                             | Exception                                                    |
  |---------------|---------------------------------------|--------------------------------------------------------------|
  |Source         | External (hardware or software)       | Internal (CPU instruction execution)                         |
  |Timing         | Asynchronous(can happen anytime)      | Synchronous (occurrs during instruction execution)           |
  |Cause          | I/O events, hardware signals          | Instruction errors, system calls                             |
  |Handling       | Uses interrupt service routines (ISR) | Uses exception handlers                                      |
  |Priority       | Often prioritized                     | Usually handled immediately                                  |
  |Recoverability | Often recoverable (resume program)    | Some exceptions are recoverable, others terminate the program|
