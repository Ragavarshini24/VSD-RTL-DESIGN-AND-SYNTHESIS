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

## Observations :

- Whenever `i0` is going low, the output `y` is latching on something either `1` or `0` permanently. There is no change in `y` whenever `i0` is zero or low.
- This clearly be latching action.
- Whenever the signal is high, output `y` is following `i1`.

### 4.2 Synthesis Using Yosys

The RTL design is synthesized using the SKY130 standard-cell library.

 Invoke **yosys**

Yosys commands:

```bash
read_liberty -lib <SKY130_LIBERTY_PATH>
read_verilog incomp_if.v
synth -top incomp_if
abc -liberty <SKY130_LIBERTY_PATH>
show
```

### Scrrenshot2: Synthesis Diagram

<img width="1920" height="1080" alt="02_incomp_if_synthesis" src="https://github.com/user-attachments/assets/0c66776f-05ff-4342-8e67-2dae97c39fa7" />

- It is a `D Latch` with `i0` as enable.

## 5. Lab 2 – Multiple Incomplete If Conditions

```verilog
module incomp_if2 (
    input i0,
    input i1,
    input i2,
    input i3,
    output reg y
);

always @(*)
begin
    if (i0)
        y <= i1;
    else if (i2)
        y <= i3;
end

endmodule
```

The first condition selects `i1`, while the second condition selects `i3` only when `i0` is false.

When both `i0` and `i2` are low, no assignment is made to `y`. Therefore, the output must retain its previous value, requiring a latch.

### Screenshot3: RTL Simulation 

<img width="1920" height="1080" alt="03_incomp_if2_simulation" src="https://github.com/user-attachments/assets/ecd67fdf-f3a0-453b-8dfa-3b76bedf3970" />

### Observations:

- Whenever `i0` is high, output is following exactly `i1`.
- When `i0` is low it look towards `i2`.
- When both `i0` and `i2` are low, output is constant.
- When `i2` is high it starts following `i3`.

### Screenshot4: Synthesis Diagram

<img width="1920" height="1080" alt="04_incom_if2_synthesis" src="https://github.com/user-attachments/assets/818eb1b5-86a1-4f0c-a094-ab1bfe8969dc" />

## 6. Lab 3 – Incomplete Case Statement

```verilog
module incomp_case(
          input i0,
          input i1,
          input i2,
          input[1:0]sel,
           output y
);

always @(*)
begin
    case (sel)
        2'b00: y = i0;
        2'b01: y = i1;
    endcase
end

endmodule
```

For sel = 2'b10 or 2'b11, there is no assignment to y. The output must retain its previous value, causing latch inference.

### Screenshot5: RTL Simulation 

<img width="1920" height="1080" alt="05_incomp_case_simulation" src="https://github.com/user-attachments/assets/872b6ab5-a139-4a4e-b1e2-9677c786999b" />

### Observations:

- When `sel=00`, output follow `i0`.
- When `sel=01`, output follow `i1`.
- When `sel=10 or 11`, it is latching on to the value of `y` which it was before.

### Screenshot6: Synthesis Diagram

<img width="1920" height="1080" alt="06_incomp_case_synthesis" src="https://github.com/user-attachments/assets/3a9d9f87-a8f3-46ce-82b4-3bba1fe2683d" />

The Yosys schematic shows that the incomplete case requires a latch.

## 7. Lab 4 – Complete Case Statement

A complete case statement assigns a value for every possible condition.

```verilog
module incomp_case(
          input i0,
          input i1,
          input i2,
          input[1:0]sel,
           output y
);

always @(*)
begin
    case (sel)
        2'b00: y = i0;
        2'b01: y = i1;
        default: y = i2;
    endcase
end

endmodule
```

The default branch handles the remaining input combinations.

### Screenshot7: RTL Simulation

<img width="1920" height="1080" alt="07_comp_case_simulation" src="https://github.com/user-attachments/assets/a1a1142c-00d6-4ffe-bf90-f4d34eaec89b" />

### Observations:

- When `sel=00`, output follow `i0`.
- When `sel=01`, output follow `i1`.
- When `sel=10 or 11`, output follow `i2`.
- There is no latching.

### Screenshot8: Synthesis Diagram

