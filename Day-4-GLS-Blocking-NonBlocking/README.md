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

### RTL Simulation Waveform

<img width="1920" height="1080" alt="1_ternary_mux_simulation" src="https://github.com/user-attachments/assets/e5eb63f0-31f7-4fd5-8607-fe8b7187318f" />


**Observation:**

The waveform shows the correct operation of the 2:1 MUX. When sel is 0, output y follows i0. When sel is 1, output y follows i1.

### 2.3 Synthesis Using Yosys

The RTL design is synthesized using the SKY130 standard-cell library.

**yosys**

Yosys commands:

```bash
read_liberty -lib <SKY130_LIBERTY_PATH>
read_verilog ternary_operator_mux.v
synth -top ternary_operator_mux
abc -liberty <SKY130_LIBERTY_PATH>
write_verilog -noattr ternary_operator_mux_net.v
show
```
### Synthesis Diagram 

<img width="1920" height="1080" alt="2_ternary_mux_synthesis" src="https://github.com/user-attachments/assets/30b63b67-e1fe-4a32-9b1a-1a4724f51e5b" />

### 2.4 Gate-Level Simulation

To simulate the synthesized netlist, compile it along with the SKY130 standard-cell Verilog models and the same testbench.

```bash
iverilog ../my_lib/verilog_model/primitives.v ../my_lib/verilog_model/sky130_fd_sc_hd.v ternary_operator_mux_net.v tb_ternary_operator_mux.v
./a.out
gtkwave tb_ternary_operator_mux.vcd
```

### Ternary MUX: RTL Simulation, Gate-Level Simulation and Synthesis

<img width="1920" height="1080" alt="3_ternary_mux" src="https://github.com/user-attachments/assets/13464782-4edb-4dde-b59c-41a5f0afe0c0" />

## 3. Synthesis-Simulation Mismatch Due to Missing Sensitivity List

Synthesis-Simulation Mismatch occurs when the behavior observed during RTL simulation differs from the behavior of the synthesized netlist.

### Reasons for Synthesis-Simulation Mismatch

- Missing Sensitivity List.

- Blocking and Non-Blocking assignment issues.

- Non-standard Verilog coding styles.

### 3.1 Missing Sensitivity List

A Verilog simulator works based on signal activity.

Consider a 2:1 MUX implemented using an incomplete sensitivity list:

```verrilog
module bad_mux (input i0, input i1, input sel, output reg y);

always @(sel)
begin
    if (sel)
        y = i1;
    else
        y = i0;
end

endmodule
```

### Problem:

- The always block executes only when sel changes.

- If i0 or i1 changes while sel remains constant, the output will not update in RTL simulation.

### Correct MUX Implementation

```verilog
module good_mux (input i0, input i1, input sel, output reg y);

always @(*)
begin
    if (sel)
        y = i1;
    else
        y = i0;
end

endmodule
```

Using always @(*) automatically includes the signals read inside the block in the sensitivity list.

### Missing Sensitivity List: RTL simulation 

<img width="1920" height="1080" alt="4_bad_mux_simulation" src="https://github.com/user-attachments/assets/61789add-d96b-46af-8aa7-4d2d27bc4776" />

### Missing Sensitivity List: Gate level simulation

<img width="1920" height="1080" alt="5_bad_mux_GLS" src="https://github.com/user-attachments/assets/911858ba-f005-431e-8427-a1b1ac5aa37c" />

### Missing Sensitivity List: RTL vs Gate-Level Simulation

<img width="1920" height="1080" alt="6_bad_mux" src="https://github.com/user-attachments/assets/649b7c36-d402-48d5-8891-d1fc40870fb3" />

### Synthesis

<img width="1920" height="1080" alt="7_bad_mux_synthesis" src="https://github.com/user-attachments/assets/2ab2f368-64a5-430b-9b29-e8416f30a7ed" />

## 4. Blocking and Non-Blocking Assignments

Blocking and Non-Blocking assignments are used inside Verilog procedural blocks.

### 4.1 Blocking Assignment (=)

- Represented using the = operator.

- Executes statements in the order in which they are written.

- The first statement completes before the next statement executes.

- Commonly used for combinational logic.

**Example:**

```bash
always @(*)
begin
    a = b;
    c = a;
end
```

