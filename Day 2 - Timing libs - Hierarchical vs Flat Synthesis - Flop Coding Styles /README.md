## Day 2: Timing Libraries, Hierarchical vs Flat Synthesis and Flop  Coding Styles

## Topics Covered
- Introduction to Timing Libraries (.lib)
- Hierarchical vs. Flat Synthesis
- Efficient Flip-Flop Coding Styles
 
# 1. Introduction to Timing Libraries (.lib)

## What is a .lib file?

A Liberty (.lib) file is a standard-cell library file used in digital IC design and synthesis.

It contains information about the standard cells available in a technology library, such as:
- Logic functionality
- Timing characteristics
- Power consumption
- Input capacitance
- Output drive strength
- Operating conditions
A `.lib` file helps synthesis and timing-analysis tools understand the behavior of standard cells.

## Understanding the Library File Name

Example:

`sky130_fd_sc_hd_tt_025C_1v80.lib`

The filename contains information about the technology, library type, process corner, temperature, and supply voltage

| Parameter | Meaning |
|---|---|
| sky130 | SkyWater 130 nm technology |
| fd_sc_hd | Foundry standard-cell library, high-density cells |
| tt | Typical process corner |
| 025C | Temperature of 25°C |
| 1v80 | Supply voltage of 1.8 V |

## Library Units

A Liberty file specifies the units used for its parameters.

Common units include:

| Parameter | Unit |
|---|---|
| Time | ns |
| Voltage | V |
| Current | mA |
| Capacitance | pF |
| Resistance | kΩ |
| Leakage power | nW |

The actual units should be verified from the library file.

## Why is the .lib File Important?

The synthesis tool uses the library to:
- Map RTL logic into standard cells
- Estimate circuit area
- Analyze timing
- Estimate power consumption
- Select cells based on timing requirements

The library contains multiple cells with different drive strengths and timing characteristics.

For example, a stronger cell can drive a larger load, but it may consume more power and area.

# 2. Hierarchical vs. Flat Synthesis

Hierarchical synthesis preserves the module boundaries of an RTL design, whereas flat synthesis combines the logic of different modules into a single flattened design. Flattening allows the synthesis tool to optimize logic across module boundaries.

### Objective

To understand the differences between hierarchical and flat synthesis using Yosys and to observe how the RTL design is converted into a standard-cell netlist using the Sky130 standard-cell library.

### Design Description

The experiment uses a Verilog design named `multiple_modules`, which consists of two submodules.

- **Submodule 1:** Implements a 2-input AND gate.
- **Submodule 2:** Implements a 2-input OR gate.
- **Top module:** Connects the output of Submodule 1 to Submodule 2 along with input `c`.

The overall Boolean expression is:

`y = (a & b) | c`

### 1. Hierarchical Synthesis

In hierarchical synthesis, the original module structure is preserved.

The top module `multiple_modules` contains two instances:
- `u1` – Submodule 1
- `u2` – Submodule 2

Each submodule is synthesized independently, and the module hierarchy is maintained in the resulting netlist.

The hierarchical design was inspected using the Yosys `show` command.

```tcl
show multiple_modules
```
**Screenshot 1: Hierarchical Synthesis**

<img width="1920" height="1080" alt="multiple_hier" src="https://github.com/user-attachments/assets/2fc35523-bca3-4921-b7d9-e6ecc1cb9129" />

The synthesized Verilog netlist was generated using:

```tcl
write_verilog multiple_modules_hier.v
```
**Screenshot 2: Hierarchical Netlist**

<img width="1920" height="1080" alt="multiple_modules_hier_netlist png" src="https://github.com/user-attachments/assets/919c8916-b36b-4d86-aa14-41938b9b731b" />

### 2. Flat Synthesis

In flat synthesis, the hierarchy of the design is removed, and the logic of all submodules is combined into a single design.

The following Yosys command was used to flatten the design:

```tcl
flatten
```

The flattened design was inspected using:

```tcl
show
```
**Screenshot 3: Flat Synthesis**