<img width="1920" height="1080" alt="08_comp_case_synthesis" src="https://github.com/user-attachments/assets/19c0e5fc-48f0-4653-b687-72392a7616e6" />

The Yosys schematic shows that the complete case can be implemented using combinational logic.

## 8. Lab 5 – Partial Assignments in Case Statements

Even when a default branch is present, a latch can still be inferred if every output is not assigned in every branch.

```verilog
module partial_case_assign(
          input i0,
          input i1,
          input i2,
          input[1:0]sel,
          output reg y,
          output reg x
);

always @(*)
begin
    case (sel)
        2'b00: begin
            y = i0;
            x = i2;
        end

        2'b01: begin
            y = i1;
        end

        default: begin
            y = i2;
            x = i1;
        end
    endcase
end

endmodule
```

In this example, `y` receives a value in all branches, whereas `x` is left unassigned when `sel = 2'b01`.

Even though a default branch is included, it does not handle the missing assignment of `x` in the `01` branch. As a result, `x` retains its previous value for that condition, causing synthesis to infer a latch for `x`.

### Screenshot9: Synthesis Diagram

<img width="1920" height="1080" alt="09_partial_assign_simulation" src="https://github.com/user-attachments/assets/62a6e65e-0044-4a2a-976e-5107f820390d" />

## 9. Lab 6 – Bad Case and Overlapping Conditions

```verilog
module bad_case (
    input i0,
    input i1,
    input i2,
    input i3,
    input [1:0] sel,
    output reg y
);

always @(*)
begin
    case (sel)
        2'b00: y = i0;
        2'b01: y = i1;
        2'b10: y = i2;
        2'b1?: y = i3;
    endcase
end

endmodule
```

### Understanding Case Pattern Matching

In a normal case statement, the selector must match the specified value exactly. The `?` symbol is not considered a general wildcard in a regular case.

To support wildcard matching, we use casez.

**Verilog Example:**

```verilog
casez (sel)
    2'b00: y = i0;
    2'b01: y = i1;
    2'b10: y = i2;
    2'b1?: y = i3;
endcase
```

### Explanation:

Here, `2'b1?` matches both `2'b10` and `2'b11`. Therefore, it overlaps with the `2'b10` condition.

When multiple patterns match the same input, the simulator executes the first matching case item. Synthesis tools may optimize the logic differently, potentially creating a mismatch between simulation and synthesized hardware.

### Screenshot10: RTL simulation

<img width="1920" height="1080" alt="10_bad_case_simulation" src="https://github.com/user-attachments/assets/6a378da1-73df-4cd3-b304-43d2a5172c5e" />

### Observations:

- When it is becoming `11` it is latching to 1 it is not following neither `i2` or `i3` it is confusing and latching `1`.
- this can be solved using GLS.

### Screenshot11: Gate Level Simulation

<img width="1920" height="1080" alt="11_bad_case_GLS" src="https://github.com/user-attachments/assets/1a9b8c23-f6e5-436d-acc9-958d248cbeb3" />

### Screenshot12: Synthesis Diagram

<img width="1920" height="1080" alt="12_bad_case_synthesis" src="https://github.com/user-attachments/assets/f54ce10d-5d31-43a6-9806-7eb983028abc" />

## 10. Looping Constructs

Verilog provides looping constructs to simplify RTL coding and describe repeated hardware operations.

There are two important looping constructs:

### Difference Between For Loop and For Generate

| For Loop | For Generate |
|----------|--------------|
| Used inside an `always` block. | Used outside an `always` block. |
| Repeats procedural statements. | Creates multiple hardware structures. |
| Uses an integer variable as the loop counter. | Uses a `genvar` variable. |
| Mainly used for MUX and DEMUX logic. | Mainly used for repeated module instantiation. |

## 11. Procedural For Loops

A Procedural `for` loop is used inside an `always` block to execute statements repeatedly.

It simplifies the description of combinational logic.

### Syntax

```verilog
integer k;

always @(*)
begin
    for (k = 0; k < N; k = k + 1)
    begin
        // Repeated operation
    end
end
```

During synthesis, the loop is expanded into individual hardware operations rather than creating a physical counter.

