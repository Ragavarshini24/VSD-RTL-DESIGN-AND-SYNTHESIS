## Day 4 – Gate-Level Simulation (GLS), Blocking vs Non-Blocking Assignments and Synthesis-Simulation Mismatch

## Introduction

Day 4 of the RTL Design and Synthesis using Verilog workshop focuses on Gate-Level Simulation (GLS), Synthesis-Simulation Mismatch, and Blocking vs Non-Blocking assignments.

In this session, I learned how to perform Gate-Level Simulation using Icarus Verilog, synthesize Verilog designs using Yosys, and analyze the differences between RTL simulation and synthesized netlist simulation.

## 1. Gate-Level Simulation (GLS)

### What is GLS?

GLS stands for Gate-Level Simulation.

- Running the testbench with the synthesized netlist as the Design Under Test (DUT).

- The netlist is logically equivalent to the RTL code, but represented using technology-specific standard cells.

- The same testbench can be used for both RTL simulation and gate-level simulation.

### Why GLS?

- To verify the logical correctness of the design after synthesis.

- To ensure that the synthesized netlist behaves as expected.

- To identify synthesis-simulation mismatches.

- To verify timing behavior using appropriate delay annotation.

### GLS using Icarus Verilog

To perform GLS, we need three inputs:

- Synthesized gate-level netlist.

- Standard-cell Verilog models.

- Testbench.

## Gate-Level Simulation (GLS) Flow

The Gate-Level Simulation (GLS) flow is used to verify the synthesized gate-level netlist by comparing its behavior with the expected RTL functionality.

The GLS flow consists of the following steps:

1. **RTL Design:** The design is initially written and verified using RTL Verilog code.
2. **Synthesis using Yosys:** Yosys synthesizes the RTL design and generates a gate-level netlist.
3. **Gate-Level Simulation:** The generated gate-level netlist is simulated along with standard cell Verilog models and the testbench using Icarus Verilog.
4. **VCD Generation:** The simulation generates a Value Change Dump (VCD) file containing signal transitions.
5. **Waveform Analysis:** GTKWave is used to open and observe the generated VCD waveforms to verify the circuit behavior.

## 2. Functional GLS – Ternary Operator MUX

The first experiment illustrates the Gate-Level Simulation (GLS) process using a 2:1 Multiplexer designed with the Verilog ternary conditional operator.

### 2.1 RTL Code

```bash
module ternary_operator_mux (
    input i0,
    input i1,
    input sel,
    output y
);

assign y = sel ? i1 : i0;

endmodule
```

## Functionality

The 2:1 Multiplexer selects one of the two input signals based on the select line (`sel`).

| Select Line (`sel`) | Output (`y`) |
|:-------------------:|:------------:|
| 0                   | i0           |
| 1                   | i1           |

**Working Principle:**
- When `sel = 0`, the output `y` follows input `i0`.
- When `sel = 1`, the output `y` follows input `i1`.

### 2.2 RTL Simulation

The Verilog design and testbench are given to Icarus Verilog.

```bash
iverilog -o ternary_mux.out ternary_operator_mux.v tb_ternary_operator_mux.v
./ternary_mux.out
```

The VCD file generated during simulation is opened using GTKWave.

```bash
gtkwave tb_ternary_operator_mux.vcd
```

RTL Simulation Waveform

