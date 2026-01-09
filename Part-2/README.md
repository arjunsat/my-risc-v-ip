## Task-2 Overview: Simple Memory-Mapped GPIO IP (RISC-V SoC)

What this task is about

You will build your first custom peripheral (IP) and plug it into an existing RISC-V SoC like a real industry flow:
RTL → Integration → Software access →  proof.



## IP: Simple GPIO Output IP

One 32-bit register

CPU write to the register → updates an output signal

CPU read from the register → returns the last written value

IP is memory-mapped and uses the SoC’s existing bus signals

Addressing:

Base address (given by mentor, e.g., 0x2000_0000)

Offset 0x00 → GPIO register



## Gpio.v
<img width="1331" height="989" alt="image" src="https://github.com/user-attachments/assets/79604d06-808e-4990-bf6d-5305c5a7b85b" />

## Gpio_ip_task2.v
<img width="1561" height="1050" alt="image" src="https://github.com/user-attachments/assets/ee13c2ff-ab90-4add-89d7-1e7aa04cb659" />



## Hardware Implementation

<img width="1705" height="864" alt="image" src="https://github.com/user-attachments/assets/d9810f97-88f4-425d-8ba9-ec389fb1eb7e" />


<img width="942" height="924" alt="image" src="https://github.com/user-attachments/assets/488ebbf9-f8c9-4a60-bf9c-89487439ca67" />

## Learning outcome (Task-2: Memory-Mapped GPIO IP)


Understood the RISC-V SoC bus and memory-map, including how peripherals are address-decoded and accessed by the CPU.

Implemented a synchronous, synthesizable RTL IP with a 32-bit register, write capture, and readback behavior.

Practiced SoC integration: instantiation, signal wiring, and clean address decoding for a new peripheral.

Learned the complete HW–SW interaction flow for memory-mapped I/O using a C program (volatile pointer loads/stores).


Improved debugging skills by correlating firmware prints, bus transactions, and RTL waveforms to isolate issues quickly.

 Gained exposure to bitstream programming / board bring-up, verifying the same IP on real hardware (LED/UART observation).