For instance, it can be used to pick specific bits from a bus or to design repeated MUX/DEMUX circuits.

This method can also be applied to larger circuits, such as a 256:1 MUX, by modifying the input size and loop limits instead of writing each selection condition separately.

## 12. Lab 1 - Multiplexer Using For Loop

**Verilog Code – mux_generate.v**

```verilog
module mux_generate (
    input i0, input i1, input i2, input i3,
    input [1:0] sel,
    output reg y
);

wire [3:0] i_int;
assign i_int = {i3, i2, i1, i0};

integer k;

always @(*) begin
    for (k = 0; k < 4; k = k + 1) begin
        if (k == sel)
            y = i_int[k];
    end
end

endmodule
```

### Working

- The four input signals are combined into a 4-bit vector.
- The for loop checks each input position.
- The selector determines which input is passed to the output.

### Screenshot13: RTL Simulation

<img width="1920" height="1080" alt="13_mux_generate_simulation" src="https://github.com/user-attachments/assets/1555d0c5-982d-40d0-8ddd-08f02ea069d2" />

### Waveform Analysis

- When sel = 00, output follows i0.
- When sel = 01, output follows i1.
- When sel = 10, output follows i2.
- When sel = 11, output follows i3.

**Result:** The 4:1 MUX operates correctly using a Verilog for loop.

### Screenshot14: Synthesis Diagram

<img width="1920" height="1080" alt="14_mux_generate_synthesis" src="https://github.com/user-attachments/assets/4cc94a6f-0e0b-4b26-85af-43b9c564b7f2" />

The Yosys schematic shows the synthesized hardware.

In this experiment, the schematic shows a latch because the output is not assigned a default value before the loop. If no loop iteration assigns `y`, the previous value must be retained.

## 13. Lab 2 - DEMUX Using For Loop

A DEMUX distributes one input to one of several outputs based on the selector.

An 1:8 DEMUX can be implemented using a for loop.

### DEMUX Using Generate-Style Loop

**Verilog Code – demux_generate.v**

```verilog

reg [7:0] y_int;

assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;

integer k;

always @(*) begin
    y_int = 8'b0;

    for (k = 0; k < 8; k = k + 1) begin
        if (k == sel)
            y_int[k] = i;
    end
end

endmodule
```

### Working

- The input is distributed to one of eight outputs.

- The selector chooses the output position.

- All other outputs remain zero.

### Screenshot15: RTL SImulation

<img width="1920" height="1080" alt="17_demux_generate" src="https://github.com/user-attachments/assets/bd4ca9ab-6fff-4be1-ad24-ba2edec82520" />

### Screenshot16:Synthesis Diagram

<img width="1920" height="1080" alt="18_demux_generate_synthesis" src="https://github.com/user-attachments/assets/0ae838be-ce66-45e2-9299-51b90e484b7f" />

## 14. Lab 3 - DEMUX Using Case

The same DEMUX functionality can also be described using a case statement.

**Verilog Code – demux_case.v**

```verilog
module demux_case (
    input i,
    input [2:0] sel,
    output o0, o1, o2, o3,
    output o4, o5, o6, o7
);

reg [7:0] y_int;

assign {o7,o6,o5,o4,o3,o2,o1,o0} = y_int;

always @(*) begin
    y_int = 8'b00000000;

    case (sel)
        3'b000: y_int[0] = i;
        3'b001: y_int[1] = i;
        3'b010: y_int[2] = i;
        3'b011: y_int[3] = i;
        3'b100: y_int[4] = i;
        3'b101: y_int[5] = i;
        3'b110: y_int[6] = i;
        3'b111: y_int[7] = i;

        default: y_int = 8'b00000000;
    endcase
end

endmodule
```

### Screenshot17: RTL Simulation

<img width="1920" height="1080" alt="15_demux_case_simulation" src="https://github.com/user-attachments/assets/a435296f-be9b-45ae-8734-8a8c2e9c1f8f" />

### Screenshot18: Synthesis Diagram

<img width="1920" height="1080" alt="16_demux_case_synthesis" src="https://github.com/user-attachments/assets/186de0ab-1235-4c19-84ce-9dadaf1184bd" />

