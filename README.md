# Aizup 8-Bit Pipelined Processor Implementation

[cite_start]A hardware implementation of the **Aizup** 8-bit pipelined RISC processor[cite: 2, 5]. [cite_start]This project is based on the architectural specification detailed in the 1996 IEEE Symposium paper: *"Aizup: A Pipelined Processor Design and Implementation on XILINX FPGA Chip"* by Yamin Li and Wanming Chu (University of Aizu)[cite: 1, 2, 4]. 

[cite_start]The Aizup processor was originally designed as an educational model to teach core concepts of instruction-level parallelism, data forwarding, and pipeline hazard mitigation[cite: 5, 6]. [cite_start]This repository contains the hardware description code implementing the core datapath, control unit, and specialized hazard detection logic[cite: 14].

---

## 🛠️ Processor Architecture & Pipeline

[cite_start]The Aizup processor uses an **8-bit word width** for both instructions and data, optimized to fit entirely within a localized memory space[cite: 25]. [cite_start]It utilizes a **4-stage pipeline**[cite: 6, 27]:

* [cite_start]**IF (Instruction Fetch):** Fetches the 8-bit instruction from code memory and increments the Program Counter (PC)[cite: 27, 32].
* [cite_start]**DC (Decode / Operand Fetch):** Decodes operation codes, evaluates branch targets, and reads from a $4 \times 8$-bit multi-port register file[cite: 27, 32, 98, 110].
* [cite_start]**EX (Execution / Memory Access):** Performs ALU operations or accesses the synchronous data memory[cite: 27, 32].
* [cite_start]**WB (Write Back):** Writes computed results or loaded memory data back into the destination register[cite: 27, 32].

### Hazard Mitigation & Optimization
* [cite_start]**Data Dependency & Forwarding:** The architecture features an internal data path and forwarding logic to resolve RAW (Read-After-Write) data hazards[cite: 34, 42, 45, 46]. [cite_start]Unlike standard designs that perform dependency detection entirely in the EX stage, Aizup optimizes the critical path by moving detection logic to the **DC stage** and transferring the status flags via pipeline registers[cite: 94]. [cite_start]This reduces gate count by sharing common decode circuitry and shortens the EX stage propagation delay[cite: 95, 96].
* [cite_start]**Control Dependency:** Adopts a **delayed branch mechanism**[cite: 97]. [cite_start]Branch targets are evaluated early in the DC stage using a dedicated address adder, introducing exactly one delay slot into the pipeline execution flow[cite: 98, 99].

---

## 📋 Instruction Set Architecture (ISA)

[cite_start]The processor supports a lean 16-instruction ISA featuring register-to-register, immediate, load/store, and conditional branch operations[cite: 26, 28]:

| Mnemonic | Opcode (4-bit) | Format / Operands | Operation Summary |
| :--- | :--- | :--- | :--- |
| **NOP** | `0000` | — | [cite_start]No Operation [cite: 32] |
| **ADD** | `0001` | RD, RS | [cite_start]$RD \leftarrow RD + RS$ (Updates Zero Flag) [cite: 32] |
| **SUB** | `0010` | RD, RS | [cite_start]$RD \leftarrow RD - RS$ (Updates Zero Flag) [cite: 32] |
| **OR** | `0011` | RD, RS | [cite_start]Bitwise OR into RD (Updates Zero Flag) [cite: 32] |
| **AND** | `0100` | RD, RS | [cite_start]Bitwise AND into RD (Updates Zero Flag) [cite: 32] |
| **XOR** | `0101` | RD, RS | [cite_start]Bitwise XOR into RD (Updates Zero Flag) [cite: 32] |
| **MOV** | `0110` | RD, RS | [cite_start]$RD \leftarrow RS$ (Updates Zero Flag) [cite: 32] |
| **LD** | `0111` | RD, RS | [cite_start]Load from memory: $RD \leftarrow M[RS]$ [cite: 32] |
| **ST** | `1000` | RD, RS | [cite_start]Store to memory: $M[RS] \leftarrow RD$ [cite: 32] |
| **ADDI** | `1001` | RD, n | [cite_start]$RD \leftarrow RD + 0000000n$ (Updates Zero Flag) [cite: 32] |
| **SUBI** | `1010` | RD, n | [cite_start]$RD \leftarrow RD - 0000000n$ (Updates Zero Flag) [cite: 32] |
| **SROL** | `1011` | R0, N | [cite_start]$R0 \leftarrow R0 \text{ or } 0000N$ (Updates Zero Flag) [cite: 32] |
| **SROH** | `1100` | R0, N | [cite_start]$R0 \leftarrow N0000$ (Updates Zero Flag) [cite: 32] |
| **BZ** | `1101` | N | [cite_start]Branch if Zero flag is set: $\text{if } (Z) \text{ PC} \leftarrow \text{PC} + (s)N$ [cite: 32] |
| **BNZ** | `1110` | N | [cite_start]Branch if Zero flag is not set: $\text{if } (!Z) \text{ PC} \leftarrow \text{PC} + (s)N$ [cite: 32] |
| **BRA** | `1111` | N | [cite_start]Unconditional branch: $\text{PC} \leftarrow \text{PC} + (s)N$ [cite: 32] |

---

## 📂 Repository Structure

```text
├── src/             # RTL Hardware Description Files (Datapath, Control Unit, ALU, RegFile)
├── test/            # Testbenches, Assembly Test Code, and Simulation Scripts
├── doc/             # Reference Architectural Diagrams and Documentation
└── README.md        # Project Manifest
