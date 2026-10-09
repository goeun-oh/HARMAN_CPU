# RISC-V RV32I CPU: Single-Cycle and Multi-Cycle RTL

**Harman Semicon Academy | April–May 2025 | SystemVerilog / RTL Design**

An educational CPU design project exploring **single-cycle and multi-cycle implementations** of a 32-bit RISC-V RV32I-based processor. The focus was on building the CPU datapath and control logic in RTL, then extending the processor toward a memory-mapped peripheral system using an AMBA APB bus (in a [companion repository](https://github.com/goeun-oh/APB)).

> **Scope:** This repository contains instructional RTL implementations and basic simulation scaffolding. It is not presented as a fully RISC-V-compliant or production-ready CPU.

## Project Highlights

- Implemented separate **instruction ROM and data RAM** following a Harvard-style memory organization.
- Built a CPU core with a **program counter, register file, ALU, immediate generator, branch/jump selection, and instruction decoder**.
- Explored a **single-cycle datapath** and a **multi-cycle implementation** that separates execution into control states and datapath registers.
- Used a **finite-state machine (FSM)** in the multi-cycle control unit to sequence instruction processing.
- Continued the project with an **APB-based, memory-mapped peripheral system**; the integrated design is in the separate `APB` repository.

## Architecture

```text
                     +--------------------------+
 Instruction ROM --->|       RV32I_Core         |
                     |  +--------------------+  |
                     |  | ControlUnit        |  |
                     |  | Decode / FSM       |  |
                     |  +--------------------+  |
                     |  | DataPath           |  |
                     |  | PC / RegFile / ALU |  |
                     |  | Immediate / MUXes  |  |
                     |  +--------------------+  |
                     +------------+-------------+
                                  |
                                  v
                              Data RAM
```

The top-level `MCU` module instantiates `RV32I_Core`, an instruction `rom`, and a data `ram`. The core is split into `ControlUnit` and `DataPath`, making it easier to study how control signals drive the datapath.

### Single-cycle implementation

**Source:** [`singleCycle/all/`](singleCycle/all/)

Each instruction is organized around a single-cycle datapath and combinational instruction decoding. The source includes the register file, ALU, program counter logic, immediate extension, ROM/RAM modules, and a simple SystemVerilog testbench.

### Multi-cycle implementation

**My implementation:** [`multiCycle/me/`](multiCycle/me/)  
**Instructor/reference material:** [`multiCycle/prfoessor/`](multiCycle/prfoessor/)

The multi-cycle version adds intermediate datapath registers and FSM-based sequencing. In `multiCycle/me/ControlUnit.sv`, the controller uses states named `Fetch`, `Decode`, `EXE`, `memAccess`, and `WB`; the transitions differ for load and store instructions.

Conceptually, the design separates instruction processing into the following stages:

| Stage | Purpose |
| --- | --- |
| Fetch | Access instruction memory and control PC advancement |
| Decode | Decode instruction fields and prepare operands |
| Execute | Perform arithmetic, address calculation, or control-flow operations |
| Memory access | Access data memory for loads and stores |
| Write-back | Handle the final stage of load-data processing |

The `me` and `prfoessor` directories are retained separately to distinguish my implementation from the instructor/reference version.

## RTL Source Map

| File | Responsibility |
| --- | --- |
| `RV32I_Core.sv` | Connects the control unit and datapath |
| `ControlUnit.sv` | Opcode decoding and control-signal generation; FSM in the multi-cycle version |
| `DataPath.sv` | PC, registers, ALU, immediate extension, arithmetic and selection logic |
| `MCU.sv` | Top-level integration of CPU core, instruction ROM, and data RAM |
| `rom.sv` / `ram.sv` | Instruction storage and data-memory model |
| `defines.sv` | Opcode and ALU operation definitions |
| `tb_RV32I.sv` | Basic simulation top with clock and reset stimulus |

Both the single-cycle and multi-cycle implementations contain files with the same module names. **Compile only one implementation at a time.**

## Simulation Notes

1. Select either `singleCycle/all/` or `multiCycle/me/` as your RTL source directory.
2. Compile the SystemVerilog source files from that directory and make `defines.sv` available on the include path.
3. Use `tb_RV32I` as the simulation top module; it instantiates `MCU` and drives clock/reset.
4. Review or edit `rom.sv` to select instruction stimuli, and inspect internal waveforms during simulation.

The included testbench is minimal; this repository does **not** include a documented automated regression flow or an ISA-compliance test report.

## Related Project: APB Peripheral Integration

**Repository:** [goeun-oh/APB](https://github.com/goeun-oh/APB)

The companion project expands the CPU into a memory-mapped peripheral system. Its [`APB_final/5.merge/`](https://github.com/goeun-oh/APB/tree/master/APB_final/5.merge) source includes a CPU, APB master, and peripheral modules such as **UART, timer, GPIO, seven-segment display, ultrasonic sensor, and DHT11 controller**. The APB project also includes intermediate design stages and bus/peripheral verification files.

---

**Project context:** Harman Semicon Academy, semiconductor design and verification training (2025).