The initial assignment y_int = 8'b00000000 ensures that all outputs have a default value before the case statement executes. Therefore, no latch is inferred.

## 15.  For Generate Construct

The generate-for construct is used to create multiple hardware instances automatically.

It is written outside procedural always blocks.

It is particularly useful when the same module or logic needs to be instantiated multiple times.

### Syntax

```verilog
genvar i;

generate
    for (i = 0; i < N; i = i + 1) begin
        // Hardware instantiation
    end
endgenerate
```

### Important Points

- Generate constructs are evaluated during elaboration.

- genvar is used as the loop variable.

- It creates repeated hardware structures.

- It reduces repetitive module instantiation.

- It is useful for adders, registers, and other repeated structures.

## 16. Ripple Carry Adder Using Generate

A Ripple Carry Adder is constructed by connecting multiple Full Adders.

The carry output of one Full Adder becomes the carry input of the next Full Adder.

### Full Adder – fa.v

```verilog
module fa (
    input a, b, c,
    output co, sum
);

assign {co, sum} = a + b + c;

endmodule
```

### 8-bit Ripple Carry Adder – rca.v

```verilog
module rca (
    input [7:0] num1,
    input [7:0] num2,
    output [8:0] sum
);

wire [7:0] int_sum;
wire [7:0] int_co;

genvar i;

generate
    for (i = 1; i < 8; i = i + 1) begin
        fa u_fa (
            .a(num1[i]),
            .b(num2[i]),
            .c(int_co[i-1]),
            .co(int_co[i]),
            .sum(int_sum[i])
        );
    end
endgenerate

fa u_fa_0 (
    .a(num1[0]),
    .b(num2[0]),
    .c(1'b0),
    .co(int_co[0]),
    .sum(int_sum[0])
);

assign sum[7:0] = int_sum;
assign sum[8] = int_co[7];

endmodule
```

### Working

- The first Full Adder receives a carry input of zero.

- The generate loop instantiates the remaining seven Full Adders.

- Each Full Adder receives the previous stage's carry.

- The final output contains eight sum bits and one carry bit.

### Screenshot19: RTL Simulation

<img width="1920" height="1080" alt="19_rca_simulation" src="https://github.com/user-attachments/assets/79fb7b4c-826d-49fa-97a6-f3d189db2432" />


Result: An 8-bit Ripple Carry Adder is implemented using the generate-for construct.

### Screenshot20: Synthesis Diagram 

<img width="1920" height="1080" alt="20_rca_synthesis" src="https://github.com/user-attachments/assets/a2efcf7f-bf26-45bb-b4fe-2b5d451b5570" />

 ## 17. Key Learnings

- if-else statements are used to implement priority-based selection logic.
- Incomplete assignments in combinational blocks may lead to latch inference.
- Complete case statements help prevent unintended latch generation.
- All outputs should be assigned in every branch of combinational logic.
- Overlapping wildcard patterns may result in unexpected synthesis behavior.
- Procedural for loops are used to repeat operations within always blocks.
- Generate loops are used to create multiple hardware instances.
- MUX and DEMUX circuits can be implemented using different RTL coding techniques.
- Ripple Carry Adders (RCA) demonstrate repeated Full Adder instantiation.
- RTL coding style plays an important role in determining the synthesized hardware structure.

## 18. Tools Used

| Tool | Purpose |
|------|---------|
| Verilog HDL | Writing RTL code |
| Icarus Verilog | Compiling and simulating Verilog designs |
| GTKWave | Viewing and analyzing simulation waveforms |
| Yosys | Synthesizing RTL designs into hardware logic |
| ABC | Optimizing logic and mapping gates |
| SKY130 Standard Cell Library | Mapping the design to standard cells |


## 19. Conclusion

Day 5 helped in understanding the impact of Verilog coding styles on hardware synthesis.

The session covered priority logic, incomplete conditional statements, inferred latches, and the importance of complete assignments in combinational circuits.

It also demonstrated how for loops simplify procedural RTL coding and how for-generate constructs help instantiate repeated hardware structures.

Practical implementations of a 4:1 MUX, 1:8 DEMUX, and 8-bit Ripple Carry Adder helped understand the relationship between RTL coding, simulation, and synthesized hardware.

































