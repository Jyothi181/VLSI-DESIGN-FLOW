# VLSI-DESIGN-FLOW
In this repository, all vlsi design steps are implemented using open source tools in linux environment and res

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