<img width="1920" height="1080" alt="multiple_flat png" src="https://github.com/user-attachments/assets/f2f667be-73e2-4170-9412-b4eea74516e0" />

The flattened Verilog netlist was generated using:

```tcl
write_verilog multiple_modules_flat.v
```
**Screenshot 4: Flat Netlist**

<img width="1920" height="1080" alt="multiple_modules_flat_netlist png" src="https://github.com/user-attachments/assets/5902aa3c-915b-45f7-8612-669b4aa312b2" />

The flattened netlist contains the mapped standard cells without maintaining the original submodule boundaries.

### 3. Submodule-Level Synthesis

In this experiment, Submodule 1 was synthesized independently to observe its logic and standard-cell mapping.

The submodule implements a 2-input AND operation.

The resulting schematic shows the mapping of the AND operation to the Sky130 standard-cell library.

This experiment helps in understanding how individual RTL modules are converted into standard cells.

**Screenshot 5 : Submodule Synthesis**

<img width="1920" height="1080" alt="sub_module png" src="https://github.com/user-attachments/assets/e4c7e65c-4f12-4cbd-8d26-0fdab25dc27a" />

### Experiment Results

| Experiment | Observation |
|---|---|
| Hierarchical synthesis | Original module hierarchy is preserved. |
| Flat synthesis | Submodule boundaries are removed, and the design is flattened. |
| Submodule-level synthesis | Individual submodule logic is synthesized independently. |

## 3.Efficient Flop Coding Styles

Flip-flops are important sequential logic elements used to store binary data. They are widely used in registers, counters, state machines, and digital systems.

In this lab, three different flip-flop coding styles were implemented, simulated, and synthesized using the SKY130 standard cell library.

### 3.1 Asynchronous Reset Flip-Flop

An asynchronous reset flip-flop resets its output independently of the clock signal.

When the reset signal is asserted, the output q is immediately reset to logic 0. When the reset is inactive, the flip-flop captures the input data d at the active clock edge.

### RTL Coding Style

```verilog
module dff_asyncres ( input clk , input async_reset , input d , output reg q );
always @ (posedge clk or posedge async_reset)
begin
    if (async_reset)
        q <= 1'b0;
    else
        q <= d;
end
endmodule
```

### Simulation Result

- The simulation waveform verifies the following behavior:

- The clock signal toggles continuously.

- The input data changes during simulation.

- When asynchronous reset is asserted, the output becomes 0 without waiting for a clock edge.

- When reset is deasserted, the output follows the input at the next active clock edge.

### Simulation Waveform:

<img width="1920" height="1080" alt="async_reset_simulation" src="https://github.com/user-attachments/assets/45ea3c8c-87ea-49db-a67d-a2896d27e4d4" />

### Synthesis Result

The RTL was synthesized using Yosys and mapped to the SKY130 standard cell library.

The synthesized schematic shows the asynchronous reset flip-flop implementation using a standard cell with an asynchronous reset control pin.

### Synthesized Schematic:

<img width="1920" height="1080" alt="async_reset_synthesis" src="https://github.com/user-attachments/assets/b4f135e2-3059-42c3-acd8-fadd68d765c3" />

### 3.2 Asynchronous Set Flip-Flop

An asynchronous set flip-flop sets its output to logic 1 independently of the clock signal.

When the asynchronous set signal is asserted, the output q becomes 1 immediately. When the set signal is inactive, the flip-flop captures the input data at the active clock edge.

### RTL Coding Style

```verilog
module dff_async_set ( input clk , input async_set , input d , output reg q );
always @ (posedge clk , posedge async_set)
begin
    if(async_set)
        q <= 1'b1;
    else
        q <= d;
end
endmodule
```

### Simulation Result 

The simulation waveform verifies the following behavior:

- The clock signal toggles continuously.

- The input data changes during simulation.

- When asynchronous set is asserted, the output becomes 1 immediately.

- When set is deasserted, the output captures the input data at the next active clock edge.

### Simulation Waveform:

<img width="1920" height="1080" alt="async_set_simulation" src="https://github.com/user-attachments/assets/6c939ec6-427f-4aab-8e0d-0f7737beada9" />

