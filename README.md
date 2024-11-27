# VLSI-DESIGN-FLOW
In this repository, all vlsi design steps are implemented using open source tools in linux environment and few experiments were carried out to get familiar with the design steps.

# Introduction

VLSI (Very Large-Scale Integration) Design refers to the process of creating integrated circuits (ICs) by combining thousands to millions of transistors on a single chip. 
VLSI involves the integration of a large number of electronic components, such as transistors, resistors, capacitors, and other functional blocks, onto a single semiconductor chip. 
This allows for the creation of complex circuits that are smaller, faster, and more power-efficient than earlier generations.


![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture1.png)


- **VLSI Design Flow :** Methodology to design an IC such that it delivers the required functionality or behaviour. This process follows the divide and conquer.
- **Idea to RTL Flow :** It takes a high-level idea or concept of a product and represents the hardware portion of the implementation in RTL. Pre-RTL flow is system level design
- **RTL to GDS Flow :** It takes an RTL through various stages of logical and physical design steps and finally represents the design as GDS file.
- **GDS to chip processes :** It takes a GDS file and prepares masks for a given GDS and fabricate/ tests / package chips.

### Terminologies

- **RTL :** Modelling of circuit as flow of data (signal) between registers

- **Library:**  Timing, area and power information of standard cells and macros 

- **Constraints:** Design goals, expected timing behaviour, enviroment (SDC) 

- **Design:** Top level entity that represents the circuit ex:MYDESIGN

- **Ports:** The interfaces of the design through which it communicates with the external world ex: In1,In2,CLK,out1, out 2 

- **Input ports:** Signals going inside the the design ex In1,In2,CLK 

- **Output ports:** Signals going outside from the design ex:out1,out2.

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

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture2.png?raw=true)
  
# RTL to GDS Flow
## Logic Synthesis 

It is the process by which RTL is converted to an equivalent circuit as inter connection of logic gates (netlist-output) represented using verilog constructs or schematic inputs to synthesis.

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture3.png?raw=true)

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

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture4.png)

### Inputs to PD

- Netlist input design (olp of logic synthesis)
- Library similar as in logic synthesis (Liberty).
- Abstract physical information of cells and technology specific information (LEF)
- Constraints similar to logic synthesis (SDC)

### Floorplan
It is the intial step of PD. Designer define the size and shape of die predefined placement information

**Chip Planning***

- Major decisions will be taken
- Partitioning into subsystems (or) blocks and arrange the blocks on the die.
- Allocation of area for the standard cells, macros and memory. Macro placement is also done.
- Includes IO cell planning and power planning
- We should ensure that voltage drop in power line should be within an acceptable limit

**Placement**
- Decides location of standard cells in the design 
- Total wire length minimization. Ensure timing is met and reduce the delay of critical path.
- Ensure there is no congestion. Placement of std cells is highly automative.

**CTS**
- Decides topology of clock network and how clock reaches each clocked element.
- CTS also performs wiring of clock network (avoid detour since majority of routing resources are still unused.)

**Routing**
- Creates wire layout for all the nets (other than clock and power supply) satisfying certain constraints
- The objective is use min wire-length, routing area, vias
- It is very complicated process (too many nets and routing constraints)

**Global Routing**
- Planning stage stage for routing. It is the first stage
- Actual layout of wires not created 
- A routing plan for a given net is created.
- Entire routing region is partitioned into rectangular tiles or global bins.
- Global routing assigns a set of global bins that will be used for making connections for a given net.
**Detailed Routing**
- Decides actual layout of each net in the pre-assigned global bins.
- The detailed router decides the actual physical inter-connections of nets by
        1.Allocating wires on each metal layer.
 	2.vias for switching between metal layers.

**ECO**
Engineering Change Order makes small final fixes in the design.

**Write GDS** 
Dump the Layouts of each layer in a GDS file.

**Optimization**
Between each PD task there are optimization steps. Small changes like buffer insertion on a given net, changing the size and placement of given cell and changing routing for a given net are done in the design to improve PPA. Small changes restricted to a small portions of a design so, that it does not create large disruptions in the design.

