# Arithmetic And Logical Instructions In x86_64 Assembly
In x86_64 assembly, **Arithmetic** and **logical** instructions allows you to perform operations on data stored in registers or memory. Here's a breakdown of the key instrucion for both the categories:

## Arithmetic Instructions
1. Addition (`add`)
   - Adds the source operand into the destination operand.
   - Example:
    ```asm
        add     rax, rbx        ; rax += rbx
    ```
2. Subtraction (`sub`)
   - Subtracs the source oprand from the destination operand.
   - Example:
    ```asm
        sub     rax, rbx        ; rax -= rbx
    ```
3. Multiplication (`imul` or `mul`)
   - Multiples the destination operand by the source operand.
     - `imul` - For signed multiplication.
     - `mul` - For unsigned multiplication.
   - Example:
    ```asm
        imul    rax, rbx        ; rax *= rbx (signed multiplication)
        mul     rax, 10         ; rax *= rbx (unsigned multiplication)
    ```
4. Division (`idiv` or `div`)
   - Divides the value in the accumulator (RAX) by the source operand and store result (quotient) in accumulator (RAX) and remainder in rdx.
     - `idiv` - For signed division.
     - `div` - For unsigned division.
   - Example:
    ```asm
        div     rbx     ; rax /= rbx and remainder in rdx
    ```
5. Increment (`inc`)
   - Increment (adds 1) to the operand.
   - Example:
   ```asm
        inc     rax     ; rax += 1
    ```
6. Decrement (`dec`)
   - Decrement (subtracts 1) from the operand.
   - Example:
    ```asm
        dec     rax     ; rax -= 1
    ```

## Logical Instructions
1. Bitwise AND (`and`)
   - Performs a bitwise AND between source and destination operands, and store the result in destination operand.
   - Example:
    ```asm
        add     rax, rbx    ; rax = rax & rbx
    ```
2. Bitwise OR (`or`)
   - Performs a bitwise OR between source and destination operands, and store the result in destination operand.
   - Example:
    ```asm
        or      rax, rbx    ; rax = rax | rbx
    ```
3. Bitwise XOR (`xor`)
    - Performs a bitwise XOR between source and destination operands, and store the result in destination operand.
    - Example:
    ```asm
        xor     rax, rbx    ; rax = rax ^ rbx
    ```
4. Bitwise NOT (`not`)
   - Inverts all the bits in the destination operand
   - Example:
    ```asm
        not     rax         ; rax = ~rax
    ```
5. Shift Left (`shl`)
   - Shifts the bits in the destination operand to the left by the specified number of positions.
   - The new value would be `[initial value] * 2^(number of positions)`
   - Example:
    ```asm
        shl     rax, 2      ; rax << 2
    ```
6. Shift Right (`shr`)
   - Shifts the bits in the destination operand to the right by the specified number of positions.
   - The new value woudl be `[initial vale] / 2^(number of positions)` rounded off
   - Example:
    ```asm
        shr     rax, 2      ; rax >> 2
    ```
7. Arithmetic Shift Right (`sar`)
   - Shifts the bits of the destination operand to the right, preserving the sign (used for signed numbers)
   - Example:
    ```asm
        sar     rax, 1      ; signed right shift, preserving the sign bit
    ```
8. Test (`test`)
   - Performs a bitwise AND between source and destination operands, it only updates the flags (according to the result).
   - Doesn't modify the destination operand or source operand.
   - Example:
    ```asm
        test rax, rbx       ; tests the bits of rax and rbx, modifies flags based on the result
    ```
9. Compare (`cmp`)
   - Subtracts the source operand from the destination operand and upates the falgs accordingly (based on the result).
   - Doesn't modify the destination operand or source operand.
   - Example:
    ```asm
        cmp     rax, rbx    ; compares rax with rbx. result of (rax - rbx) sets the flag
    ```