### Synthesis Result

The RTL was synthesized using Yosys and mapped to the SKY130 standard cell library.

The synthesized schematic shows an asynchronous set flip-flop with a dedicated set control pin.

### Synthesized Schematic:

<img width="1920" height="1080" alt="async_set_synthesis" src="https://github.com/user-attachments/assets/1f7d3d1e-a12d-4875-b63b-0c696ce8f203" />

### 3.3 Synchronous Reset Flip-Flop

A synchronous reset flip-flop resets its output only at the active clock edge.

When the synchronous reset signal is asserted, the output becomes 0 at the next active clock edge. Unlike asynchronous reset, the output does not change immediately when the reset signal changes.

### RTL Coding Style

```verilog
module dff_syncres ( input clk , input async_reset , input sync_reset , input d , output reg q );
always @ (posedge clk)
begin
    if (sync_reset)
        q <= 1'b0;
    else
        q <= d;
end
endmodule
```

### Simulation Result

The simulation waveform verifies the following behavior:

- The clock signal toggles continuously.

- The input data changes during simulation.

- When synchronous reset is asserted, the output becomes 0 at the active clock edge.

- When reset is deasserted, the output captures the input data at the next active clock edge.

### Simulation Waveform:

<img width="1920" height="1080" alt="sync_reset_simulation" src="https://github.com/user-attachments/assets/ad112916-4bb1-4ad5-a854-874773160c0e" />

### Synthesis Result

The RTL was synthesized using Yosys and mapped to the SKY130 standard cell library.

The synthesized schematic shows a synchronous reset implementation using combinational logic and a standard D flip-flop.

The reset logic is implemented before the D input of the flip-flop.

### Synthesized Schematic:

<img width="1920" height="1080" alt="sync_reset_synthesis" src="https://github.com/user-attachments/assets/ea7ddae5-c003-4cee-bc92-f9b6554e5c52" />

## Comparison of Flip-Flop Coding Styles

The following table compares the behavior and synthesis implementation of asynchronous reset, asynchronous set, and synchronous reset flip-flops.

| Feature | Asynchronous Reset | Asynchronous Set | Synchronous Reset |
|---|---|---|---|
| Reset / Set behavior | Independent of clock | Independent of clock | Depends on clock |
| Output when control is asserted | 0 (Logic LOW) | 1 (Logic HIGH) | 0 (Logic LOW) |
| Control checked | At any time | At any time | At active clock edge |
| RTL sensitivity | `posedge clk` and `posedge async_reset` | `posedge clk` and `posedge async_set` | `posedge clk` |
| Typical synthesis mapping | Reset-capable flip-flop | Set-capable flip-flop | D flip-flop with reset logic |

## 4. Logic Optimization

Logic optimization is an important step in RTL design that helps reduce unnecessary hardware and improve circuit efficiency.

In this experiment, multiplication operations were implemented using Verilog HDL and synthesized using Yosys. The synthesized schematics were analyzed to understand how simple arithmetic operations can be optimized into basic wiring connections instead of using general-purpose multiplier hardware.

The following operations were studied:

- Multiplication by 2

- Multiplication by 9

### 4.1 Multiplication by 2       

For a 3-bit unsigned input `a` and 4-bit output `y`:

`y = a * 2`

This is equivalent to `y = {a, 1'b0}`. Synthesis can implement the operation using a shift and wiring instead of a general multiplier.

<img width="1920" height="1080" alt="Screenshot 2026-11-03 161010" src="https://github.com/user-attachments/assets/da0f15b6-c89f-4775-b841-f16ad69e18c5" />

### Multiplication by Nine

For a 3-bit unsigned input and 6-bit output:

`y = a * 9 = {a, a}`

Since \(9 = 8 + 1\), the result can be implemented through bit concatenation rather than a general multiplier.

<img width="1920" height="1080" alt="Screenshot 2026-11-03 162823" src="https://github.com/user-attachments/assets/79b7dfc4-3d6c-4949-b0a2-1830f2a414f7" />