**Iterative flow** 
-    PD implementation tasks performed along with verification tasks (timing, power, signal integrity etc.,)
-    PD implementation should ensure design closure(including timing closure). Achieving design closure means that we have achieved state in which everything is fine in terms of properties.
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

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture5.png?raw=true)       ![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture6.png)

***Figure 1: Running Tool***

Verilog file after synthesis – func.v will be created in the directory after synthesis.
To open Verilog file – gedit func.v

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture7.png?raw=true)

***Figure : Verilog File after synthesis***

# Verification
**Objective:** To demonstrate the simulation and the code coverage tool. Also, the working of the tool is explained with an example Verilog code.

**Tools used:**
- ICARUS for Verilog Simulation
- COVERED for computing code coverage
- GTKWave is an analysis tool used to perform debugging on Verilog or VHDL simulation models. Here, we will be using one of the dump file formats, Value Change Dump (VCD)

**Input Files:**
- Mycounter.v – Verilog code
- Testbench.v – Testbench

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture8.png)

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture9.png)

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

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture10.png)

***Figure: Verificaiton - GTK wave***

Select the Tesbench-I1 and then select each signal and insert to view the waveforms.In the left panel, expand the Testbench and click on the subfolder. It will expand to show input Clock, Reset and output waveform OUT[3:0]. Drag those to the signals panel and analyze the results.

## Generating the code coverage report: 
To estimate the percentage of RTL design tested by the test bench, COVERED Verilog Code Coverage Analyzer tool is used. Generate the code coverage report in the same directory i.e., icarus_codes by executing the following command

`$ covered score -t Testbench -v Testbench.v -v Mycounter.v -vcd count.vcd -o Mycounter.cdd`

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture11.png)

To view the coverage report, execute `$ covered report -d v Mycounter.cdd` The coverage report is displayed in the terminal

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture12.png)  ![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture13.png)

***Figure : Code coverage report***

It is obtained that Code coverage is 100%

# Logical Synthesis

**Objective:** To demonstrate converting of RTL netlist to gate level netlist by using synthesis tool with an example Verilog code.

**Tools used:** Yosys (Open SYnthesis Suite)

**Input files :** 
- verilog file: top.v
- script file: yosys_commands.tcl
- Technology library: NangateOpenCellLibrary_typical.lib, toy.lib

The Synopsys Design Constraint (SDC) file is not provided, so the synthesized netlist will be mapped for minimum area constraints.

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture14.png)

## TCLscript
- Read modules from Verilog file -> `read_verilog top.v`
- Elaborate design hierarchy -> `hierarchy −check −top top`
- Translate processes to netlists -> `proc`
- Mapping to the internal cell library -> `techmap`
- Mapping flip−flops to toy Library -> `dfflibmap −liberty toy.lib`
- Mapping logic to toy Library -> `abc −liberty toy.lib`
- Remove unused cells and wires -> `clean`
- Write the current design to a Verilog file -> `write_verilog −noattr synth_example.v`

## Steps to perform the logic synthesis

Launch the Linux distribution and cd to the directory and load the input files in yosys_codes directory.
![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture15.png)

Launch the yosys tool -> `yosys`

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture16.png)

You can individually run the commands listed or source tcl file.  yosys> `script yosys_commands.tcl` 
 To view the generated netlist, close the yosys tool with ctrl+Z. To view the netlist: `gedit synth_example .v`

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture17.png)


# Logic Optimization

**Objective:** To perform the resource sharing and to observe how it leads to decrease in area.

**Resource Sharing:**

if (sel == 1’b0) z = a*b;
else z=x*y;

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture18.png)

**Tools used:** Yosys (Open SYnthesis Suite)  
**Input Files:**  
- Design file: top.v 
- Yosys script files: opt.tcl, not_opt.tcl 
- Technology library: toy.lib 

## Unoptimized Synthesis

**Steps to perform Logical Optimization:**

Run the Unoptimized code.

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture19.png)

Mapping the flipflops and combinational circuits to the cells in our library

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture20.png)

`stat -liberty toy.lib`

Design was synthesized It reported the chip area in the library units for unoptimized cells is 13698

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture21.png)

Now get the Verilog netlist by writing into a Verilog file.

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture22.png)

Netlist was created

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture23.png)

Can view the logic diagram using command show

