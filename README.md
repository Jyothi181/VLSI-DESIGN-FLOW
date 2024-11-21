# VLSI-DESIGN-FLOW

# Introduction

VLSI (Very Large-Scale Integration) Design refers to the process of creating integrated circuits (ICs) by combining thousands to millions of transistors on a single chip. 
VLSI involves the integration of a large number of electronic components, such as transistors, resistors, capacitors, and other functional blocks, onto a single semiconductor chip. 
This allows for the creation of complex circuits that are smaller, faster, and more power-efficient than earlier generations.

![image](https://github.com/user-attachments/assets/1d09475a-bc9d-46ab-8077-bec55cdcd649)

- **VLSI Design Flow :** Methodology to design an IC such that it delivers the required functionality or behaviour. This process follows the divide and conquer.
- **Idea to RTL Flow :** It takes a high-level idea or concept of a product and represents the hardware portion of the implementation in RTL. Pre-RTL flow is system level design
- **RTL to GDS Flow :** It takes an RTL through various stages of logical and physical design steps and finally represents the design as GDS
- **GDS to chip processes :** It takes a GDS prepares masks for a given GDS and fabricate/ tests / package chips

### Terminologies

- RTL : Modelling of circuit as flow of data (signal) between registers

- Library:  Timing, area and power information of standard cells and macros 

- Constraints: Design goals, expected timing behaviour, enviroment (SDC) 

- Design: Top level entity that represents the circuit ex:MYDESIGN

- Ports: The interfaces of the design through which it communicates with the external world ex: In1,In2,CLK,out1, out 2 

- Input ports: Signals going inside the the design ex In1,In2,CLK 

- Output ports: Signals going outside from the design ex:out1,out2.

- Cells: Basic entity delivering combinational or sequential contained in librariesex ANZ, NOT, BUF, DFF design is composed of multiple cells connected together

- Instances: cells when used inside a design are called instances
Ex:I1, I2, I3, out-reg, out2-reg
The same cell can be instantiated multiple times
Ex:out1-reg and out2-reg are instances of the same cell Dff

- Pin: An Interface of a library cell (or) instance through which it communicates with the other components
ex: A, B, Y are the pins of cell AN2 and the instance I1 Library pin and Instance pin (if we want to be explicit)
I/p pin or o/p pin based on direction of flow of signal to cell/instance
Instance pin name typically specified as combination of instance name and pin name seperated by /
Ex: I1/A,I2/B, anti-reg/Q

- Nets The wire that connects diff instances and ports is called Nets.
Ex: N1,N2,N3,.....N8

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
## Logic Synthesis: 

It is the process by which RTL is converted to an equivalent circuit as inter connection of logic gates (netlist-output) represented using verilog constructs or schematic inputs to synthesis.
RTL given design (Verilog, VHDL)

![image](https://github.com/user-attachments/assets/0293331c-f072-4806-ac10-c911eb2a66ed)


### RTL Synthesis: 
Initial part of logic synthesis consisting of translating an RTL to a netlist of generic logic gates.
•	A generic logic gate has a well defined Boolean function.
•	ex:AND,NAND,XOR,MUX,latches
•	It does not have a fixed transistor-level implementation and also not have a well-defined area, delay, power attributes.

### Logic Optimization:
•	Optimizations on a generic gate netlist 
•	It is typically area -driven

### Technology Mapping:
•	Map a netlist consisting of generic logic gates to the std cells in the given technology Library and obtain a netlist & stdcells.
Technology dependent Optimization:
•	PPA can be estimated more accurately after technology mapping
•	Perform timing, areas Power optimization over netlist of std cells


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

-	first stepin PD major decisions will be taken
-	Partitioning into subsystems (or) blocks
-	Arrange the blocks on the die
-	Allocation of area for the stod cells, macros, memory 
-	Includes IO cell planning and power planning
-	We should ensure that voltage drop in power line should be within an acceptable limit

### Placement
-	Decides location of stdcells in the design 
-	Total wire length minimization
-	Ensure timing is met. Reduce the delay of critical path.
-	Ensure no congestion. Placement of std cells is highly automative.

### CTS
-	Decides topology of clock n/w and how clock reaches each clocked element.
-	CTS also performs wiring of clock n/w (avoid detour since majority of rooting resources are still unused.)
Objectives ensure minimum skew symmetric structure
Minimize Pd: clock n/w consumes large fraction of total power CTS consumes 40% of total PD
Consideration during chip planning.one  voltage drop and another is Congestion.lots of nets are running close together it leads to timing problems routability problems.

### Routing
•	Creates wire layout for all the nets (other than clock and power supply) satisfying certain constraints
•	The objective is use min wire-length, routing area, vias
•	very Complicated process (too many nets and rating constraint )

#### Global Routing
•	Planning stage stage for routing. It is the first stage
•	Actual layout of wires not created 
•	A routing plan for a given net is created.
•	Entire routing region is partitioned into rectangular tiles or global bins.
•	Global routing assigns a set of global bins that will be used for making connections for a given net.
#### Detailed Routing
•	Decides actual layat of each net in the pre-assigned global bins.
•	The detailed router decides the actual physical inter-Connections of nets by: 1.Allocating wires on lach metal layer. 2.vias for Switching between metal layers.

### ECO 
Engineering Change Order makes small final fixes in the design.

### Write GDS 
Dump the Layouts of each layer in a GDS file.

### Optimization
Between each PD task there are optimization steps.
1. Small changes in the design to improve PPA 
    -	Buffer insertion on a given net
    -	changes in the size of a given cell.
	  - changes in the placement of a given cell.
    -	change in routing for a given net.

2. Changes are kept small and restricted to a small portions of a design
    -	Do not Create Large disruption in the design
    - Design flow should converge

### Iterative flow 
- PD implementation tasks performed along with verification tasks (timing, power, signal integrity etc.,)
-	PD implementation should ensure design closure(including timing closure)
-	Achieving design closure means that we have achieved state in which everything is fine  in terms of properties that design should satisfy). This is a challenging task
-	Design tasks do not have the full information and works with estimates that can be wrong.
-	PD is iterative One task may require that previous tasks retract same design decisions.This creates loops.
-	Achieving design closure with min no. of iterations is the goal of a PD.

