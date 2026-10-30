## Day 1 – Introduction to Verilog RTL Design and Synthesis      
##  Overview         
This introduces the fundamentals of Verilog RTL design, testbenches, simulation, and the RTL simulation flow using open-source tools.                                           
It explains how a digital hardware design is described using Verilog, how a testbench verifies the design, and how a simulator executes the design to generate waveform results.

## Objectives
- Understand the basics of RTL design.     
- Learn what a Verilog simulator is.     
- Understand the purpose of a testbench.         
- Learn how RTL design and testbench work together.             
- Understand the Icarus Verilog simulation flow.             
- Observe signal transitions using GTKWave.

## Tools and Technologies

The following tools and technologies were used to perform RTL design, functional simulation, waveform analysis, and synthesis.

| **Tool / Technology** | **Purpose** |
|---|---|
| Verilog HDL | RTL Design |
| Icarus Verilog | Functional Simulation |
| GTKWave | Waveform Analysis |
| Yosys | RTL Synthesis |
| SKY130 PDK | Standard Cell Technology |
| Liberty (.lib) | Standard Cell Characterization |
| Oracle VirtualBox | Virtualization Platform |
| Linux (VSD Squadron) | Development Environment |

## 1.Introduction to Verilog RTL Design                                                                                              
### RTL Design                                                                                              
RTL stands for Register Transfer Level. It describes the operation and data transfer of digital hardware using a hardware description language such as Verilog.            
RTL code specifies how a digital circuit behaves in response to its inputs, clocks, and control signals.                        
### Testbench    
A testbench is a Verilog module used to verify the functionality of an RTL design.It provides input stimulus to the design under test (DUT), observes the outputs, and helps check whether the design behaves as expected.   

A testbench is mainly used for simulation and is generally not synthesized into the final hardware.    
### Simulator     
A simulator is a software tool that executes the RTL design along with the testbench to verify the design's behavior over time.It helps identify functional errors before implementing the design in hardware.                        
**Tool used:** Icarus Verilog (Iverilog)  

### Icarus Verilog Simulation Flow                
1.**Design Input:** Load the Verilog RTL code and testbench into Icarus Verilog.                                                        
2.**Compilation:** Compile both files to create the simulation executable.                                                                              
3.**Simulation Execution:** Run the executable to evaluate the design using the testbench inputs.                
4.**Waveform Generation:** Record signal value changes in a VCD file during simulation.                                                     
5.**Waveform Visualization:** Open the VCD file in GTKWave to observe signal transitions and verify the design's operation.                                         

**FLOW:**

       RTL Design
            |
            v
       +-----------+
       |           |
       | Icarus    | <---- Testbench
       | Verilog   |
       |           |
       +-----------+
            |
            v
         VCD File
            |
            v
        +---------+
        | GTKWave |
        +---------+
            |
            v
      Waveform Analysis
## 2. Lab 1: Repository Setup and RTL Simulation

### Step 1: Cloning the Workshop Repository                                                  
To begin the lab, I downloaded the required SKY130 RTL Design and Synthesis Workshop repository from GitHub. This repository contains the Verilog design files and other resources needed for the lab activities.  

I used the following commands:            
```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop
ll
```
**Sc1:  Repository  Directory Contents**
<img width="1920" height="1080" alt="Screenshot 2026-10-30 123509" src="https://github.com/user-attachments/assets/ada5b40e-fd38-4e20-be14-e1ac82af4534" />
After cloning the repository,it will create directory with sky130RTLDesignAndSynthesisWorkshop and it shows the contents of the cloned workshop repository.     
### Step 2: Accessing the Verilog Files       

Next, I moved into the verilog_files directory to access the RTL design files and their related testbenches.                
```bash
cd verilog_files
ls
```
**Sc2:Verilog_files**  

<img width="1920" height="1080" alt="Screenshot 2026-10-30 131625" src="https://github.com/user-attachments/assets/d42082b9-f963-4237-b88a-b3768dd59e83" />  

This directory contains the Verilog source files used to describe the digital circuits, along with testbench files that help verify their functionality through simulation.                            
### Step 3: 2:1 Multiplexer Simulation Using Icarus Verilog  
I used a 2:1 multiplexer design to perform RTL simulation. The Verilog design and its corresponding testbench were loaded into Icarus Verilog for compilation and simu lation.

**Files used:**
- Design file: good_mux.v                                  
- Testbench file: tb_good_mux.v

**1. Compiling the Design**  
 
  First, I loaded the multiplexer design and testbench into Icarus Verilog using the following command:
  ```bash
  iverilog good_mux.v tb_good_mux.v
  ```
The compiler checks both Verilog files and generates an executable file named a.out if the compilation is successful.

**2. Running the Simulation**

After successful compilation, I executed the generated file using:

```bash
./a.out
```
when we execute,it dump the vcd file named **tb_good_mux.vcd** to store the signal transitions during simulation.  

**3. Viewing the Simulation Waveform**

To observe the signal changes graphically, I opened the generated VCD file using gtkwave.
gtkwave is a command to launch the waveform.
```bash
gtkwave tb_good_mux.vcd
```
**Sc3:good_mux waveform**

<img width="1920" height="1080" alt="Screenshot 2026-10-30 133610" src="https://github.com/user-attachments/assets/305a13e6-dbd8-41d3-9178-f088341a50ba" />


The simulation waveform confirms the expected working of the 2:1 multiplexer:

- When sel = 0, the output y follows input i0.

- When sel = 1, the output y follows input i1.

The observed waveform verifies that the multiplexer operates according to its truth table.

### Step 4: Exploring the Verilog Design and Testbench Files 

After completing the simulation, I explored the Verilog files to understand how the multiplexer was designed and how its functionality was verified using the testbench.