Here, c receives the updated value of a.

### 4.2 Non-Blocking Assignment (<=)

- Represented using the <= operator.

- Right-hand-side expressions are evaluated when the statements execute.

- Left-hand-side updates are scheduled for a later simulation region.

- Commonly used for sequential circuits.

**Example:**

```bash
always @(posedge clk)
begin
    a <= b;
    c <= a;
end
```

Here, c receives the previous value of a, while a receives the value of b.

### Difference Between Blocking and Non-Blocking

| Blocking (=) | Non-Blocking (<=) |
|---|---|
| Sequential execution | Scheduled updates |
| Statements execute in written order | RHS values are evaluated before scheduled updates |
| Used mainly for combinational logic | Used mainly for sequential logic |
| Statement order affects behavior | Models simultaneous register updates |

**Important:** Use blocking assignments for combinational procedural logic and non-blocking assignments for clocked sequential logic.

### 4.3 Synthesis-Simulation Mismatch for Blocking Statements

**Blocking Caveat Design**

Consider the following Verilog code:

```bash
module blocking_caveat (
    input a,
    input b,
    input c,
    output reg d
);

reg x;

always @(*)
begin
    d = x & c;
    x = a | b;
end

endmodule
```

### Explanation

- The statements are executed sequentially because blocking assignments are used.

- First, d is assigned using the current value of x and c.

- Next, x is updated with the value of a | b.

- Therefore, the first statement uses the previous value of x.

The RTL simulation can exhibit latch-like behavior because the output depends on the previous value of x.

### Blocking Assignment Caveat: RTL Simulation 

<img width="1920" height="1080" alt="8_blocing_caveat_simulation" src="https://github.com/user-attachments/assets/7bb2124e-61af-4000-a6c2-3ae02e755851" />


**Observation:**

The RTL simulation demonstrates the effect of statement ordering in blocking assignments. The output does not always follow the intended combinational expression.

###  Blocking Assignment Caveat: GLS

<img width="1920" height="1080" alt="blocking_caveat_GLS" src="https://github.com/user-attachments/assets/1b735218-3ef7-44e5-9c9a-bf0dd2eb0681" />


**Observation:**

The gate-level simulation can produce a different output from the RTL simulation for the same inputs. This demonstrates the synthesis-simulation mismatch caused by the blocking assignment coding style.

### Blocking Assignment Caveat: RTL vs Gate-Level Simulation

<img width="1920" height="1080" alt="blocking_caveat" src="https://github.com/user-attachments/assets/7d7be712-d2ea-4eef-9284-becd5a080411" />

### Synthesis

<img width="1920" height="1080" alt="9_blocking_caveat_synthesis" src="https://github.com/user-attachments/assets/b80ced8a-9a35-45a9-a8f9-ace452aa543b" />


**Conclusion:**

The blocking caveat experiment demonstrates why proper Verilog coding styles are important to avoid differences between RTL simulation and synthesized hardware.

## 5. Tools Used

| Tool | Application |
|---|---|
| Verilog HDL | RTL Design |
| Icarus Verilog | RTL and Gate-Level Simulation |
| Yosys | Logic Synthesis |
| GTKWave | Waveform Analysis |
| SKY130 Standard Cell Library | Technology Mapping |
| ABC | Logic Optimization and Technology Mapping |

## 6. Key Learnings

- Understood the concept and importance of Gate-Level Simulation.

- Learned how to perform GLS using Icarus Verilog.

- Understood the role of synthesized netlists and standard-cell Verilog models.

- Learned about synthesis-simulation mismatch.

- Understood the importance of a complete sensitivity list.

- Differentiated blocking and non-blocking assignments.

- Learned how blocking statement ordering affects simulation.

- Performed RTL simulation, synthesis and gate-level simulation.

- Understood the importance of verifying synthesized designs.

## 8. Conclusion

- In Day 4, I learned about Gate-Level Simulation, Synthesis-Simulation Mismatch, and Blocking vs Non-Blocking assignments.

- I performed RTL simulation using Icarus Verilog, synthesized the designs using Yosys with the SKY130 standard-cell library, and analyzed the generated waveforms using GTKWave.

- The experiments helped me understand how incomplete sensitivity lists and improper blocking assignment usage can cause differences between RTL simulation and synthesized netlist behavior.








