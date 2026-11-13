## Day 5 – Optimization in Synthesis

### Overview

Day 5 focuses on optimization in synthesis using Verilog HDL. This session covers conditional statements and looping constructs and explains how coding styles affect hardware inference during synthesis.

The main objective is to understand how to write efficient RTL code, avoid unintended latch inference, and generate repeated hardware using looping constructs.

## 1. If-Else Construct

- The `if-else` statement is mainly used to implement priority logic in digital circuits.

```verilog
always @(*)
begin
    if (cond1)
        y = a;
    else if (cond2)
        y = b;
    else
        y = c;
end
```

The conditions are checked one by one in sequence. When cond1 is true, its corresponding assignment is executed, and the other conditions are ignored. The next condition, cond2, is evaluated only if cond1 is false.

This results in priority-based logic, which can be implemented in hardware using nested or cascaded multiplexers.

## 2.Inferred Latches

An inferred latch occurs when an output is not assigned under every possible condition in a combinational always @(*) block.

When the condition is not satisfied, the output retains its previous value.

### Example: Incomplete If Statement

```verilog
module incomp_if (
    input i0,
    input i1,
    output reg y
);

always @(*) begin
    if (i0)
        y = i1;
end

endmodule
```

### Observation:

- When i0 = 1, output y follows i1.

- When i0 = 0, output y retains its previous value.

- Therefore, synthesis infers a D-latch.

### Important Note

Incomplete conditions in a clocked block are different.

For example, a counter can intentionally retain its value when enable is low:

```verilog
always @(posedge clk or posedge reset) begin
    if (reset)
        count <= 3'b000;
    else if (en)
        count <= count + 1;
end
```

Here, the counter is implemented using flip-flops with an enable/hold behavior, not an unintended combinational latch.

## 3. Case Statements

A case statement is used to select one of multiple possible operations based on a selector signal.

It is commonly used to implement multiplexers and selection logic.

### Syntax

```bash
always @(*) begin
    case (sel)
        2'b00: y = a;
        2'b01: y = b;
        2'b10: y = c;
        2'b11: y = d;
        default: y = 1'b0;
    endcase
end
```
### Caveats with Case Statements

**1. Incomplete Case**

If some selector combinations are not specified and there is no default assignment, synthesis may infer a latch.

**2. Default Statement**

Using a default assignment ensures that the output receives a value for unmatched selector combinations.

**3. Partial Assignments**

If multiple outputs are assigned in a case statement, every output must be assigned in every branch to avoid unintended latch inference.

**4. Overlapping Conditions**

Avoid overlapping wildcard case conditions, as they can produce ambiguous or priority-dependent behavior depending on the case construct used.

## 4. Lab 1 – Incomplete If Statement

```verilog
module incomp_if (
    input i0,
    input i1,
    output reg y
);

always @(*)
begin
    if (i0)

        y <= i1;
end

endmodule
```

In this code, `y` is assigned a value only when `i0` is HIGH (1). When `i0` is LOW (0), no assignment takes place, so `y` holds its previous value.

As a result, synthesis infers a `D latch` to maintain the previous output value.

### 4.1 RTL Simulation

The Verilog design and testbench are given to Icarus Verilog.

```bash
iverilog incomp_if.v tb_incomp_if.v
./a.out
```

The VCD file generated during simulation is opened using GTKWave.

```bash
gtkwave tb_incomp_if.vcd
```

### Screenshot1: Simulation Waveform

<img width="1920" height="1080" alt="01_incomp_if_simulation" src="https://github.com/user-attachments/assets/3087c609-49bd-4f6c-a85e-874d69bfed71" />

- Whenever `i0` is going low, the output `y` is latching on something either `1` or `0` permanently. There is no change in `y` whenever `i0` is zero or low.
- This clearly be latching action.
- Whenever the signal is high, output `y` is following `i1`.







