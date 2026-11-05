## Day 3 : Combinational and Sequential Optimization

##  Introduction to Logic Optimizations

 In Digital Logic,there are two types of logic.
  - Combinational Logic
  - Sequential Logic

## 1. Combinational Logic Optimization

Combinational logic circuits produce outputs based on their present inputs.

Optimization techniques simplify the Boolean expressions and remove unnecessary logic elements.

### Types of Combinational Optimization

- **Constant Propagation**

   Constant propagation is a technique in which constant input values are propagated through the logic circuit.

   When an input is fixed to either logic 0 or logic 1, the circuit can be simplified by eliminating unnecessary logic gates.

   This reduces the number of cells required to implement the circuit.

- **Boolean Logic Optimization**

   Boolean logic optimization simplifies complex Boolean expressions using Boolean algebra and logic minimization techniques.

   The synthesis tool identifies redundant logic and converts the design into a simpler equivalent circuit.

   Techniques include:

    - Boolean algebra

    -  Karnaugh Maps (K-Maps)

    -  Quine–McCluskey algorithm

 ### Tools Used

- RTL synthesis and logic optimization

- SKY130 Standard Cell Library – Technology library for synthesis

- GTKWave – Viewing simulation waveforms, if required

### Lab 1: `opt_check`

The multiplexer has one input connected to logic 0. After Boolean simplification, the circuit is represented by an AND gate.

Commands used:

```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog opt_check.v
synth -top opt_check
opt_clean -purge
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

The opt_clean -purge pass eliminates unused cells and wires from the synthesized design.

<img width="1920" height="1080" alt="1_opt_check" src="https://github.com/user-attachments/assets/ac8c89c4-418a-49ba-986e-5a6754266581" />

### Lab 2: `opt_check2`

A constant logic 1 at one multiplexer input allows the Boolean expression to be simplified into an OR gate, based on the input and select connections.

<img width="1920" height="1080" alt="2_opt_check2" src="https://github.com/user-attachments/assets/e3ce09bf-b181-4b7d-a90e-b469946ecb77" />

### Lab 3: `opt_check3`

The circuit contains multiplexer inputs connected to ground. Constant propagation simplifies the logic, resulting in a three-input AND gate.

<img width="1920" height="1080" alt="3_opt_check3" src="https://github.com/user-attachments/assets/edd85143-b528-4889-9305-60b739c2110f" />

### Lab 4: `opt_check4`

Yosys performs Boolean simplification on the original design and generates an equivalent XNOR-based logic implementation.

<img width="1920" height="1080" alt="4_opt_check4" src="https://github.com/user-attachments/assets/54df1f6e-3fa1-4adb-b59c-0140a71ecd63" />

### Lab 5: `multiple_module`

The design consists of interconnected Verilog modules. Flattening removes the module hierarchy, and optimization reduces the resulting logic to a single cell.

<img width="1920" height="1080" alt="5_multiple_module" src="https://github.com/user-attachments/assets/a51f615f-b441-4b5a-981f-8ed7864bc7f3" />

### Lab 6: `multiple_module_opt2`

The second multiple-module design is synthesized using flattening and optimization passes to obtain a simplified logic implementation.

<img width="1920" height="1080" alt="6_multiple_module_opt2" src="https://github.com/user-attachments/assets/97553557-c0f2-434c-9e5f-f3d04221b554" />

## 2. Sequential Logic Optimization

Sequential logic optimization focuses on improving circuits that contain flip-flops and registers without changing their intended functionality.

The following techniques are used:

**Constant Propagation:** Simplifies sequential circuits by replacing known constant values and eliminating unnecessary logic.

**Retiming**: Rearranges registers across combinational logic to improve timing performance while maintaining the required circuit behavior.

**Sequential Logic Cloning**: Creates copies of registers to reduce fanout and improve timing in critical paths.

**State Optimization:** Eliminates redundant states and simplifies state representation to reduce circuit complexity.

Sequential optimization helps improve circuit timing, reduce unnecessary hardware, and achieve efficient implementation while preserving the original functionality.

### D Flip-Flop Constant Optimization

### 2.1 dff_const1

```verilog
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
    if(reset)
        q <= 1'b0;
    else
        q <= 1'b1;
