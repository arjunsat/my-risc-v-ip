# RISC-V Codespace Bring-Up — From Basics 

This repository is a **beginner-friendly explanation** of what I did during **Task-1** and *why each step matters*.  
It explains the **key terms**, **tools**, **files**, and the **full execution flow** in simple language.

> **Scope:** Environment + RISC-V reference run + VSDFPGA lab firmware build  
> **Not included:** FPGA flashing / board bring-up (intentionally later)

---

## What I Did

1. Opened the **vsd-riscv2** GitHub Codespace (stable prebuilt environment).
2. Verified important tools exist (`riscv64-unknown-elf-gcc`, `spike`, `pk`).
3. Compiled and ran a small RISC-V program (`sum1ton.c`) in a simulator.
4. Cloned **vsdfpga_labs** inside the same Codespace.
5. Built the firmware HEX file (`riscv_logo.bram.hex`) used later for BRAM initialization.
6. Avoided FPGA build/flash tools (as required by Task-1).

---

# Part A — Basics You Must Know First

## 1) What is RISC-V?

**RISC-V** is an open Instruction Set Architecture (ISA).

- **ISA** = the “language” a CPU understands (add, load, store, branch, etc.)
- Different CPU implementations can follow the same ISA and still run the same code

So the pipeline is:

**C program → compiled → RISC-V machine code → executed by a RISC-V CPU**

Since we don’t use a physical RISC-V CPU here, we run the program on a simulator.

---

## 2) What is a Toolchain?

A **toolchain** is a group of tools that converts source code into a runnable program.

For this flow we used:

- **Compiler:** `riscv64-unknown-elf-gcc`
- **Simulator:** `spike`
- **Loader/runtime:** `pk` (proxy kernel)

---

## 3) What is Cross-Compilation?

My Codespace CPU is **x86_64**, but my target program is **RISC-V**.

So I need a compiler that runs on x86_64 but generates RISC-V output:

✅ `riscv64-unknown-elf-gcc` does exactly that.

### Meaning of the name

- `riscv64` → generates RISC-V 64-bit binaries  
- `unknown-elf` → “bare-metal style”; outputs an ELF binary without assuming Linux/Windows runtime  
- `gcc` → GNU C Compiler  

---

## 4) What is an ELF file?

**ELF** = Executable and Linkable Format  
It’s the standard binary format on Linux-like systems.

An ELF typically contains:

- machine code instructions (`.text`)
- constants/strings (`.rodata`)
- initialized data (`.data`)
- entry point (where execution starts)
- optional symbol/debug info

---

## 5) What is a “.o file”?

Usually:

- `.o` = **object file** (compiled but not linked)

But in our case we used:


riscv64-unknown-elf-gcc -o sum1ton.o sum1ton.c
## Why `sum1ton.o` Runs Like an Executable (Even With `.o`)

Because we did **not** use `-c`, the tool actually:

- compiled ✅
- linked ✅
- produced a complete executable **ELF** ✅

So even though the output file ends with **`.o`**, it behaves like a runnable executable program.

---

# Part B — Tools Used 

## 1) `riscv64-unknown-elf-gcc` (Compiler)

### What it does
- converts C source into **RISC-V machine code**
- links it into an executable

### Why it matters
- confirms your environment can **build RISC-V programs correctly**

---

## 2) `spike` (RISC-V Simulator)

### What it is
- official **RISC-V ISA simulator**

### What it does
- simulates a **RISC-V CPU core**
- executes your compiled **RISC-V ELF** binary

### Why it matters
- you can run RISC-V programs **without real hardware**

---

## 3) `pk` (Proxy Kernel / Loader)

### What it does
- loads the ELF binary into **simulated memory**
- sets up an execution environment
- provides minimal runtime support (**printing / basic syscalls**)

That’s why we run:


spike pk sum1ton.o


## 4) `iverilog` (Verilog Simulator)

**Not used directly in Task-1 execution**, but we confirmed it exists.

**Why it matters later**
- Used to simulate RTL/SoC modules in future tasks.

---

# Part C — What We Ran and Why

## Step 1: Locate the program

The sample program is located at:

bash
/workspaces/vsd-riscv2/samples/sum1ton.c

What it does

Computes a sum and prints the result.

Step 2: Compile the program

Command:

riscv64-unknown-elf-gcc -o sum1ton.o sum1ton.c


Output:

sum1ton.o (RISC-V ELF executable)

Step 3: Inspect the binary
A) Check the file exists + metadata
ls -l sum1ton.o


Shows

file exists

permissions

size

timestamp

✅ Meaning: proves compilation produced an output file.

B) Check the file type + architecture
file sum1ton.o


We got

ELF executable

RISC-V target

statically linked

debug info present

✅ Meaning: proves it’s a real RISC-V executable binary.

Step 4: Run the binary

Command:

spike pk sum1ton.o


What happens internally

pk loads the ELF into simulated RAM

Spike simulates CPU execution

Program prints output via runtime support from pk

Observed output

bbl loader
Sum from 1 to 9 is 45


✅ Meaning: RISC-V execution flow works end-to-end.


