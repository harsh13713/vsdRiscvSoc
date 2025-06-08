# 📘 RISC-V Bare-Metal Programming – Weekly Progress

This repository contains all task outputs for 34 weeks of RISC-V programming practice.  
👇 Click on a week below to view its tasks.

---

## 📅 Weekly Index

- [▶️ Week 1: Toolchain, Hello World & Basics](#week-1)
<!-- Add more weeks later like this:
- [▶️ Week 2: Assembly Loops and Stack](#week-2)
- [▶️ Week 3: Interrupts & GPIO](#week-3)
-->

---

Sure! Here’s the full complete markdown block including all the C and bash code parts fully wrapped with proper syntax, ready to paste directly:

## 🧠 Week 1: Toolchain, Hello World & Basics

# 📘 RISC-V Bare-Metal Programming – Week 1 Tasks

<details>
<summary><strong>🔧 Task 1: Toolchain Setup & Sanity Check</strong></summary>

**Steps Performed:**

- Extracted the toolchain using:
  ```bash
  tar -xzf riscv-toolchain-rv32imac-x86_64-ubuntu.tar.gz

    Added following to ~/.bashrc:

export PATH=$HOME/riscv/bin:$PATH

Verified installation:

    riscv32-unknown-elf-gcc --version
    riscv32-unknown-elf-gdb --version
    riscv32-unknown-elf-objdump --version
![Screenshot from 2025-06-08 12-09-36](https://github.com/user-attachments/assets/5036ddaa-d933-48cf-9892-0145ad21bedb)
</details>

<details>
<summary><strong>👋 Task 2: Compile “Hello, RISC-V”</strong></summary>

✅ Simple C code:

```c
#include <stdio.h>

int main() {
    printf("Hello, RISC-V!\n");
    return 0;
}
```
✅ Compiled with:
```bash
riscv32-unknown-elf-gcc -march=rv32imc -mabi=ilp32 -o hello.elf hello.c
```
✅ Confirmed with:
```bash
file hello.elf
```

![hello c](https://github.com/user-attachments/assets/2216f6c8-7f7a-48d2-aa4c-e80e4e411746)
![elf chk](https://github.com/user-attachments/assets/ac686476-21ee-49ab-8677-0a30158fb180)

</details> 
<details>
<summary><strong>📜 Task 3: Generate .s File & Analyze Prologue</strong></summary>

**Command Used:**
```bash
riscv32-unknown-elf-gcc -S -O0 hello.c -o hello.s
```

![cat hello s](https://github.com/user-attachments/assets/3a917c07-91df-4dca-829b-eab827b3ddc1)
 Understanding Prologue & Epilogue
🔸 Prologue (function entry):
```bash
addi    sp,sp,-16        # Reserve 16 bytes on the stack
sw      ra,12(sp)        # Save return address (ra) at offset 12
```
➡️ This sets up the stack frame and saves the return address so the function can safely return later.
🔸 Epilogue (function exit):
```bash
lw      ra,12(sp)        # Restore return address
addi    sp,sp,16         # Restore the stack pointer
ret                      # Return to the caller
```
➡️ This restores the state before the function was called and jumps back using the saved return address.
</details>

<details>
<summary><strong>🔍 Task 4: Hex Dump & Disassembly</strong></summary>

**Commands Used:**
```bash
riscv32-unknown-elf-objdump -d hello.elf > hello.dump
riscv32-unknown-elf-objcopy -O ihex hello.elf hello.hex
```
You can inspect it with:
```bash
cat hello.dump
```
![hello dump](https://github.com/user-attachments/assets/8978e143-06a4-4dc8-9555-e6a2c9691470)
Create an Intel HEX file
```bash
riscv32-unknown-elf-objcopy -O ihex hello.elf hello.hex
```
You can view it with:
```bash
cat hello.hex
```
![hello hex](https://github.com/user-attachments/assets/746e9c3d-c53d-424e-a11d-921a5a6ed692)
</details>