## Optimized Synthesis
Now perform the optimized synthesis by using following commands

    read_verilog top.v
    proc
    clean
    opt
    share -aggressive
    techmap
    difflibmap -liberty toy.lib
    abc -liberty toy.lib
    clean
    stat -liberty toy.lib



Design was synthesized and it reported the chip area in the library units for optimized cells is 7451

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture24.png)

Get the Verilog netlist by writing into a Verilog file.

`write_verilog -noattr -noexpr -nohex -nodec netlist_final_opt.v`

The chip area and no. of gates of the optimized logic is less than the unoptimized logic. The tool has done resource sharing.


# Static Timing Analysis

**Objective:** To analyze and verify the timing performance of digital circuits at the gate level.


**Tools used:** OpenSTA. 

**Input Files:**
- Verilog file (top.v)
- OpenSTA script file (test.tcl)
- constraint file (top.sdc)
- technology library (toy.lib). 

**test.tcl:** 

The script file "test.tcl" contains a series of commands that will be executed by OpenSTA to perform various tasks related to static timing analysis.

Here's a breakdown of the commands commonly found in the "test.tcl" script: 

- read_liberty toy.lib  -> Instructs OpenSTA to read and load the Liberty file "toy.lib".  
- read_verilog top.v -> Intructs OpenSTA to read and load the Verilog file (gate level verilog netlist) "top.v 
- link_design top  -> Using "top," which stands for the main module, links the Verilog code with the Liberty timing cells. 
- read_sdc top.sdc  -> Reads and loads the Synopsys Design Constraints (SDC) file "top.sdc".  
- report_checks -path_delay max -format full -> Report of the timing checks for the design (setup) 
- report_checks -path_delay max > reports.txt -> Store the report of the timing checks for the design (setup) in the reports.txt file. 
- report_checks -path_delay min -format full -> Report of the timing checks for the design (hold)
- report_checks -path_delay min > reports.txt -> Store the report of the timing checks for the design (hold) in the reports.txt file.

Move the input files top.v, top.sdc, toy.lib, test.tcl into the STA directory

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture25.png)

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture26.png)

Top.v

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture27.png)

Top.sdc

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture28.png)

Test.tcl

Source the tcl script file which has commands to read the input files and provide the timing report.

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture29.png)

Individual timing reports can be obtained by using the following commands.

Setup slack -> report_checks -path_delay max -format full

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture30.png)

Hold Slack -> `report_checks -path_delay min -format full`

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture31.png)


# Technology Library
**Objective:** To understand how delay calculation and Static Timing Analysis (STA) are impacted by Technology Library and Constraints. Impact of Technology library and constraints on delay and also on STA

**Concepts:**

**Non-linear Delay Model (NLDM):** Non-Linear Delay Models are used to estimate the delay of signal propagation through various circuit elements, such as interconnects, gates, and wires in a more accurate and complex way compared to linear models.
In STA, non-linear delay models are used to estimate the timing of a signal’s path through a circuit, considering the actual behaviour of the gate and interconnect delays.
These models offer more accurate delay estimations and are key for optimizing performance, meeting timing constraints, and ensuring the robustness of the final chip design.

**Constraints:** Constraints refer to the requirements, limitations, or conditions that a design must adhere to during the design and optimization process. These constraints guide the layout, timing, power, and functionality of a chip, and they are essential for ensuring that the final product meets all necessary performance, reliability, and manufacturability goals. Ex: Timing, power, area, manufacturing, clock, routing and functional constraints.
Some of the constraints are listed below
- create_clock: to create a clock reference for timing analysis 
- set_input_delay: to specify the delay in input before reaching the given circuit 
- set_output_delay: to model change in required time due to external circuit elements 
- set_input_transition: to specify transition/slew at the input port 
- set_load: to specify load capacitance at the output port 
- set_clock_uncertainty: to add pessimism to timing analysis

## Requirements:  
**Tool used:**  Open STA
**Input files :** 
- Design file: test.v 
- OpenSTA script file: test.tcl 
- Technology library: toy.lib
- SDC file: test.sdc 
All input files are loaded into the directory.

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture32.png)

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture33.png?raw=true)

Picture : test.v

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture34.png?raw=true)

Picture : test.tcl


