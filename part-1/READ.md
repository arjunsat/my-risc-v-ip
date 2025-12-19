# Task-1: Environment Setup & RISC-V Reference Bring-Up

This repository documents the complete execution, understanding, and validation of **Task-1**, which focuses on setting up a stable development environment and successfully running a reference RISC-V design before moving to FPGA or RTL work.

> **Important:** This is **NOT an FPGA programming task**. FPGA tools must **not** be installed or used at this stage.

---

## Objective

- Ensure **toolchain readiness**
- Verify a **working RISC-V reference execution**
- Understand the **RISC-V software execution flow**
- Validate **multi-repository workflow**
- Prepare for upcoming **IP, SoC, and FPGA development**

---

## Step 1: GitHub Codespace Setup (Mandatory)

### Repository Used
https://github.com/vsdip/vsd-riscv2

### Actions Performed
- Forked the `vsd-riscv2` repository
- Launched a GitHub Codespace from the fork
- Verified the Codespace opened successfully with no errors

### Why This Matters
- Ensures all participants start from the **same OS, tools, and versions**
- Avoids “works on my machine” issues
- Reflects real semiconductor team onboarding workflows

---

## Step 2: Verify RISC-V Reference Flow (Mandatory)

### Toolchain Verification

Verified the presence and working status of:
- `riscv64-unknown-elf-gcc` (RISC-V cross-compiler)
- `spike` (RISC-V ISA simulator)
- `iverilog` (Verilog simulator — later use, but confirmed installed)

#### What each tool does

**riscv64-unknown-elf-gcc**
- Cross-compiles programs on x86/Linux to generate **bare-metal RISC-V binaries**
- `unknown-elf` means the output is not targeting Linux/Windows/macOS — it’s for a “no-OS” embedded-style target

**spike**
- Official RISC-V ISA simulator
- Runs RISC-V binaries without real hardware

**iverilog**
- Verilog simulator (not required to complete the RISC-V run, but part of the overall dev environment)

---

## Step 3: Where the RISC-V Program Is Located

### Program Path
`vsd-riscv2/samples/`

### Files Used
- `sum1ton.c` — the C source program
- `load.S` — assembly support/startup
- `Makefile` — build automation

### Program Purpose
A simple reference program that computes a sum (1 to N) and prints the result, validating:
- compiler toolchain
- binary correctness
- simulator execution

---

## Step 4: How the Program Was Compiled

From:
`/workspaces/vsd-riscv2/samples`

Command used:
- `riscv64-unknown-elf-gcc -o sum1ton.o sum1ton.c`

### Key Concept: Why the file is called `.o` but is not a “normal object file”
Normally:
- `.o` = compiled object (not linked)

But here:
- we did **not** use `-c`
- so GCC compiled **and linked** a complete executable

That’s why the output is a full ELF binary.

---

## Step 5: What the `.o` File Contains (Core Concepts)

Even though it’s named `sum1ton.o`, it contains:
- **ELF header** (metadata about the executable)
- **.text section** (RISC-V machine instructions)
- **.data / .rodata** (initialized data + constants/strings)
- **symbol tables + debug info** (because it is “not stripped”)
- **entry point** (start address for execution)
- static linking information

You confirmed this using:

- `ls -l sum1ton.o`  
  ✅ Shows file exists and its size/permissions

- `file sum1ton.o`  
  ✅ Confirms it is a **RISC-V ELF executable** (not just an object file)

---

## Step 6: How the Program Was Loaded & Run (Spike + pk)

Command used:
- `spike pk sum1ton.o`

### What happens internally

**Spike**
- Creates a simulated RISC-V CPU and memory system

**pk (proxy kernel)**
- Minimal runtime “loader”
- Loads the ELF into simulated memory
- Provides minimal system-call like services (enough to print)

Then:
- the CPU starts executing at the ELF entry point
- program runs and prints output

### Observed Output
- `bbl loader`
- `Sum from 1 to 9 is 45`

✅ This confirms:
- the binary ran successfully
- no compile/runtime errors
- toolchain + simulator are functional

---

## Step 7: Step-2 Completion Status

✅ **Step 2 is COMPLETE**

You achieved:
- successful compile
- successful run on Spike
- correct output

---

## Step 8: Step 3 — Clone and Run VSDFPGA Labs (Mandatory)

Repository:
https://github.com/vsdip/vsdfpga_labs

Commands used:
- `cd /workspaces`
- `git clone https://github.com/vsdip/vsdfpga_labs.git`
- `cd vsdfpga_labs`
- `ls`

✅ Verified repository contains:
- `README.md`
- `basicRISCV/`
- images for reference

---

## Step 9: VSDFPGA Labs — Firmware Build (No FPGA Hardware)

Path:
`/workspaces/vsdfpga_labs/basicRISCV/Firmware`

Command run:
- `make riscv_logo.bram.hex | tee vsdfpga_firmware_log.txt`

### What this step does
It generates a BRAM initialization hex file:
- `riscv_logo.bram.hex`

This hex file is used later by FPGA RTL to initialize memory contents.

✅ You successfully executed the firmware build step.

---

## Step 10: Why FPGA Build/Flash Was Not Done (Correct Behavior)

The FPGA build flow would require tools like:
- `yosys`
- `nextpnr-ice40`
- `icetime`
- `icepack`

Task-1 explicitly says:
> FPGA tools must NOT be installed or used at this stage.

So you correctly stopped at:
- firmware generation
- log verification
- dry-run checking only

---

## Step 11: Dockerfile Reference Understanding (Local Prep Guidance)

The Dockerfile is a **reference** listing:
- required packages
- expected versions
- how tools are installed/configured

It is **NOT** meant to be executed blindly.

Key takeaways:
- installs core build dependencies
- downloads prebuilt SiFive toolchain into `/opt/riscv`
- builds Spike
- builds riscv-pk (proxy kernel)
- sets PATH to include `/opt/riscv/bin`

---

# Understanding Check (Mandatory Answers)

## 1) Where is the RISC-V program located in vsd-riscv2?
`/workspaces/vsd-riscv2/samples/sum1ton.c`

## 2) How is the program compiled and loaded into memory?
- Compiled using `riscv64-unknown-elf-gcc` (cross-compiler)
- Output is a RISC-V ELF executable (linked)
- Loaded into simulated memory by `pk`
- Executed by `spike`

## 3) How does the RISC-V core access memory and memory-mapped IO?
- Uses RISC-V load/store instructions to access memory addresses
- IO appears as specific addresses (MMIO regions)
- `pk` + simulation environment provide minimal services to let output appear (e.g., print)

## 4) Where would a new FPGA IP block integrate in this system?
As a **memory-mapped peripheral**:
- attached to the SoC interconnect/bus
- mapped to a specific address range
- accessed by firmware via load/store to those addresses

---

# Optional Confidence Task (Recommended)

## Goal
Modify the output message or a constant, rebuild, rerun, and observe changes.

### Note (Editor availability)
If `nano` is missing, use:
- `vi sum1ton.c`
or
- `sed` to patch text quickly

Example approach:
- change a printed string or value in `sum1ton.c`
- recompile with `riscv64-unknown-elf-gcc -o sum1ton.o sum1ton.c`
- rerun with `spike pk sum1ton.o`

---

# Final Status

✅ Codespace environment set up  
✅ Toolchain verified (`gcc`, `spike`, `iverilog`)  
✅ RISC-V reference program compiled and executed successfully  
✅ VSDFPGA labs cloned successfully  
✅ Firmware hex generated successfully  
✅ FPGA build/flash steps intentionally avoided  

🎯 **Task-1 is COMPLETE**
