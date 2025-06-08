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

### 🔧 Tool Versions

```bash
which riscv32-unknown-elf-gdb
```
```bash
riscv32-unknown-elf-gdb --version
```
```bash
file hello.elf
```
```bash
riscv32-unknown-elf-objdump -h hello.elf
riscv32-unknown-elf-readelf -l hello.elf
```
GDB Session
```bash
riscv32-unknown-elf-gdb hello.elf
Disassemble main:

(gdb) disassemble main
(gdb) info symbol 0x10170
(gdb) x/10i 0x10162
(gdb) info symbol 0x100e2
(gdb) x/5i 0x100e2
(gdb) x/s 0x1245c
(gdb) x/1xw 0x10162
(gdb) x/1xw 0x10170
```
![Screenshot from 2025-06-08 15-19-40](https://github.com/user-attachments/assets/a8001d5a-3536-4066-aaed-815fd8d1b3f2)
![Screenshot from 2025-06-08 15-20-00](https://github.com/user-attachments/assets/8fcaa112-6916-4348-aa72-acf03afab777)
![Screenshot from 2025-06-08 15-20-15](https://github.com/user-attachments/assets/f61432c2-c178-422c-bf4c-0704142cbdbd)
![Screenshot from 2025-06-08 15-20-25](https://github.com/user-attachments/assets/35e78012-889b-47db-b65f-337eacc87050)

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
#define UART0 0x10000000
#define uart_tx (*((volatile char *)UART0))

#include <stdint.h>

void uart_putchar(char c) {
    uart_tx = c;
}

void uart_puts(const char *s) {
    while (*s) {
        uart_putchar(*s++);
    }
}

void uart_putnum(uint32_t num) {
    char buf[10];
    int i = 0;
    if (num == 0) {
        uart_putchar('0');
        return;
    }
    while (num > 0 && i < 10) {
        buf[i++] = '0' + (num % 10);
        num /= 10;
    }
    while (i--) {
        uart_putchar(buf[i]);
    }
}

static inline uint32_t add_inline(uint32_t a, uint32_t b) {
    uint32_t result;
    asm volatile ("add %0, %1, %2" : "=r"(result) : "r"(a), "r"(b));
    return result;
}

static inline uint32_t demo_volatile(uint32_t input) {
    uint32_t output;
    asm volatile ("slli %0, %1, 1" : "=r"(output) : "r"(input));
    return output;
}

void _start() {
    uart_puts("=== Inline Assembly: No CSRs ===\n");

    uart_puts("15 + 25 = ");
    uart_putnum(add_inline(15, 25));
    uart_putchar('\n');

    uart_puts("5 << 1 = ");
    uart_putnum(demo_volatile(5));
    uart_putchar('\n');

    while (1) {}
}
```
Compile
```bash
riscv32-unknown-elf-gcc -nostdlib -march=rv32imc -mabi=ilp32 -Wl,-e,_start -o inline_assembly_nocsr.elf inline_assembly.c
```
Generate assembly file
```bash
riscv32-unknown-elf-gcc -S inline_assembly.c
```
View inline assembly in generated code
```bash
echo "=== Generated Assembly with Inline Code ==="
grep -A 5 -B 5 -E "(add|slli|mv)" inline_assembly.s
```
Complete verification
```bash
echo "=== Task 9: Inline Assembly Implementation ==="

echo -e "\n1. Source code created:"
ls -la inline_assembly.c

echo -e "\n2. Compilation:"
riscv32-unknown-elf-gcc -nostdlib -nostartfiles -nodefaultlibs -march=rv32imc -mabi=ilp32 -Wl,-e,_start -o inline_assembly.elf inline_assembly.c \                     
  && echo "✓ Compiled!" \
  || echo "❌ Compilation failed"
                                   
echo -e "\n3. Assembly generation:"                                                                                  
riscv32-unknown-elf-gcc -S inline_assembly.c && echo "✓ Assembly generated!" || echo "❌ Failed to generate assembly"
                                                       
echo -e "\n4. Inline assembly found in generated code:"
grep -A 2 -B 2 -E "add|slli|mv" inline_assembly.s | head -10
```
```bash
file inline_assembly.elf
```
![Screenshot from 2025-06-08 16-00-23](https://github.com/user-attachments/assets/3d1daa73-35f1-42e8-85d9-f74e0a180e67)
![Screenshot from 2025-06-08 16-09-57](https://github.com/user-attachments/assets/d3d1efbf-4fae-485b-882c-ad4cb85bedd9)
![Screenshot from 2025-06-08 16-10-46](https://github.com/user-attachments/assets/8d2511fe-5463-4760-bc93-4907733ed0f1)
![Screenshot from 2025-06-08 16-11-21](https://github.com/user-attachments/assets/9f916e31-c8e5-4925-baed-6aa4a9fa9890)
![Screenshot from 2025-06-08 16-11-55](https://github.com/user-attachments/assets/a1d2fed2-cf79-4068-8d84-1f2607719581)
![Screenshot from 2025-06-08 16-12-01](https://github.com/user-attachments/assets/f5a352d3-0bc8-4b9e-8d16-c13cf8ba1995)
![Screenshot from 2025-06-08 16-12-08](https://github.com/user-attachments/assets/ff463b13-cf4e-4df3-a179-a9a7559b812a)
![Screenshot from 2025-06-08 16-12-36](https://github.com/user-attachments/assets/7e1a70ed-944a-4744-b57b-1f054dc672d2)
![Screenshot from 2025-06-08 16-12-41](https://github.com/user-attachments/assets/175bfd72-1739-465f-aa85-7721bc0e8562)

</details>
<details>
<summary><strong>🔌 Task 10: Memory-Mapped I/O using Volatile vs Non-Volatile</strong></summary>

### 🧪 Objective:
Demonstrate the importance of using `volatile` for memory-mapped I/O in RISC-V bare-metal programming by comparing two versions:
- ✅ `gpio_vol.c` – with `volatile`
- ❌ `gpio_novol.c` – without `volatile`

---

### 🧠 What is `volatile`?

- Tells the compiler **not to optimize** memory accesses.
- Required for memory-mapped I/O since values can change outside the program's control (via hardware).
- Prevents removal or reordering of `*gpio = ...` operations.

---

**gpio_vol.c**
```c
#include <stdint.h>

#define UART0 0x10000000
#define GPIO_ADDR 0x10012000

#define uart_tx (*((volatile char *)UART0))
#define gpio_reg (*((volatile uint32_t *)GPIO_ADDR))

// Send a character to UART
void uart_putchar(char c) {
    uart_tx = c;
}

// Send a string to UART
void uart_puts(const char *s) {
    while (*s) {
        uart_putchar(*s++);
    }
}

// Convert number to decimal and print to UART
void uart_putnum(uint32_t num) {
    char buf[10];
    int i = 0;
    if (num == 0) {
        uart_putchar('0');
        return;
    }
    while (num > 0) {
        buf[i++] = '0' + (num % 10);
        num /= 10;
    }
    while (i--) {
        uart_putchar(buf[i]);
    }
}

// Perform GPIO operations
void gpio_task10_demo() {
    uart_puts("=== Task 10: GPIO Demo ===\n");

    // Write 0x1 to GPIO (set pin high)
    gpio_reg = 0x1;
    uart_puts("GPIO written: 0x1\n");

    // Read back and toggle
    uint32_t current = gpio_reg;
    gpio_reg = ~current;
    uart_puts("GPIO toggled.\n");

    // Set bit 0
    gpio_reg |= (1 << 0);
    uart_puts("Bit 0 set.\n");

    // Clear bit 1
    gpio_reg &= ~(1 << 1);
    uart_puts("Bit 1 cleared.\n");
}

// Entry point (no main)
void _start() {
    gpio_task10_demo();

    while (1) {
        // Infinite loop (bare-metal style)
    }
}
```
gpio_novol.c
```c
#include <stdint.h>

#define UART0 0x10000000
#define GPIO_ADDR 0x10012000

#define uart_tx (*((volatile char *)UART0))
#define gpio_ptr ((uint32_t *)GPIO_ADDR)  // ❌ Not volatile on purpose

void uart_putchar(char c) {
    uart_tx = c;
}

void uart_puts(const char *s) {
    while (*s) {
        uart_putchar(*s++);
    }
}

// This function omits `volatile`, so the compiler may optimize away writes
void toggle_gpio_no_volatile(void) {
    uart_puts("Writing to GPIO without volatile...\n");

    *gpio_ptr = 0x1;  // Set high
    *gpio_ptr = 0x0;  // Set low
    *gpio_ptr = 0x1;  // Set high again — may be optimized away

    uart_puts("Done writing GPIO without volatile.\n");
}

// Bare-metal entry point
void _start() {
    toggle_gpio_no_volatile();

    while (1) {}
}
```
Compilation
```bash
riscv32-unknown-elf-gcc -nostdlib -nostartfiles -nodefaultlibs \
  -march=rv32imc -mabi=ilp32 -Wl,-e,_start -o gpio_vol.elf gpio_vol.c

riscv32-unknown-elf-gcc -nostdlib -nostartfiles -nodefaultlibs \
  -march=rv32imc -mabi=ilp32 -Wl,-e,_start -o gpio_novol.elf gpio_novol.c
```
Assembly Analysis (Optimized with -O2)
```bash
riscv32-unknown-elf-gcc -S -O2 gpio_vol.c -o gpio_vol.s
riscv32-unknown-elf-gcc -S -O2 gpio_novol.c -o gpio_novol.s
```
With volatile (gpio_vol.s) – memory operations preserved:
```bash
105:	sw	a3,0(a4)
115:	lw	a4,0(a3)
119:	sw	a4,0(a3)
128:	lw	a4,0(a3)
132:	sw	a4,0(a3)
```
Without volatile (gpio_novol.s) – some writes optimized away:
```bash
54:	sw	a3,0(a4)         # Only one memory write remains
70:	sw	ra,12(sp)        # Function prologue, not GPIO
```
✅ Memory instructions (sw, lw) are optimized out in the non-volatile version.
![Screenshot from 2025-06-08 16-48-56](https://github.com/user-attachments/assets/aac5ef57-5313-4b2e-a7f3-f31b7a022b00)
![Screenshot from 2025-06-08 16-51-16](https://github.com/user-attachments/assets/02e88936-6568-4228-94fc-0efe39fe71da)
![Screenshot from 2025-06-08 17-18-39](https://github.com/user-attachments/assets/f6e5724d-53b8-47bc-854f-0213c02928a9)
![Screenshot from 2025-06-08 17-18-51](https://github.com/user-attachments/assets/c7c89e4c-68e0-4c6f-98d0-9fac8ddeef75)

</details>

<details>
<summary><strong>📦 Task 11: Custom Linker Script and Bare-Metal Setup</strong></summary>

### 🔧 Objective:
Create and test a custom linker script for a bare-metal RISC-V RV32IMC program. Ensure correct placement of `.text`, `.data`, and `.bss` sections in memory.

---

### 🗂️ Files Used:

| File           | Purpose                                   |
|----------------|-------------------------------------------|
| `minimal.ld`   | Custom linker script                      |
| `test_linker.c`| C file with variables in `.data` and `.bss` |
| `start.S`      | Minimal `_start` assembly to call `main()`|

---

`min_link.ld` Highlights:

- Places `.text` at **0x00000000** (Flash/ROM)
- Places `.data` and `.bss` at **0x10000000** (SRAM)
- Defines `_stack_top` at top of SRAM

```ld
MEMORY {
    FLASH (rx)  : ORIGIN = 0x00000000, LENGTH = 256K
    SRAM  (rwx) : ORIGIN = 0x10000000, LENGTH = 64K
}

SECTIONS {
    .text : { *(.text.start) *(.text*) *(.rodata*) } > FLASH
    .data : { _data_start = .; *(.data*) _data_end = .; } > SRAM
    .bss  : { _bss_start = .; *(.bss*) _bss_end = .; } > SRAM
    _stack_top = ORIGIN(SRAM) + LENGTH(SRAM);
}
```
test_link.c 
```c
uint32_t counter = 0x12345678; // Goes to .data
uint32_t status_flag;          // Goes to .bss

void write_pattern(void) {
    counter = 0xCAFEBABE;
    status_flag = 0x0000DEAD;
}

void main(void) {
    write_pattern();
    while (1) {}
}
```
start.S (Minimal Entry Point):
```asm
.section .text.start
.globl _start

_start:
    la sp, _stack_top
    call main

halt:
    j halt
```
Compile with Custom Linker Script

Compile assembly and C code with custom linker script
```bash
riscv32-unknown-elf-gcc -c start.S -o start.o
riscv32-unknown-elf-gcc -c test_link.c -o test_link.o
```
Link with custom linker script
```bash
riscv32-unknown-elf-ld -T min_link.ld start.o test_link.o -o test_link.elf
```
Complete working build script
```c
#!/bin/bash

echo "=== Task 11: Linker Script Implementation ==="

# Step 1: Compile all sources
echo "1. Compiling with custom linker script..."
riscv32-unknown-elf-gcc -c start.S -o start.o
riscv32-unknown-elf-gcc -c test_link.c -o test_link.o
riscv32-unknown-elf-ld -T min_link.ld start.o test_link.o -o test_link.elf

if [ $? -ne 0 ]; then
    echo "✗ Compilation failed!"
    exit 1
else
    echo "✓ Compilation successful!"
fi

# Step 2: Verify memory layout
echo -e "\n2. Verifying memory layout:"
echo "Text section should be at 0x00000000:"
riscv32-unknown-elf-objdump -h test_link.elf | grep ".text"

echo "Data section should be at 0x10000000:"
riscv32-unknown-elf-objdump -h test_link.elf | grep -E "\.data|\.sdata"

# Step 3: Display symbol table (for verification)
echo -e "\n3. Symbol addresses:"
riscv32-unknown-elf-nm test_link.elf | head -10

echo -e "\n✓ Linker script test completed successfully!"
```
```bash
chmod +x build_link_test.sh
./build_link_test.sh
```
![Screenshot from 2025-06-08 17-36-27](https://github.com/user-attachments/assets/626150ba-c4c6-4983-aa35-247865937e9d)
![Screenshot from 2025-06-08 17-39-26](https://github.com/user-attachments/assets/bfa869cc-8648-4d08-a79e-d9f94f6407db)
![Screenshot from 2025-06-08 18-18-59](https://github.com/user-attachments/assets/e38b17cb-5e31-4f99-a7f4-922b640a2734)
![Screenshot from 2025-06-08 19-09-12](https://github.com/user-attachments/assets/7497fca6-88ce-4e75-9376-3aa57d50c4f2)
![Screenshot from 2025-06-08 19-29-39](https://github.com/user-attachments/assets/d36d37c2-572b-43c2-b515-114ad6b32ab9)

🧠 Flash Memory (0x00000000 - 0x0003FFFF)

This section of memory is meant for storing the program's code permanently. It holds things like the instructions, constant values, and the entry point of the program (like _start). Flash memory is non-volatile, which means its contents stay even when the power is off. In my linker script, I’ve allocated 256KB for it. It's mostly read-only during execution.
⚡ SRAM (0x10000000 - 0x1000FFFF)

SRAM is where the program keeps temporary data while it's running. It stores global variables, the BSS segment (uninitialized data), and also supports the heap and stack. Unlike Flash, SRAM is volatile — meaning it loses everything when power is cut. It allows both reading and writing, and it's very fast. I’ve allocated 64KB for SRAM in the linker script.
📌 Flash vs SRAM – Why They're at Different Addresses

These memory regions are placed at different base addresses for several good reasons:

    Architecture Design: Flash and SRAM often sit on separate memory buses, so their address ranges are distinct.

    Performance: Flash is great for fetching instructions, while SRAM is better for handling data read/write operations quickly.

    Power Saving: In low-power modes, Flash can be turned off while SRAM stays active to preserve temporary data.

    Security: Flash is more secure since it can be made read-only during execution, while SRAM needs to be flexible for runtime changes.
</details>

<details>
<summary><strong>💡 Task 12: LED Blink Using Memory-Mapped GPIO</strong></summary>

### 🎯 Objective
Create a bare-metal program that toggles an LED using memory-mapped I/O, controlled via GPIO register access. This exercise also uses a custom linker script and manual startup code (`_start`).

---

### 🗂️ Files Created

| File              | Description                                 |
|-------------------|---------------------------------------------|
| `task12_led_blink.c` | Blinks LED using GPIO register toggling     |
| `led_start.s`     | Assembly `_start` that sets up stack and calls `main` |
| `led_blink.ld`    | Custom linker script for code/data placement |

---

### 🧠 Memory Mapping

- `GPIO_BASE`: `0x10012000`
- Output Register: `GPIO_BASE + 0x00`
- Direction Register: `GPIO_BASE + 0x04`
- Only GPIO pin 0 is used (toggled repeatedly)

---

### 🔧 Code Overview

**task12_led_blink.c**
```c
#define GPIO_BASE 0x10012000
#define GPIO_OUT  (*(volatile uint32_t *)(GPIO_BASE + 0x00))
#define GPIO_DIR  (*(volatile uint32_t *)(GPIO_BASE + 0x04))

void delay(volatile uint32_t count) {
    while (count--) {
        __asm__ volatile ("nop");
    }
}

void main(void) {
    GPIO_DIR |= (1 << 0);       // Set GPIO pin 0 as output
    while (1) {
        GPIO_OUT ^= (1 << 0);   // Toggle pin 0
        delay(100000);
    }
}
```
led_start.s
```asm
.section .text.start
.globl _start

_start:
    lui  sp, %hi(_stack_top)
    addi sp, sp, %lo(_stack_top)
    call main

hang:
    j hang
```
led_blink.ld
```ld
ENTRY(_start)

MEMORY {
    FLASH (rx)  : ORIGIN = 0x00000000, LENGTH = 256K
    SRAM  (rwx) : ORIGIN = 0x10000000, LENGTH = 64K
}

SECTIONS {
    .text : { *(.text.start) *(.text*) *(.rodata*) } > FLASH
    .data : { _data_start = .; *(.data*) _data_end = .; } > SRAM
    .bss  : { _bss_start = .; *(.bss*) _bss_end = .; } > SRAM
    _stack_top = ORIGIN(SRAM) + LENGTH(SRAM);
}
```
Compile the LED blink program
```bash
riscv32-unknown-elf-gcc -c led_start.s -o led_start.o
riscv32-unknown-elf-gcc -c led_blink.c -o led_blink.o
```
Link with custom linker script
```bash
riscv32-unknown-elf-ld -T led_blink_link.ld led_start.o led_blink.o -o led_blink.elf
```
Complete build script
```c
#!/bin/bash
echo "=== Task 12: LED Blink Implementation ==="

Compile everything
echo "1. Compiling LED blink program..."
riscv32-unknown-elf-gcc -c led_start.s -o led_start.o
riscv32-unknown-elf-gcc -c task12_led_blink.c -o task12_led_blink.o
riscv32-unknown-elf-ld -T led_blink.ld led_start.o task12_led_blink.o -o task12_led_blink.elf

echo "✓ Compilation successful!"

Verify results
echo -e "\n2. Verifying LED blink program:"
file task12_led_blink.elf

echo -e "\n3. Checking memory layout:"
riscv32-unknown-elf-objdump -h task12_led_blink.elf | grep -E "(text|data)"

echo -e "\n4. GPIO register usage in disassembly:"
riscv32-unknown-elf-objdump -d task12_led_blink.elf | grep -A 5 -B 5 "0x10012000"

echo -e "\n✓ LED blink program ready!"

```
```bash
chmod +x build_led_blink.sh
./build_led_blink.sh
```
![Screenshot from 2025-06-08 19-52-04](https://github.com/user-attachments/assets/43e09b54-cfe2-4128-b663-d0ea1d7155de)
![Screenshot from 2025-06-08 19-54-31](https://github.com/user-attachments/assets/6cc618c4-f185-489e-99ca-546f6669a646)
![Screenshot from 2025-06-08 19-58-45](https://github.com/user-attachments/assets/207fd60d-a734-46df-80b9-f8c9b6b13431)
![Screenshot from 2025-06-08 20-05-53](https://github.com/user-attachments/assets/9526dfac-1c02-483e-aac7-6a902a9b2a5a)
![Screenshot from 2025-06-08 20-06-18](https://github.com/user-attachments/assets/14b3fd29-de5b-4994-9bfa-b927ff462be6)

LED Blink Algorithm:

    Initialization: Set GPIO pin 0 as output using direction register
    Main Loop: Infinite loop with LED toggle and delay
    Toggle Operation: XOR output register bit 0 to alternate LED state
    Timing Control: Delay function with configurable count for visible blinking

</details>
<details>
<summary><strong>⏱️ Task 13: Machine Timer Interrupt Using RISC-V CSRs</strong></summary>

📄 File Overview

| File                 | Description                                     |
|----------------------|-------------------------------------------------|
| `timer_interrupt.c`  | Sets up timer and defines C interrupt handler   |
| `start_inter.S`      | Assembly `_start` and trap redirection          |
| `link.ld`            | Custom linker script for `.text`, `.data`, etc. |

---
timer_inter.c
```c
#include <stdint.h>

#define CLINT_BASE      0x02000000
#define MTIMECMP        (*(volatile uint64_t *)(CLINT_BASE + 0x4000))
#define MTIME           (*(volatile uint64_t *)(CLINT_BASE + 0xBFF8))

#define GPIO_BASE       0x10012000
#define GPIO_OUT        (*(volatile uint32_t *)(GPIO_BASE + 0x00))
#define GPIO_DIR        (*(volatile uint32_t *)(GPIO_BASE + 0x04))

#define MIE_MTIE        (1 << 7)
#define MSTATUS_MIE     (1 << 3)

static inline void write_csr(const char *csr, uint32_t value) {
    if (csr == "mstatus") {
        __asm__ volatile("csrw mstatus, %0" :: "r"(value));
    } else if (csr == "mie") {
        __asm__ volatile("csrw mie, %0" :: "r"(value));
    }
}

void timer_init() {
    uint64_t now = MTIME;
    MTIMECMP = now + 500000;  // Schedule next timer interrupt

    write_csr("mie", MIE_MTIE);        // Enable machine timer interrupt
    write_csr("mstatus", MSTATUS_MIE); // Global interrupt enable
}

// Interrupt handler attribute
void __attribute__((interrupt)) machine_timer_handler(void) {
    // Toggle GPIO pin 0
    GPIO_OUT ^= (1 << 0);

    // Schedule next interrupt
    MTIMECMP = MTIME + 500000;
}

// Dummy main loop
void main(void) {
    GPIO_DIR |= (1 << 0); // Make GPIO pin 0 output
    timer_init();

    while (1) {
        // Wait for timer interrupt
    }
}
```
start_inter.s
```asm
.section .text.start
.global _start

_start:
    # Set up stack pointer
    lui sp, %hi(_stack_top)
    addi sp, sp, %lo(_stack_top)
    
    # Initialize trap vector
    la t0, trap_handler
    csrw mtvec, t0
    
    # Call main program
    call main
    
    # Infinite loop (shouldn't reach here)
1:  j 1b

# Simple trap handler (if needed)
trap_handler:
    # Save context
    addi sp, sp, -64
    sw ra, 0(sp)
    sw t0, 4(sp)
    sw t1, 8(sp)
    sw t2, 12(sp)
    sw a0, 16(sp)
    sw a1, 20(sp)
    
    # Call C interrupt handler
    call machine_timer_handler
    
    # Restore context
    lw ra, 0(sp)
    lw t0, 4(sp)
    lw t1, 8(sp)
    lw t2, 12(sp)
    lw a0, 16(sp)
    lw a1, 20(sp)
    addi sp, sp, 64
    
    # Return from interrupt
    mret

.size _start, . - _start
.size trap_handler, . - trap_handler
```
link_inter.ld
```ld
/*
 * Linker Script for Timer Interrupt - RV32IMC
 * Places .text at 0x00000000 (Flash/ROM)
 * Places .data at 0x10000000 (SRAM)
 */

ENTRY(_start)

MEMORY
{
    FLASH (rx)  : ORIGIN = 0x00000000, LENGTH = 256K
    SRAM  (rwx) : ORIGIN = 0x10000000, LENGTH = 64K
}

SECTIONS
{
    /* Text section in Flash at 0x00000000 */
    .text 0x00000000 : {
        *(.text.start)    /* Entry point first */
        *(.text*)         /* All other text */
        *(.rodata*)       /* Read-only data */
    } > FLASH

    /* Data section in SRAM at 0x10000000 */
    .data 0x10000000 : {
        _data_start = .;
        *(.data*)         /* Initialized data */
        _data_end = .;
    } > SRAM

    /* BSS section in SRAM */
    .bss : {
        _bss_start = .;
        *(.bss*)          /* Uninitialized data */
        _bss_end = .;
    } > SRAM

    /* Stack at end of SRAM */
    _stack_top = ORIGIN(SRAM) + LENGTH(SRAM);
}
```
Compile Timer Interrupt Program
Compile the timer interrupt program with CSR support
```bash
riscv32-unknown-elf-gcc -march=rv32imac_zicsr -c start_inter.s -o start_inter.o
riscv32-unknown-elf-gcc -march=rv32imac_zicsr -c timer_inter.c -o timer_inter.o
riscv32-unknown-elf-ld -T link_inter.ld start_inter.o timer_inter.o -o timer_inter.elf
```
complete working build script
build_timer_inter.sh
```c

#!/bin/bash
echo "=== Task 13: Timer Interrupt Implementation ==="

# Compile everything with zicsr extension
echo "1. Compiling timer interrupt program..."
riscv32-unknown-elf-gcc -march=rv32imac_zicsr -c start_inter.s -o start_inter.o
riscv32-unknown-elf-gcc -march=rv32imac_zicsr -c timer_inter.c -o timer_inter.o
riscv32-unknown-elf-ld -T link_inter.ld start_inter.o timer_inter.o -o timer_inter.elf

echo "✓ Compilation successful!"

# Verify results
echo -e "\n2. Verifying timer interrupt program:"
file timer_inter.elf

echo -e "\n3. Checking interrupt-related symbols:"
riscv32-unknown-elf-nm timer_inter.elf | grep -E "(interrupt|timer|handler)"

echo -e "\n4. CSR operations in disassembly:"
riscv32-unknown-elf-objdump -d timer_inter.elf | grep -A 3 -B 1 "csr"

echo -e "\n✓ Timer interrupt program ready!"
```
```bash
chmod +x build_timer_inter.sh
./build_timer_inter.sh
```
![Screenshot from 2025-06-08 20-22-24](https://github.com/user-attachments/assets/c5004876-0146-45da-a2b6-0272d1e3d59a)
![Screenshot from 2025-06-08 20-24-04](https://github.com/user-attachments/assets/e489ef7a-f322-427d-accf-84d5f57cc0db)
![Screenshot from 2025-06-08 20-27-28](https://github.com/user-attachments/assets/793876b8-07bc-43e5-9e17-cd910c9920c7)
![Screenshot from 2025-06-08 20-44-13](https://github.com/user-attachments/assets/87f4f47f-2f84-41a4-8fdf-a3ccad6a6bcb)
![Screenshot from 2025-06-08 20-49-55](https://github.com/user-attachments/assets/ba0d6592-2151-4323-89b0-56d1addddba3)
![Screenshot from 2025-06-08 20-50-27](https://github.com/user-attachments/assets/b52c921a-fdbe-4d12-9690-8a2e0aa5818b)
![Screenshot from 2025-06-08 20-50-45](https://github.com/user-attachments/assets/7a1da1d4-55e5-4e37-9999-625494008a6b)

</details>
<details>
<summary><strong>🔁 Task 14: Circular Queue - With & Without Atomic Operations</strong></summary>

### 🎯 Objective

Implement and compare two circular queue designs:
1. ✅ With atomic operations (`__atomic_exchange_n`)
2. ❌ Without atomic operations (single-core safe only)

---

### 📂 Files

| File                        | Description                                  |
|-----------------------------|----------------------------------------------|
| `task14_queue_atomic.c`     | Thread-safe queue using spinlocks            |
| `task14_queue_non_atomic.c` | Basic queue for single-core use              |
| `start.s`                   | Minimal startup assembly                     |
| `linker.ld`                 | Bare-metal linker script                     |

---

atomic_queue.c
```c
#include <stdint.h>
#include <stdbool.h>

#define QUEUE_SIZE 8

volatile uint32_t queue[QUEUE_SIZE];
volatile uint32_t head = 0;
volatile uint32_t tail = 0;
volatile uint32_t lock = 0;

static inline void lock_acquire(volatile uint32_t *lock) {
    while (__atomic_exchange_n(lock, 1, __ATOMIC_ACQUIRE) != 0) {}
}
static inline void lock_release(volatile uint32_t *lock) {
    __atomic_store_n(lock, 0, __ATOMIC_RELEASE);
}

bool enqueue(uint32_t value) {
    lock_acquire(&lock);
    uint32_t next_tail = (tail + 1) % QUEUE_SIZE;
    if (next_tail == head) {
        lock_release(&lock);
        return false;
    }
    queue[tail] = value;
    tail = next_tail;
    lock_release(&lock);
    return true;
}

bool dequeue(uint32_t *value) {
    lock_acquire(&lock);
    if (head == tail) {
        lock_release(&lock);
        return false;
    }
    *value = queue[head];
    head = (head + 1) % QUEUE_SIZE;
    lock_release(&lock);
    return true;
}

void test_queue(void) {
    uint32_t val;
    enqueue(10); enqueue(20); enqueue(30);
    dequeue(&val); dequeue(&val);
    enqueue(40); enqueue(50);
    while (dequeue(&val)) { (void)val; }
}

void main(void) {
    test_queue();
    while (1);
}
```

no_atomic_queue.c
```c
#include <stdint.h>
#include <stdbool.h>

#define QUEUE_SIZE 8

volatile uint32_t queue[QUEUE_SIZE];
volatile uint32_t head = 0;
volatile uint32_t tail = 0;

bool enqueue(uint32_t value) {
    uint32_t next_tail = (tail + 1) % QUEUE_SIZE;
    if (next_tail == head) return false;
    queue[tail] = value;
    tail = next_tail;
    return true;
}

bool dequeue(uint32_t *value) {
    if (head == tail) return false;
    *value = queue[head];
    head = (head + 1) % QUEUE_SIZE;
    return true;
}

void test_queue(void) {
    uint32_t val;
    enqueue(10); enqueue(20); enqueue(30);
    dequeue(&val); dequeue(&val);
    enqueue(40); enqueue(50);
    while (dequeue(&val)) { (void)val; }
}

void main(void) {
    test_queue();
    while (1);
}
```
atomic_start.s

```asm
.section .text.start
.globl _start

_start:
    lui sp, %hi(_stack_top)
    addi sp, sp, %lo(_stack_top)
    call main

hang:
    j hang
.size _start, . - _start
```
atomic_link.ld
```ld
ENTRY(_start)

MEMORY {
    FLASH (rx)  : ORIGIN = 0x00000000, LENGTH = 256K
    SRAM  (rwx) : ORIGIN = 0x10000000, LENGTH = 64K
}

SECTIONS {
    .text : { *(.text.start) *(.text*) *(.rodata*) } > FLASH
    .data : { _data_start = .; *(.data*) _data_end = .; } > SRAM
    .bss  : { _bss_start = .; *(.bss*) _bss_end = .; } > SRAM
    _stack_top = ORIGIN(SRAM) + LENGTH(SRAM);
}
```
Compile the atomic operations program with RV32IMAC
```bash
riscv32-unknown-elf-gcc -march=rv32imac -c atomic_start.s -o atomic_start.o
riscv32-unknown-elf-gcc -march=rv32imac -c atomic_queue.c -o atomic_queue.o
```
Link with custom linker script
```bash
riscv32-unknown-elf-ld -T atomic_link.ld atomic_start.o atomic_queue.o -o atomic_queue.elf
```
Also compile non-atomic version for comparison
```bash
riscv32-unknown-elf-gcc -march=rv32imc -c no_atomic_queue.c -o no_atomic_queue.o
riscv32-unknown-elf-ld -T atomic_link.ld atomic_start.o no_atomic_queue.o -o no_atomic_queue.elf
```

Complete working build script
```c

#!/bin/bash
echo "=== Task 14: Atomic Extension Demonstration ==="

# Compile with RV32IMAC (includes atomic extension)
echo "1. Compiling with atomic extension (RV32IMAC)..."
riscv32-unknown-elf-gcc -march=rv32imac -c atomic_start.s -o atomic_start.o
riscv32-unknown-elf-gcc -march=rv32imac -c atomic_queue.c -o atomic_queue.o
riscv32-unknown-elf-ld -T atomic.ld atomic_start.o atomic_queue.o -o atomic_queue.elf

echo "2. Compiling without atomic extension (RV32IMC)..."
riscv32-unknown-elf-gcc -march=rv32imc -c no_atomic_queue.c -o no_atomic_queue.o
riscv32-unknown-elf-ld -T atomic_link.ld atomic_start.o no_atomic_queue.o -o no_atomic_queue.elf

echo "✓ Compilation successful!"

# Verify results
echo -e "\n3. Verifying atomic operations program:"
file atomic_queue.elf

echo -e "\n4. Checking for atomic instructions:"
riscv32-unknown-elf-gcc -march=rv32imac -S atomic_queue.c
grep -E "(lr\.w|sc\.w|amoadd|amoswap|amoand|amoor)" atomic_queue.s

echo -e "\n5. Disassembly showing atomic instructions:"
riscv32-unknown-elf-objdump -d atomic_queue.elf | grep -A 2 -B 2 "lr\.w\|sc\.w\|amo"

echo -e "\n✓ Atomic extension demonstration ready!"
```
```bash
chmod +x build_atomic.sh
./build_atomic.sh
```
![Screenshot from 2025-06-08 21-28-41](https://github.com/user-attachments/assets/69a79eb1-3481-4a5f-950a-e7041e39e31b)
![Screenshot from 2025-06-08 21-35-40](https://github.com/user-attachments/assets/4729744d-233c-46c5-99de-4203642d50b2)
![Screenshot from 2025-06-08 21-36-41](https://github.com/user-attachments/assets/31ce243d-e92d-45aa-a211-c7d4b7673b21)
![Screenshot from 2025-06-08 22-06-47](https://github.com/user-attachments/assets/a59fe712-ed44-4520-9921-8e717cb55568)
![Screenshot from 2025-06-08 22-18-34](https://github.com/user-attachments/assets/144835b0-1f08-41b6-888a-6a99dbbae31d)








