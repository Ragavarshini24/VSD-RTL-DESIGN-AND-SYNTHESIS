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








