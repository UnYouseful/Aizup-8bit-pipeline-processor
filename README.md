# Aizup 8-Bit Pipelined Processor Implementation

A hardware implementation of the **Aizup** 8-bit pipelined RISC processor. This project is based on the architectural specification detailed in the 1996 IEEE Symposium paper: *"Aizup: A Pipelined Processor Design and Implementation on XILINX FPGA Chip"* by Yamin Li and Wanming Chu (University of Aizu). 

The Aizup processor was originally designed as an educational model to teach core concepts of instruction-level parallelism, data forwarding, and pipeline hazard mitigation. This repository contains the hardware description code implementing the core datapath, control unit, and specialized hazard detection logic.

---

##  Processor Architecture & Pipeline

The Aizup processor uses an **8-bit word width** for both instructions and data, optimized to fit entirely within a localized memory space. It utilizes a **4-stage pipeline**:

* **IF (Instruction Fetch):** Fetches the 8-bit instruction from code memory and increments the Program Counter (PC).
* **DC (Decode / Operand Fetch):** Decodes operation codes, evaluates branch targets, and reads from a 4x8-bit multi-port register file.
* **EX (Execution / Memory Access):** Performs ALU operations or accesses the synchronous data memory.
* **WB (Write Back):** Writes computed results or loaded memory data back into the destination register.

### Hazard Mitigation & Optimization
* **Data Dependency & Forwarding:** The architecture features an internal data path and forwarding logic to resolve RAW (Read-After-Write) data hazards. Unlike standard designs that perform dependency detection entirely in the EX stage, Aizup optimizes the critical path by moving detection logic to the **DC stage** and transferring the status flags via pipeline registers. This reduces gate count by sharing common decode circuitry and shortens the EX stage propagation delay.
* **Control Dependency:** Adopts a **delayed branch mechanism**. Branch targets are evaluated early in the DC stage using a dedicated address adder, introducing exactly one delay slot into the pipeline execution flow.

---

##  Instruction Set Architecture (ISA)

The processor supports a lean 16-instruction ISA featuring register-to-register, immediate, load/store, and conditional branch operations:

| Mnemonic | Opcode (4-bit) | Format / Operands | Operation Summary |
| :--- | :--- | :--- | :--- |
| **NOP** | `0000` | — | No Operation |
| **ADD** | `0001` | RD, RS | RD = RD + RS (Updates Zero Flag) |
| **SUB** | `0010` | RD, RS | RD = RD - RS (Updates Zero Flag) |
| **OR** | `0011` | RD, RS | Bitwise OR into RD (Updates Zero Flag) |
| **AND** | `0100` | RD, RS | Bitwise AND into RD (Updates Zero Flag) |
| **XOR** | `0101` | RD, RS | Bitwise XOR into RD (Updates Zero Flag) |
| **MOV** | `0110` | RD, RS | RD = RS (Updates Zero Flag) |
| **LD** | `0111` | RD, RS | Load from memory: RD = M[RS] |
| **ST** | `1000` | RD, RS | Store to memory: M[RS] = RD |
| **ADDI** | `1001` | RD, n | RD = RD + 0000000n (Updates Zero Flag) |
| **SUBI** | `1010` | RD, n | RD = RD - 0000000n (Updates Zero Flag) |
| **SROL** | `1011` | R0, N | R0 = R0 or 0000N (Updates Zero Flag) |
| **SROH** | `1100` | R0, N | R0 = N0000 (Updates Zero Flag) |
| **BZ** | `1101` | N | Branch if Zero flag is set: if (Z) PC = PC + (s)N |
| **BNZ** | `1110` | N | Branch if Zero flag is not set: if (!Z) PC = PC + (s)N |
| **BRA** | `1111` | N | Unconditional branch: PC = PC + (s)N |