|  | C=0.1ff | C=100ff|
| ------------------ | ---- | --- |
| Tr=0.1ps | 1 | 80 |  
| Tr=100ps | 4 | 200 |

Table : toy.lib

Input delay=5ps, slew = 0.1ps, output delay=5ps and CL=100ff

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture35.png?raw=true)

Picture: test.sdc

Expected Delay: 80 ps

Change to the respective directory where Open STA is installed.
Run OpenSTA by giving input files and study the impact of library, delay, and constraints on STA

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture36.png?raw=true)

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture37.png?raw=true)

Input delay – 5, inverter delay 80, data arrival time = 85 
Clock=1000,  output external delay = 5, data required time = 995
Slack = Data required time -Data arrival time = 995-85 =910

Similarly the STA analysis is carried out for different values for different parameters and slack increases or decreases accordingly. Here the slack is positive. so, the slack is met.

| input delay | inverter delay | data arrival time | output external delay | slack |
| ------------------ | ---- | --- | ------ | --------- |
| 5 | 80 | 85 | 5 | 910 |
| 5 | 200 | 205 | 5 | 790 |
| 5 | 4 | 9 | 5 | 986 |
| 25 | 4 | 29 | 5 | 966 |
| 25 | 4 | 29 | 35 | 936 |


# Power Analysis using Open STA

**Objective:** To examine how Power Analysis is done by the tool.

**Concepts:** 

**Non-linear Power Model (NLPM)**

Average area consumed per transition=(1+2)/2 = 1.5fJ = 1.5 x 10-15𝐽

Clock period = 1000ps

No. of clock cycles per second= 1/1000×10−12=109

Activity=No. of transitions per clock cycles=0.1

No. of transitions per second=0.1 × 109 

Internal power = Energy per transition x number of transition per second=1.5×10−15×0.1×109 = 1.5×10−7 𝑊  

**Switching Power Computation:** 
Load = 𝐶 = 0.1𝑓𝑓 

Voltage = 1 V 

Energy dissipated in one transition = ½  x 𝐶𝑉2 =0.5×0.1×10−15 ×12 = 5×10−17 J 

No. of transitions per second =0.1 × 109 

Switching power= 𝐸𝑛𝑒𝑟𝑔𝑦 𝑝𝑒𝑟 𝑡𝑟𝑎𝑛𝑠𝑖𝑡𝑖𝑜𝑛 × 𝑛𝑢𝑚𝑏𝑒𝑟 𝑜𝑓 𝑡𝑟𝑎𝑛𝑠𝑖𝑡𝑖𝑜𝑛 𝑝𝑒𝑟 𝑠𝑒𝑐𝑜𝑛𝑑 =5×10−17×0.1×109 = 5×10−9 𝑊 

Leakage Power: From toy.lib 150 × 10−12 = 1.5 × 10−10 𝑊

Input delay = 5ps, slew=0.1ps, output delay=5ps, CL=0.1ff

**Requirements:**  

**Tool used:** OpenSTA

**Input Files:**  

- Design file: test.v 
- OpenSTA script file: test.tcl 
- Technology library: toy.lib
- SDC file: test.sdc 

All input files are loaded into the directory.

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture38.png?raw=true)

Picture : test.v


![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture39.png?raw=true)

Picture : test.tcl


Fall Transition: 
|  | C=0.1ff | C=100ff|
| ------------------ | ---- | --- |
| Tr=0.1ps | 1 | 2 |  
| Tr=100ps | 3 | 4 |

Rise Transition:

|  | C=0.1ff | C=100ff|
| ------------------ | ---- | --- |
| Tr=0.1ps | 2 | 4 |  
| Tr=100ps | 6 | 10 |

Table: toy.lib
 
![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture40.png?raw=true)

Picture: test.sdc

We set the Power activity. The dynamic power dissipation depends on how frequently the switching is occurring in the circuit. So, to compute the dynamic power dissipation tool will need to know the activity of the signal. So, if it is not specified tool will assume the default activity in this case, the activity will be 0.1 and tool will report the power based on this.

![image](https://github.com/Jyothi181/VLSI-DESIGN-FLOW/blob/main/Design_Figures/Picture41.png?raw=true)

Picture : Power Analysis