end
endmodule
```

This example demonstrates constant propagation in a flip-flop with an asynchronous reset and constant data input.

- The reset polarity and input conditions determine the output behavior.

- Simulation is used to observe the flip-flop response.

- Synthesis helps analyze whether the sequential logic can be simplified.

**GTKWave Simulation:**

<img width="1920" height="1080" alt="7_diff_const_gtk" src="https://github.com/user-attachments/assets/2705d14c-640e-4fe4-91ec-6677888360d9" />

**Synthesized logic diagram:**

<img width="1920" height="1080" alt="8_diff_const" src="https://github.com/user-attachments/assets/6bfdd475-1678-48c3-9970-6a319849e212" />

### 2.2 dff_const2

This example demonstrates the optimization of a flip-flop with constant reset and data values.

- The constant input conditions result in a constant output.

- Yosys identifies the redundant sequential logic during synthesis.

- The flip-flop can be removed and replaced with a constant connection.

**GTKWave Simulation:**

<img width="1920" height="1080" alt="9_diff_const2_gtk" src="https://github.com/user-attachments/assets/cb6c8aa5-c22b-4bb6-ac1e-c72919051b29" />

**Synthesized logic diagram:**

<img width="1920" height="1080" alt="10_diff_const2" src="https://github.com/user-attachments/assets/0669c18e-cd93-4337-a317-e44f49604bba" />

### 2.3 dff_const3

```verilog
module dff_const3(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
    if(reset)
    begin
        q <= 1'b1;
        q1 <= 1'b0;
    end
    else
    begin
        q1 <= 1'b1;
        q <= q1;
    end
end
endmodule
```

This example demonstrates the interaction between two flip-flops, where the output of the first flip-flop is connected to the set input of the second flip-flop.

- When reset is activated, the first flip-flop updates its output based on its reset condition.

- The second flip-flop responds to the previous output value at the active clock edge.

- The sequential dependency between the registers affects their output behavior.

- Due to this dependency, both flip-flops cannot be directly replaced with constant values during synthesis.

**GTKWave Simulation:**

<img width="1920" height="1080" alt="11_diff_const 3 _gtk" src="https://github.com/user-attachments/assets/0bf2f4fd-5fbc-42fa-87b0-b00384a61d19" />

**Synthesized logic diagram:**

<img width="1920" height="1080" alt="12_diff_const3" src="https://github.com/user-attachments/assets/ccc04381-c2c5-46be-96a4-e0556d480ad8" />

### 2.4 dff_const4

```verilog
module dff_const4(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
    if(reset)
    begin
        q <= 1'b1;
        q1 <= 1'b1;
    end
    else
    begin
        q1 <= 1'b1;
        q <= q1;
    end
end
endmodule
```

This example demonstrates constant propagation in sequential logic using reset and set conditions.

- The reset and set inputs force the flip-flops to known constant values.

- Yosys identifies the constant behavior during synthesis.

- The redundant sequential elements are removed.

- The optimized design contains constant connections instead of flip-flops.

**GTKWave Simulation:**

<img width="1920" height="1080" alt="13_diff_const4_gtk" src="https://github.com/user-attachments/assets/d5169f44-3eb6-4172-8039-cc169635d577" />

**Synthesized logic diagram:**

<img width="1920" height="1080" alt="14_diff_const4" src="https://github.com/user-attachments/assets/7d49db22-f88f-4656-ad72-f2e3caabf111" />

### 2.5 dff_const5

```verilog
module dff_const5(input clk, input reset, output reg q);
reg q1;

always @(posedge clk, posedge reset)
begin
    if(reset)
    begin
        q <= 1'b0;
        q1 <= 1'b0;
    end
    else
    begin
        q1 <= 1'b1;
        q <= q1;
    end
end
endmodule
```

This example explores the effect of reset conditions on sequential logic optimization.

- Both reset signals are assigned a value of 0.

- The resulting flip-flop behavior differs from the previous example.

- The registers retain their sequential functionality.

- Yosys preserves the flip-flops in the synthesized design instead of replacing them with constant values.

**GTKWave Simulation:**

<img width="1920" height="1080" alt="15_diff_const5" src="https://github.com/user-attachments/assets/f47fec37-f864-4b5d-85b3-87155ead497a" />

**Synthesized logic diagram:**

<img width="1920" height="1080" alt="16_diff_const5" src="https://github.com/user-attachments/assets/a23c8b0b-fbae-4195-813b-9d7b6d7f7c2f" />

## 3. Unused-Output Optimization

Synthesis tools remove logic that does not affect observable outputs or other required behavior. This reduces unnecessary hardware.

### 3.1 counter_opt

#### Verilog Code (`counter_opt.v`)

```verilog
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = count[0];

always @(posedge clk , posedge reset)
begin
    if(reset)
        count <= 3'b000;
    else
        count <= count + 1;
end

endmodule
```

This example demonstrates sequential logic optimization by removing unused counter bits.

- The design uses a 3-bit counter, but only count[0] is connected to the output q.

- The upper two bits do not contribute to the observable output.

- Yosys identifies and removes the unnecessary counter bits during synthesis.

- The optimized circuit retains only the required logic, consisting of a flip-flop and an inverter.

<img width="1920" height="1080" alt="17_counter_opt" src="https://github.com/user-attachments/assets/347153fc-8c1f-423b-bb86-ba9f6651e6cf" />

### 3.2 counter_opt2

#### Verilog Code (`counter_opt2.v`)

```verilog
module counter_opt (input clk , input reset , output q);
reg [2:0] count;
assign q = (count[2:0]== 3'b100);

always @(posedge clk , posedge reset)
begin
    if(reset)
        count <= 3'b000;
    else
        count <= count + 1;
end

endmodule
```

<img width="1920" height="1080" alt="18_counter_opt2" src="https://github.com/user-attachments/assets/4c702640-018f-4497-adf3-f8335508e862" />

## Key Learnings

- Learned combinational logic optimization using Boolean simplification and constant propagation.

- Understood the role of conditional operators in Verilog-based designs.

- Practiced synthesis of multiple-module designs using Yosys.

- Explored the effect of opt_clean -purge in removing unused logic.

- Studied constant propagation and optimization in D flip-flop designs.

- Understood how reset, set, and sequential dependencies influence register optimization.

- Observed how unused counter bits can be eliminated during synthesis.

- Learned that synthesis optimization reduces hardware complexity while preserving the required circuit functionality.

## Tools Used

- Verilog HDL
- Yosys
- Icarus Verilog
- GTKWave
- SKY130 standard-cell library

## Conclusion

Day 3 focused on combinational and sequential logic optimization using Verilog HDL and Yosys. Various designs, including conditional logic, multiple-module circuits, counters, and D flip-flops, were implemented and synthesized. The experiments demonstrated how constant propagation, Boolean simplification, and sequential optimization reduce unnecessary hardware while preserving the required functionality. This lab provided a better understanding of writing efficient RTL and analyzing synthesized circuit implementations.