To view both files together, I used the following command in the Ubuntu terminal:

```bash
gvim tb_good_mux.v -o good_mux.v
```
This command opens the two Verilog files in separate horizontal windows in the gvim editor.

- tb_good_mux.v:** Contains the testbench code, which provides input signals, applies test conditions and checks the behavior of the multiplexer.

- good_mux.v:** Contains the actual RTL implementation of the 2:1 multiplexer, including its input, select and output signals.

**Sc4: Verilog Design and Testbench Files in gvim** 


<img width="1920" height="1080" alt="Screenshot 2026-10-30 152725" src="https://github.com/user-attachments/assets/fbd96c04-1ba2-4105-be0d-99a95320e543" />


## 3. Introduction to Yosys and Logic Synthesis         
### What Does a Synthesizer Do?

A synthesizer is a tool that converts an RTL (Register Transfer Level) design written in Verilog into a hardware representation using logic gates and standard cells.

In this, Yosys is used to perform RTL synthesis. It helps convert the Verilog design into a gate-level netlist that can be implemented using cells from a selected technology library.

### Understanding Logic Synthesis
Logic synthesis is the process of transforming a Verilog design into an equivalent hardware circuit.

The main steps involved in synthesis are:

**RTL Reading:** The Verilog source code is loaded and analyzed.

**Logic Optimization:** The design is simplified to remove unnecessary logic and improve efficiency.

**Technology Mapping:** The optimized logic is mapped to suitable standard cells from the target library.

**Netlist Generation:** A gate-level netlist is produced to represent the synthesized circuit.

### Understanding the .lib File

A .lib file, also known as a Liberty file, contains information about the standard cells available in a particular technology library.

It provides important details such as:

- Cell area and power information.                           
- Timing characteristics and delays.                           
- Input and output pin details.                               
- Different cell versions with varying drive strengths.                   

This information helps synthesis and timing analysis tools select appropriate cells for the design.

## 4.Lab 2: Synthesis of a 2:1 Multiplexer Using Yosys

To perform RTL synthesis of a 2:1 multiplexer using Yosys and map the logic to the SKY130 standard-cell library. The synthesized logic is then viewed and written into a Verilog netlist.

### Synthesis Procedure

First, open the Ubuntu terminal and navigate to the project directory. Start the Yosys tool using the following command:   

```bash
yosys
```
After entering the Yosys environment, perform the following steps.  

### Step 1: Read the SKY130 Standard-Cell Library   
```bash
read_liberty -lib ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
This command loads the SKY130 standard-cell library into Yosys. The library contains the cell definitions required for technology mapping.                       

### Step 2: Read the RTL Design  

```bash
read_verilog good_mux.v
```
This command reads the Verilog RTL file of the 2:1 multiplexer. The design is loaded into Yosys for further processing. Make sure the file is read without errors.

### Step 3: Perform Logic Synthesis

```bash
synth -top good_mux
```
This command synthesizes the RTL design by converting the Verilog description into a logic-level representation. The top module is specified as good_mux.                      
### Step 4: Technology Mapping Using ABC 
``` bash
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```
ABC is used for logic optimization and technology mapping. It converts the synthesized logic into suitable standard cells available in the SKY130 library.

### Step 5: View the Synthesized Logic

```bash
show
```
This command opens a graphical representation of the synthesized multiplexer logic. It helps to understand how the RTL design is represented using logic gates.

**Sc5:Synthesized Logic Diagram:**

<img width="1920" height="1080" alt="Screenshot 2026-10-30 165801" src="https://github.com/user-attachments/assets/e556ff0b-6397-4250-a929-0830b814e5a2" />

Shows the graphical representation of the synthesized multiplexer after logic optimization and technology mapping.

### Step 6: To write the Netlist

```bash
write_verilog -noattr good_mux_netlist.v
```
This command generates the synthesized Verilog netlist and saves it as good_mux_netlist.v. The netlist represents the design using the mapped standard cells.

To view the generated netlist in GVim , use
```bash
!gvim good_mux_netlist.v
```

This generates the technology-mapped Verilog netlist.


<img width="1920" height="1080" alt="Screenshot 2026-10-30 171722" src="https://github.com/user-attachments/assets/ea6adafe-e758-4d6c-970e-a4c7ead16f9d" />  

## 5.Results and Observations
The implementation of the project helped verify the different stages of the digital design and synthesis flow. The following observations were made during the practical work:

**1.RTL Development:** The required digital circuit was described using Verilog HDL.

**2.Testbench Preparation:** A testbench was created to provide different input combinations to the design.

**3.Functional Simulation:** The simulation results were examined to confirm that the circuit behaved as expected.

**4.Waveform Observation:** Input and output signal transitions were analyzed using waveform visualization.

**5.RTL Synthesis:** Yosys successfully processed the Verilog design and converted it into an intermediate logic representation.

**6.Logic Optimization:** Unnecessary logic was optimized to obtain a simplified implementation.

**7.Technology Mapping:** ABC mapped the optimized logic to suitable cells from the SKY130 standard-cell library.

**8.Netlist Generation:** The mapped design was exported as a gate-level Verilog netlist.

**9.Post-Synthesis Verification:** The generated netlist was inspected to check the synthesized circuit structure and cell connections.

## 6.Conclusion

Through this project, I gained hands-on experience with RTL design, functional simulation, and logic synthesis using open-source VLSI tools.

The design flow helped me understand how a Verilog description is transformed into an optimized gate-level netlist using Yosys and the SKY130 standard-cell library. I also gained knowledge of Liberty library files, technology mapping, and checking the synthesized output.

Overall, this project strengthened my understanding of the digital design flow and provided a good starting point for exploring advanced ASIC design and physical implementation.


















