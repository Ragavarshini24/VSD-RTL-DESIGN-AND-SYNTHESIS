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





