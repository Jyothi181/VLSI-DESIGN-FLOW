# VLSI-DESIGN-FLOW
In this repository, all vlsi design steps are implemented using open source tools in linux environment and few experiments were carried out to get familiar with the design steps.

# Introduction

VLSI (Very Large-Scale Integration) Design refers to the process of creating integrated circuits (ICs) by combining thousands to millions of transistors on a single chip. 
VLSI involves the integration of a large number of electronic components, such as transistors, resistors, capacitors, and other functional blocks, onto a single semiconductor chip. 
This allows for the creation of complex circuits that are smaller, faster, and more power-efficient than earlier generations.

![image](https://github.com/user-attachments/assets/1d09475a-bc9d-46ab-8077-bec55cdcd649)

- **VLSI Design Flow :** Methodology to design an IC such that it delivers the required functionality or behaviour. This process follows the divide and conquer.
- **Idea to RTL Flow :** It takes a high-level idea or concept of a product and represents the hardware portion of the implementation in RTL. Pre-RTL flow is system level design
- **RTL to GDS Flow :** It takes an RTL through various stages of logical and physical design steps and finally represents the design as GDS file.
- **GDS to chip processes :** It takes a GDS file and prepares masks for a given GDS and fabricate/ tests / package chips.

### Terminologies

- **RTL :** Modelling of circuit as flow of data (signal) between registers

- **Library:**  Timing, area and power information of standard cells and macros 

- Constraints: Design goals, expected timing behaviour, enviroment (SDC) 

- Design: Top level entity that represents the circuit ex:MYDESIGN

- Ports: The interfaces of the design through which it communicates with the external world ex: In1,In2,CLK,out1, out 2 

- Input ports: Signals going inside the the design ex In1,In2,CLK 

- Output ports: Signals going outside from the design ex:out1,out2.

- **Cells:** Basic entity delivering combinational or sequential contained in libraries. ex: ANZ, NOT, BUF, DFF design is composed of multiple cells connected together

- **Instances:** cells when used inside a design are called instances

- **Pin:** An Interface of a library cell (or) instance through which it communicates with the other components. Input pin or output pin based on direction of flow of signal to cell/instance

- **Nets:** The wire that connects diff instances and ports is called Nets.

# Idea to RTL Flow
**Pre-RTL Methodologies :**
1.	**Hardware-Software Partitioning :** Exploit the merits of both hardware and software by choosing right combination of hardware and software to implement a given function.
    Hardware usually runs as parallel circuits and can have very good PPA. Software runs sequentially on a general-purpose processor.
3.	**Functional Specification to RTL:** Functional Specification can be made at a higher level of abstraction. It needs to be converted to RTL.
    Functional specification can be done and converted to RTL by 3 different methods.
      - **Manual coding :** straightforward. Coding can be Verilog or VHDL.
      - **IP Assembly :** reusing existing RTL
      - **Behaviour Synthesis :** Automatic method of generating RTL from high-level languages. Process of converting an algorithm (not timed) to an equivalent design in RTL (fully timed) and satisfy the specify constraints.
        It is also called High-level Synthesis

![image](https://github.com/user-attachments/assets/121f2880-070d-49a9-9f9d-88deda0ea051)
  
# RTL to GDS Flow
## Logic Synthesis 

It is the process by which RTL is converted to an equivalent circuit as inter connection of logic gates (netlist-output) represented using verilog constructs or schematic inputs to synthesis.

![image](https://github.com/user-attachments/assets/0293331c-f072-4806-ac10-c911eb2a66ed)


***RTL Synthesis:*** Initial part of logic synthesis consisting of translating an RTL to a netlist of generic logic gates.
•	A generic logic gate has a well defined Boolean function. ex:AND,NAND,XOR,MUX,latches
•	It does not have a fixed transistor-level implementation and also not have a well-defined area, delay, power attributes.

***Logic Optimization:*** Optimizations done on a generic gate netlist. It is typically area-driven.

***Technology Mapping:***
•	Map a netlist consisting of generic logic gates to the standard cells in the given technology Library and obtain a netlist & stdcells.
***Technology dependent Optimization:***
•	PPA can be estimated more accurately after technology mapping
•	Perform timing, area, power optimization over netlist of std cells


## Physical Design
It is the process by which a design in the form of a netlist is converted to an equivalent design in the form of layout or GPS(geometrical patterns of masks)

![image](https://github.com/user-attachments/assets/54b19452-efef-46ad-8b38-9d59962b3b9e)

### Inputs to PD

- Netlist input design (olp of logic synthesis)
- Library similar as in logic synthesis (Liberty).
- Abstract physical information of cells and technology specific information (LEF)
- Constraints similar to logic synthesis (SDC)

### Floorplan
It is the intial step of PD. Designer define the size and shape of die predefined placement information

### Chip Planning

-	Major decisions will be taken
-	Partitioning into subsystems (or) blocks and arrange the blocks on the die.
-	Allocation of area for the standard cells, macros and memory. Macro placement is also done.
-	Includes IO cell planning and power planning
-	We should ensure that voltage drop in power line should be within an acceptable limit

### Placement
-	Decides location of standard cells in the design 
-	Total wire length minimization. Ensure timing is met and reduce the delay of critical path.
-	Ensure there is no congestion. Placement of std cells is highly automative.

### CTS
-	Decides topology of clock network and how clock reaches each clocked element.
-	CTS also performs wiring of clock network (avoid detour since majority of routing resources are still unused.)

### Routing
-	Creates wire layout for all the nets (other than clock and power supply) satisfying certain constraints
-	The objective is use min wire-length, routing area, vias
-	It is very complicated process (too many nets and routing constraints)

#### Global Routing
-	Planning stage stage for routing. It is the first stage
-	Actual layout of wires not created 
-	A routing plan for a given net is created.
-	Entire routing region is partitioned into rectangular tiles or global bins.
-	Global routing assigns a set of global bins that will be used for making connections for a given net.
#### Detailed Routing
-	Decides actual layout of each net in the pre-assigned global bins.
-	The detailed router decides the actual physical inter-connections of nets by
        1.Allocating wires on each metal layer.
 	2.vias for switching between metal layers.

### ECO 
Engineering Change Order makes small final fixes in the design.

### Write GDS 
Dump the Layouts of each layer in a GDS file.

### Optimization
Between each PD task there are optimization steps. Small changes like buffer insertion on a given net, changing the size and placement of given cell and changing routing for a given net are done in the design to improve PPA. Small changes restricted to a small portions of a design so, that it does not create large disruptions in the design.

### Iterative flow 
-    PD implementation tasks performed along with verification tasks (timing, power, signal integrity etc.,)
-    PD implementation should ensure design closure(including timing closure)
-    Achieving design closure means that we have achieved state in which everything is fine in terms of properties that design should satisfy). This is a challenging task
-    PD is iterative. One task may require that previous tasks retract same design decisions.This creates loops.
-    Achieving design closure with minimum no. of iterations is the goal of a PD.

# BAMBU – High Level Synthesis
**Objective:** To generate a Verilog file from high level language by using High Level Synthesis 
**Tool used:**  
-> Any HLS tool can be used. 
-> Here, open-source tool Bambu HLS is used 
**Input files:** C file – tut1.c
## Steps to perform High Level Synthesis
- Change to the respective directory where bambu is installed.
- Bambu is installed in the UNIX system -> `./bambu-0.9.7.App Image`
- Input the C code for generating Verilog RTL using HLS Tool 
- Run the Bambu tool by giving C file as input and top fname is func
- Command : `./bambu-0.9.7.AppImage <path-to-c-file> --top-fname=<accelerator-function-to-be implemented-in-hardware>` 
- Ex: `./bambu-0.9.7.AppImage tut1.c --top-fname=func`

![image](https://github.com/user-attachments/assets/62046826-7ed2-4334-b455-d95f88e9b330)   ![image](https://github.com/user-attachments/assets/1da2af93-7a0e-4ff0-970d-c661f8d67158)


***Figure 1: Running Tool***

Verilog file after synthesis – func.v will be created in the directory after synthesis.
To open Verilog file – gedit func.v

![image](https://github.com/user-attachments/assets/fad64d7f-f318-4cb1-bf63-567892692109)

***Figure 2: Verilog File after synthesis***

# Verification
**Objective:** To demonstrate the simulation and the code coverage tool. Also, the working of the tool is explained with an example Verilog code.

**Tools used:**
- ICARUS for Verilog Simulation
- COVERED for computing code coverage
- GTKWave is an analysis tool used to perform debugging on Verilog or VHDL simulation models. Here, we will be using one of the dump file formats, Value Change Dump (VCD)

**Input Files:**
- Mycounter.v – Verilog code
- Testbench.v – Testbench

![image](https://github.com/user-attachments/assets/f06a9180-1050-42fc-98f4-eb55b457a543)

![image](https://github.com/user-attachments/assets/ff2ae5ec-4617-4103-b8e1-d43133990117)

## Simulation and viewing the output waveform
Launch the Linux distribution. Install Icarus Tool and change directory to icarus_codes . Include input files in the working directory.
- `cd icarus/icarus_codes`
To simulate by launching the iverilog tool, command is 
- `$ iverilog -o Mycounter Mycounter.v Testbench.v`
- `$ vvp Mycounter`

**Description:**
- -o tells the tool to save the output in my counter file. This command compiles the program
- vvp Mycounter – To execute the compiled program and outputs are stored in the dump file called count.vcd

- It is not convenient to view the output in the command prompt. So waveform analysis tool is used – GTKwave.
- `gtkwave count.vcd` - This command launches the GTKWave analyzer application.

![image](https://github.com/user-attachments/assets/079d548d-bce2-4ef5-b100-ae58b3e963c3)

***Figure: Verificaiton - GTK wave***

Select the Tesbench-I1 and then select each signal and insert to view the waveforms.In the left panel, expand the Testbench and click on the subfolder. It will expand to show input Clock, Reset and output waveform OUT[3:0]. Drag those to the signals panel and analyze the results.

## Generating the code coverage report: 
To estimate the percentage of RTL design tested by the test bench, COVERED Verilog Code Coverage Analyzer tool is used. Generate the code coverage report in the same directory i.e., icarus_codes by executing the following command

`$ covered score -t Testbench -v Testbench.v -v Mycounter.v -vcd count.vcd -o Mycounter.cdd`

![image](https://github.com/user-attachments/assets/ca95d55e-6fd4-46fa-8eff-310b5bb6854f)

To view the coverage report, execute `$ covered report -d v Mycounter.cdd` The coverage report is displayed in the terminal

![image](https://github.com/user-attachments/assets/8a431775-020f-433d-b695-bc5ccfff9993)  ![image](https://github.com/user-attachments/assets/d9953a72-3fb8-4737-8e59-a848bd7c2503)

***Figure : Code coverage report***

It is obtained that Code coverage is 100%

# Logical Synthesis

**Objective:** To demonstrate working of RTL synthesis tool with an example Verilog code.

**Tools used:** Yosys (Open SYnthesis Suite)

**Input files :** 
- verilog file: top.v
- script file: yosys_commands.tcl
- Technology library: NangateOpenCellLibrary_typical.lib, toy.lib

The Synopsys Design Constraint (SDC) file is not provided, so the synthesized netlist will be mapped for minimum area constraints.

![image](https://github.com/user-attachments/assets/1e391f01-27a8-44a6-9c94-c4fcf34c3964)

**TCLscript**
- Read modules from Verilog file -> `read_verilog top.v`
- Elaborate design hierarchy -> `hierarchy −check −top top`
- Translate processes to netlists -> `proc`
- Mapping to the internal cell library -> `techmap`
- Mapping flip−flops to NangateOpenCellLibrary -> `dfflibmap −liberty NangateOpenCellLibrary_typical.lib`
- Mapping logic to NangateOpenCellLibrary -> `abc −liberty NangateOpenCellLibrary_typical.lib`
- Remove unused cells and wires -> `clean`
- Write the current design to a Verilog file -> `write_verilog −noattr synth_example.v`

# Steps to perform the logic synthesis
Launch the Linux distribution and cd to the directory and load the input files in yosys_codes directory.
![image](https://github.com/user-attachments/assets/7e16b4c9-8cfb-4a62-b0d6-416542390fdb)

Launch the yosys tool -> `yosys`

![image](https://github.com/user-attachments/assets/ea13c099-b184-4cba-a2ef-ad283158b751)

You can individually run the commands listed or source tcl file.  yosys> `script yosys_commands.tcl` 
 To view the generated netlist, close the yosys tool with ctrl+Z. To view the netlist: `gedit synth_example .v`

![image](https://github.com/user-attachments/assets/6a45fd64-90e8-47ae-8172-294665ee7eb8)























# Technology Library
**Objective:** To understand how delay calculation and Static Timing Analysis (STA) are impacted by Technology Library and Constraints. Impact of Technology library and constraints on delay and also on STA
Concepts: 
Non-linear Delay Model (NLDM): Non-Linear Delay Models are used to estimate the delay of signal propagation through various circuit elements, such as interconnects, gates, and wires in a more accurate and complex way compared to linear models.
In STA, non-linear delay models are used to estimate the timing of a signal’s path through a circuit, considering the actual behaviour of the gate and interconnect delays.
These models offer more accurate delay estimations and are key for optimizing performance, meeting timing constraints, and ensuring the robustness of the final chip design.

Constraints: Constraints refer to the requirements, limitations, or conditions that a design must adhere to during the design and optimization process. These constraints guide the layout, timing, power, and functionality of a chip, and they are essential for ensuring that the final product meets all necessary performance, reliability, and manufacturability goals. Ex: Timing, power, area, manufacturing, clock, routing and functional constraints.
Some of the constraints are listed below
• create_clock: to create a clock reference for timing analysis 
• set_input_delay: to specify the delay in input before reaching the given circuit 
• set_output_delay: to model change in required time due to external circuit elements 
• set_input_transition: to specify transition/slew at the input port 
• set_load: to specify load capacitance at the output port 
• set_clock_uncertainty: to add pessimism to timing analysis

Requirements:  
Tool used:  Open STA
Input files : 
•	Design file: test.v 
•	OpenSTA script file: test.tcl 
•	Technology library: toy.lib
•	SDC file: test.sdc 
All input files are loaded into the directory.

![image](https://github.com/user-attachments/assets/5ec0a94f-8501-426d-90e9-c2f9e0ab10f7)

![image](https://github.com/user-attachments/assets/3e74ab7d-3780-4db6-b720-4056d129c375)
test.v

![image](https://github.com/user-attachments/assets/dd712bb4-2663-4202-8ff6-64a66b03455b)

test.tcl

From toy.lib
	C=0.1ff	C=100ff
Tr=0.1ps	1	80
Tr=100ps	4	200

Input delay=5ps, slew = 0.1ps, output delay=5ps and CL=100ff

![image](https://github.com/user-attachments/assets/25477cb0-8cf7-4bb3-b414-6e88afafc52a)

test.sdc

Expected Delay: 80 ps

Change to the respective directory where Open STA is installed.
Run OpenSTA by giving input files and study the impact of library, delay, and constraints on STA

![image](https://github.com/user-attachments/assets/8c77c65c-5ba1-4dbf-aee5-6f65e120ebbf)

![image](https://github.com/user-attachments/assets/c57a2059-f211-4567-a5f5-d9c88014a109)

Input delay – 5, inverter delay 80, data arrival time = 85 
Clock=1000,  output external delay = 5, data required time = 995
Slack = Data required time -Data arrival time = 995-85 =910
![image](https://github.com/user-attachments/assets/fa99bf74-c409-45a9-8ccb-85667826403e)

Input delay – 5, inverter delay 200, data arrival time = 205
Clock=1000,  output external delay = 5, data required time = 995
Slack = Data required time -Data arrival time = 995-205 =790

![image](https://github.com/user-attachments/assets/b59a417b-8727-4a8f-be99-2cc9384e2118)

Input delay – 5, inverter delay 4, data arrival time = 9
Clock=1000,  output external delay = 5, data required time = 995
Slack = Data required time -Data arrival time = 995-9 =986
![image](https://github.com/user-attachments/assets/9a5bc1b6-5c7c-472a-85c9-ed1eaf9b088e)
Input delay – 25, inverter delay 4, data arrival time = 29
Clock=1000,  output external delay = 5, data required time = 995
Slack = Data required time -Data arrival time = 995-29 =966
![image](https://github.com/user-attachments/assets/2a15e51f-8231-4fd3-8996-ead21e59ac24)
Input delay – 25, inverter delay 4, data arrival time = 29
Clock=1000,  output external delay = 35, data required time = 965
Slack = Data required time -Data arrival time = 965-29 =936

