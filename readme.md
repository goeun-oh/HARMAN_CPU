# RISC-V RV32I CPU

Harman Semicon Academy | April–May 2025  
SystemVerilog / RTL Design

This repository contains my single-cycle and multi-cycle RISC-V RV32I CPU implementations from the Harman Semicon Academy training course. I started with a simple CPU datapath and then added an FSM-based control unit for multi-cycle execution.

## What I implemented

- **Datapath:** program counter, register file, ALU, immediate generator, and muxes for branch/jump and write-back data
- **Control unit:** instruction decoding and control signals for arithmetic, load/store, branch, and jump operations
- **Memory:** separate instruction ROM and data RAM (Harvard-style organization)
- **Multi-cycle design:** control FSM and intermediate datapath registers to separate instruction processing across clock cycles

The multi-cycle controller in `multiCycle/me/ControlUnit.sv` uses `Fetch`, `Decode`, `EXE`, `memAccess`, and `WB` states. The number of cycles depends on the instruction type.

## Repository structure

| Path | Contents |
| --- | --- |
| [`singleCycle/all/`](singleCycle/all/) | Single-cycle CPU RTL and testbench |
| [`multiCycle/me/`](multiCycle/me/) | My multi-cycle CPU implementation |
| [`multiCycle/prfoessor/`](multiCycle/prfoessor/) | Instructor's reference implementation |
| [`multiCycle/readme.md`](multiCycle/readme.md) | Notes on the multi-cycle design |

Main RTL modules:

- `RV32I_Core.sv` — connects the control unit and datapath
- `ControlUnit.sv` — decodes instructions and generates control signals
- `DataPath.sv` — register file, ALU, PC logic, immediate extension, and datapath registers
- `MCU.sv` — connects the CPU core, ROM, and RAM
- `tb_RV32I.sv` — basic clock/reset simulation testbench

The source folders contain modules with the same names, so each CPU version should be compiled separately. The included testbench is a starting point for waveform inspection rather than an automated ISA compliance test.

## APB peripheral integration

After the CPU exercises, I worked on connecting the CPU to memory-mapped peripherals through an AMBA APB bus. The peripheral project is in a separate repository:

**[APB — CPU and peripheral integration](https://github.com/goeun-oh/APB)**

It includes APB bus logic and modules for UART, GPIO, timer, seven-segment display, and sensors.
