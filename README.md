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
<details>
<summary><strong>🧾 Task 5: ABI & Register Cheat-Sheet</strong></summary>

| Register | ABI Name | Usage             |
|----------|----------|-------------------|
| x0       | zero     | Constant zero     |
| x1       | ra       | Return address    |
| x2       | sp       | Stack pointer     |
| x3       | gp       | Global pointer    |
| x4       | tp       | Thread pointer    |
| x5–x7    | t0–t2    | Temporaries       |
| x8–x9    | s0–s1    | Saved registers   |
| x10–x17  | a0–a7    | Function args/ret |
| x18–x27  | s2–s11   | Saved registers   |
| x28–x31  | t3–t6    | Temporaries       |

Calling convention:
- `a0–a7` → Function arguments and return values  
- `s0–s11` → Callee-saved (preserved across function calls)  
- `t0–t6` → Caller-saved (can be overwritten by callees)

</details>
<details>
<summary><strong>🐞 Task 6: GDB Debugging</strong></summary>

```bash
riscv32-unknown-elf-gdb hello.elf
```
Inside GDB:
```bash
(gdb) target sim
(gdb) break main
(gdb) run
(gdb) info registers
(gdb) disassemble
```
Actions:

    Set a breakpoint at main
    Run the program
    View register values
    Step through the program

</details>
<details>
<summary><strong>🖥️ Task 7: Emulator Run using QEMU</strong></summary>

Step 1: Install Required Packages (if not done)

Just in case:
```bash
sudo apt update
sudo apt install build-essential device-tree-compiler libglib2.0-dev libpixman-1-dev git \
libexpat-dev libgmp-dev libmpc-dev libmpfr-dev libz-dev python3 gawk bison flex texinfo \
libtool autoconf automake
```
Step 2: Clone and Build OpenSBI
```bash
cd ~/riscv-projects/week1
git clone https://github.com/riscv-software-src/opensbi.git
cd opensbi
```
Build for 32-bit:
```bash
make PLATFORM=generic CROSS_COMPILE=riscv32-unknown-elf-
```
Check if hello.elf exists
In your terminal:
```bash
find ~/riscv-projects/ -name hello.elf
```
If this shows a path like:
```bash
/home/harshini123/riscv-projects/week1/hello.elf
```
Run QEMU like this (replace the path with yours):
```bash
qemu-system-riscv32 -nographic \
  -machine virt \
  -bios ~/riscv-projects/week1/opensbi/build/platform/generic/firmware/fw_dynamic.elf \
  -kernel ~/riscv-projects/week1/hello.elf
```
Hello c program
```c
// hello.c
volatile char *uart = (volatile char *)0x10000000;
void _start() {
    const char *str = "Hello, RISC-V!\n";
    while (*str) *uart = *str++;
    while (1);  // hang
}
```
Linker code
```ld
MEMORY
{
  ROM (rx) : ORIGIN = 0x80200000, LENGTH = 512K
  RAM (rw) : ORIGIN = 0x84000000, LENGTH = 128K
}

SECTIONS
{
  . = ORIGIN(ROM);

  .text : {
    *(.text*)
  } > ROM

  .rodata : {
    *(.rodata*)
  } > ROM

  .data : {
    *(.data*)
  } > RAM

  .bss : {
    *(.bss*)
    *(COMMON)
  } > RAM
}
```
Compile using
```bash
riscv32-unknown-elf-gcc -T linker.ld -nostartfiles -o hello.elf hello.c
```
![uart hello riscv](https://github.com/user-attachments/assets/91c298b5-c193-46dd-b9d2-d4fb8adfe84e)
</details>
<details>
<summary><strong>🚀 Task 8: GCC Optimization (-O0 vs -O2)</strong></summary>

**Commands Used:**
```bash
riscv32-unknown-elf-gcc -S -O0 hello.c -o hello_O0.s
riscv32-unknown-elf-gcc -S -O2 hello.c -o hello_O2.s
```
![hello no opt](https://github.com/user-attachments/assets/1300c154-9c3b-4cfd-a56e-4a98f7ed4c99)
![hello o2](https://github.com/user-attachments/assets/2aa7a47b-7f86-4f78-8f6a-268c57a50765)

Comparison:

    -O0 (no optimization): includes full function call overhead, redundant instructions.

    -O2 (optimized): inlines functions, removes dead code, reuses registers efficiently.

  </details>
  <details>
<summary><strong>⚙️ Task 9: Inline Assembly – Reading Cycle Counter</strong></summary>

**C Code with Inline Assembly:**
```c





