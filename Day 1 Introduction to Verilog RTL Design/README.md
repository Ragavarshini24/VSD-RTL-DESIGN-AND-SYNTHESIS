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
## 1.Introduction to Verilog RTL Design                                                                                              
## RTL Design                                                                                              
RTL stands for Register Transfer Level. It describes the operation and data transfer of digital hardware using a hardware description language such as Verilog.            
RTL code specifies how a digital circuit behaves in response to its inputs, clocks, and control signals.                        
## Testbench    
A testbench is a Verilog module used to verify the functionality of an RTL design.It provides input stimulus to the design under test (DUT), observes the outputs, and helps check whether the design behaves as expected.   

A testbench is mainly used for simulation and is generally not synthesized into the final hardware.    
## Simulator     
A simulator is a software tool that executes the RTL design along with the testbench to verify the design's behavior over time.It helps identify functional errors before implementing the design in hardware.                        
**Tool used:** Icarus Verilog (Iverilog)  

## Icarus Verilog Simulation Flow                
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

## Step 1: Cloning the Workshop Repository                                                  
To begin the lab, I downloaded the required SKY130 RTL Design and Synthesis Workshop repository from GitHub. This repository contains the Verilog design files and other resources needed for the lab activities.  

I used the following commands:            
```bash
git clone https://github.com/kunalg123/sky130RTLDesignAndSynthesisWorkshop.git
cd sky130RTLDesignAndSynthesisWorkshop
ls -la
```
**Sc1:  Repository  Directory Contents**
<img width="1920" height="1080" alt="Screenshot 2026-10-30 123509" src="https://github.com/user-attachments/assets/ada5b40e-fd38-4e20-be14-e1ac82af4534" />
After cloning the repository,it will create directory with sky130RTLDesignAndSynthesisWorkshop and it shows the contents of the cloned workshop repository.     
## Step 2: Accessing the Verilog Files       

Next, I moved into the verilog_files directory to access the RTL design files and their related testbenches.                
```bash
cd verilog_files
ls
```
**Sc2:Verilog_files**  

<img width="1920" height="1080" alt="Screenshot 2026-10-30 131625" src="https://github.com/user-attachments/assets/d42082b9-f963-4237-b88a-b3768dd59e83" />  

This directory contains the Verilog source files used to describe the digital circuits, along with testbench files that help verify their functionality through simulation.                            


