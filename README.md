# Physical-Design-Using-OpenLane

This project is done in the course ["Advanced Physical Design using OpenLANE/Sky130"](https://www.vlsisystemdesign.com/advanced-physical-design-using-openlane-sky130/) by VLSI System Design Corporation. In this project a complete RTL to GDSII flow for PicoRV32a SoC is executed with Openlane using Skywater130nm PDK. Custom designed standard cells with Sky130 PDK are also used in the flow. Timing Optimisations are carried out. Slack violations are removed. DRC is verified.


## Table of Contents
- [Day - 1 Inception of Open-Source EDA, OpenLane and Sky130 PDK](#day---1-inception-of-open-source-eda-openlane-and-sky130-pdk)
  * [Overview](#overview)
  * [Inception of Opensource EDA](#inception-of-opensource-eda)
       + [SOC Design & OpenLANE](#soc-design--openlane)
          + [Components of opensource digital ASIC design](#components-of-opensource-digital-asic-design)
          + [Simplified RTL2GDS Flow](#simplified-rtl2gds-flow)
          + [OpenLANE ASIC Flow](#openlane-asic-flow)
       + [Opensource EDA tools](#opensource-eda-tools)
          + [OpenLANE design stages](#openlane-design-stages)
          +  [OpenLANE Files](#openlane-files)
          +  [Invoking OpenLANE](#invoking-openlane)
          +  [Design Preparation Step](#design-preparation-step)
          +  [Review of files & Synthesis step](#design-preparation-step#review-of-files-&-synthesis-step)
- [Day - 2 Floorplanning & Placement and library cells](#Day---2-Floorplanning-&-Placement-and-library-cells)
  * Utilization factor and Aspect ratio
     + [Floorplanning considerations](#floorplanning-considerations)
        +  [Utilization Factor & Aspect Ratio](#utilization-factor--aspect-ratio)
        +  [Pre-placed cells](#pre-placed-cells)
        +  [Decoupling capacitors](#decoupling-capacitors)
        +  [Power Planning](#power-planning)
        +  [Pin Placement](#pin-placement)
  * Floorplan run on OpenLANE & view in Magic](#floorplan-run-on-openlane--view-in-magic)
     + [Floorplanning](#Floorplanning)
     + [Floorplannong run on OpenLANE & view in Magic](#Floorplanning-run-on-openlane--view-in-magic)
     + [Placement](#Placement)
     + [Placement run on OpenLANE & view in Magic](#placement-run-on-openlane--view-in-magic)
  * [CELL DESIGN AND CHARACETRIZATION FLOWS](#CELL-DESIGN-AND-CHARACETRIZATION-FLOWS)
     + [Standard Cell Characterization Flow](#standard-cell-characterization-flow)
     + [Timing Characterization](#Timing-Characterizations)


## Day - 1 Inception of Open-Source EDA, OpenLane and Sky130 PDK
### Overview
OpenLANE is an opensource tool or flow used for opensource tape-outs. The OpenLANE flow comprises a variety of tools such as Yosys, ABC, OpenSTA, Fault, OpenROAD app, Netgen and Magic which are used to harden chips and macros, i.e. generate final GDSII from the design RTL. The primary goal of OpenLANE is to produce clean GDSII with no human intervention. OpenLANE has been tuned to function for the Google-Skywater130 Opensource Process Design Kit.


### SoC Design & OpenLANE

#### Components of opensource digital ASIC design
The design of digital Application Specific Integrated Circuit (ASIC) requires three enablers or elements - Resistor Transistor Logic Intellectual Property (RTL IPs), Electronic Design Automation (EDA) Tools and Process Design Kit (PDK) data.

![ASIC](https://user-images.githubusercontent.com/86701156/124005001-35a32a80-d9f6-11eb-8fcc-0917ad337699.PNG)

- Opensource RTL Designs: github, librecores, opencores
- Opensource EDA tools: QFlow, OpenROAD, OpenLANE
- Opensource PDK data: Google Skywater130 PDK

The ASIC flow objective is to convert RTL design to GDSII format used for final layout. The flow is essentially a software also known as automated PnR (Place & route).

#### Simplified RTL2GDS Flow

![RTL2GDS flow](https://user-images.githubusercontent.com/86701156/124006238-a139c780-d9f7-11eb-8da9-6069b055fbe0.PNG)

![RTLtoGDSIIflow](https://user-images.githubusercontent.com/83152452/131134578-5cd34ec9-a388-476b-aa4b-914c250d7ec9.png)

- Synthesis: RTL Converted to gate level netlist using standard cell libraries (SCL)
- Floor & Power Planning: Planning of silicon area to ensure robust power distribution
- Placement: Placing cells on floorplan rows aligned with sites
  - Global Placement: for optimal position of cells
  - Detailed Placement: for legal positions
- Routing: Valid patterns for wires
- Signoff: Physical (DRC, LVS) and Timing verifications (STA)

#### OpenLANE ASIC Flow



![OpenLaneflow](https://user-images.githubusercontent.com/83152452/131135115-46148ff1-9489-48f6-a334-6702c25def59.png)

From conception to product, the ASIC design flow is an iterative process that is not static for every design. The details of the flow may change depending on ECO’s, IP requirements, DFT insertion, and SDC constraints, however the base concepts still remain. The flow can be broken down into 11 steps:

1. Architectural Design – A system engineer will provide the VLSI engineer with specifications for the system that are determined through physical constraints. 
   The VLSI engineer will be required to design a circuit that meets these constraints at a microarchitecture modeling level.

2. RTL Design/Behavioral Modeling – RTL design and behavioral modeling are performed with a hardware description language (HDL). 
   EDA tools will use the HDL to perform mapping of higher-level components to the transistor level needed for physical implementation. 
   HDL modeling is normally performed using either Verilog or VHDL. One of two design methods may be employed while creating the HDL of a microarchitecture:
   
    a. RTL Design – Stands for Register Transfer Level. It provides an abstraction of the digital   circuit using:
   
   - i. 	 Combinational logic
   - ii. 	 Registers
   - iii.  Modules (IP’s or Soft Macros)
 
    b. 	Behavioral Modeling – Allows the microarchitecture modeling to be performed with behavior-based modeling in HDL. This method bridges the gap between C and HDL allowing HDL design to be performed

3. RTL Verification - Behavioral verification of design

4. DFT Insertion - Design-for-Test Circuit Insertion

5. Logic Synthesis – Logic synthesis uses the RTL netlist to perform HDL technology mapping. The synthesis process is normally performed in two major steps:

     - GTECH Mapping – Consists of mapping the HDL netlist to generic gates what are used to perform logical optimization based on AIGERs and other topologies created 
       from the generic mapped netlist.
       
     - Technology Mapping – Consists of mapping the post-optimized GTECH netlist to standard cells described in the PDK
  
6. Standard Cells – Standard cells are fixed height and a multiple of unit size width. This width is an integer multiple of the SITE size or the PR boundary. Each standard cell comes with SPICE, HDL, liberty, layout (detailed and abstract) files used by different tools at different stages in the RTL2GDS flow.

7. Post-Synthesis STA Analysis: Performs setup analysis on different path groups.

8. Floorplanning – Goal is to plan the silicon area and create a robust power distribution network (PDN) to power each of the individual components of the synthesized netlist. In addition, macro placement and blockages must be defined before placement occurs to ensure a legalized GDS file. In power planning we create the ring which is connected to the pads which brings power around the edges of the chip. We also include power straps to bring power to the middle of the chip using higher metal layers which reduces IR drop and electro-migration problem.

9. Placement – Place the standard cells on the floorplane rows, aligned with sites defined in the technology lef file. Placement is done in two steps: Global and Detailed. In Global placement tries to find optimal position for all cells but they may be overlapping and not aligned to rows, detailed placement takes the global placement and legalizes all of the placements trying to adhere to what the global placement wants.

10. CTS – Clock tree synteshsis is used to create the clock distribution network that is used to deliver the clock to all sequential elements. The main goal is to create a network with minimal skew across the chip. H-trees are a common network topology that is used to achieve this goal.

11. Routing – Implements the interconnect system between standard cells using the remaining available metal layers after CTS and PDN generation. The routing is performed on routing grids to ensure minimal DRC errors.

The Skywater 130nm PDK uses 6 metal layers to perform CTS, PDN generation, and interconnect routing.

### Opensource EDA tools

OpenLANE utilises a variety of opensource tools in the execution of the ASIC flow:
Task | Tool/s
------------ | -------------
RTL Synthesis & Technology Mapping | [yosys](https://github.com/YosysHQ/yosys), abc
Floorplan & PDN | init_fp, ioPlacer, pdn and tapcell
Placement | RePLace, Resizer, OpenPhySyn & OpenDP
Static Timing Analysis | [OpenSTA](https://github.com/The-OpenROAD-Project/OpenSTA)
Clock Tree Synthesis | [TritonCTS](https://github.com/The-OpenROAD-Project/OpenLane)
Routing | FastRoute and [TritonRoute](https://github.com/The-OpenROAD-Project/TritonRoute) 
SPEF Extraction | [SPEF-Extractor](https://github.com/HanyMoussa/SPEF_EXTRACTOR)
DRC Checks, GDSII Streaming out | [Magic](https://github.com/RTimothyEdwards/magic), [Klayout](https://github.com/KLayout/klayout)
LVS check | [Netgen](https://github.com/RTimothyEdwards/netgen)
Circuit validity checker | [CVC](https://github.com/d-m-bailey/cvc)

#### OpenLANE design stages

1. Synthesis
	- `yosys` - Performs RTL synthesis
	- `abc` - Performs technology mapping
	- `OpenSTA` - Performs static timing analysis on the resulting netlist to generate timing reports
2. Floorplan and PDN
	- `init_fp` - Defines the core area for the macro as well as the rows (used for placement) and the tracks (used for routing)
	- `ioplacer` - Places the macro input and output ports
	- `pdn` - Generates the power distribution network
	- `tapcell` - Inserts welltap and decap cells in the floorplan
3. Placement
	- `RePLace` - Performs global placement
	- `Resizer` - Performs optional optimizations on the design
	- `OpenDP` - Perfroms detailed placement to legalize the globally placed components
4. CTS
	- `TritonCTS` - Synthesizes the clock distribution network (the clock tree)
5. Routing
	- `FastRoute` - Performs global routing to generate a guide file for the detailed router
	- `CU-GR` - Another option for performing global routing.
	- `TritonRoute` - Performs detailed routing
	- `SPEF-Extractor` - Performs SPEF extraction
6. GDSII Generation
	- `Magic` - Streams out the final GDSII layout file from the routed def
	- `Klayout` - Streams out the final GDSII layout file from the routed def as a back-up
7. Checks
	- `Magic` - Performs DRC Checks & Antenna Checks
	- `Klayout` - Performs DRC Checks
	- `Netgen` - Performs LVS Checks
	- `CVC` - Performs Circuit Validity Checks

#### OpenLANE Files

The openLANE file structure looks something like this:

- skywater-pdk: contains PDK files provided by foundry
- open_pdks: contains scripts to setup pdks for opensource tools 
- sky130A: contains sky130 pdk files

#### Invoking OpenLANE and Design Preparation 

Openlane can be invoked using docker command followed by opening an interactive session.
flow.tcl is a script that specifies details for openLANE flow.

```
docker
./flow.tcl -interactive
package require openlane 0.9
```

```
prep -design picorv32a
```

![invoke_openlane](./Day_1/images/invoke_openlane.png)


#### Review of files & Synthesis step
* A "runs" folder is generated within the picorv32a folder.
* The merged file is created during the merging operation in the pircorv32a design preparation (it merges lef and techlef files)
* Next, we run the synthesis of picorv32a design in the openlane interactive terminal:

`run_synthesis`

![openlane - placement - run_synthesis](https://user-images.githubusercontent.com/83152452/185788656-fd086122-f98e-4fc0-bd4e-a711555c8435.png)


* The yosys and ABC tools are utilised to convert RTL to gate level netlist.
* Calcuation of Flop Ratio:
```
Flop ratio = Number of D Flip flops 
             ______________________
             Total Number of cells
```

```
dfxtp_4 = 1596,
Number of cells = 10104,
Flop ratio = 1596/10104 = 0.1579 = 15.79%
```
* We may check the success of the synthesis step by checking the synthesis folder for the synthesized netlist file (.v file)
* The synthesis statistics report can be accessed within the reports directory. It is usually the last yosys file since files are listed chronologically by date of modification.
* The synthesis area report are as follows:
* 
![synth_rep1](./Day_1/images/synth_rep1.png)

![synth_rep_2](./Day_1/images/synth_rep_2.png)



## Floorplanning & Placement and library cells

### Floorplanning considerations

#### Utilization Factor & Aspect Ratio  

Two parameters are of importance when it comes to floorplanning namely, Utilisation Factor and Aspect Ratio. They are defined as follows:

```
Utilisation Factor =  Area occupied by netlist
                     __________________________
                        Total area of core
```

```
Aspect Ratio =  Height
               ________
                Width
```
                                  
A Utilisation Factor of 1 signifies 100% utilisation leaving no space for extra cells such as buffer. However, practically, the Utilisation Factor is 0.5-0.6. Likewise, an Aspect ratio of 1 implies that the chip is square shaped. Any value other than 1 implies rectanglular chip.

#### Pre-placed cells

Once the Utilisation Factor and Aspect Ratio has been decided, the locations of pre-placed cells need to be defined. Pre-placed cells are IPs comprising large combinational logic which once placed maintain a fixed position. Since they are placed before placement and routing, the are known as pre-placed cells.

#### Decoupling capacitors

Pre-placed cells must then be surrounded with decoupling capacitors (decaps). The resistances and capacitances associated with long wire lengths can cause the power supply voltage to drop significantly before reaching the logic circuits. This can lead to the signal value entering into the undefined region, outside the noise margin range. Decaps are huge capacitors charged to power supply voltage and placed close the logic circuit. Their role is to decouple the circuit from power supply by supplying the necessary amount of current to the circuit. They pervent crosstalk and enable local communication.

#### Power Planning

Each block on the chip, however, cannot have its own decap unlike the pre-placed macros. Therefore, a good power planning ensures that each block has its own VDD and VSS pads connected to the horizontal and vertical power and GND lines which form a power mesh.

#### Pin Placement

The netlist defines connectivity between logic gates. The place between the core and die is utilised for placing pins. The connectivity information coded in either VHDL or Verilog is used to determine the position of I/O pads of various pins. Then, logical placement blocking of pre-placed macros is performed so as to differentiate that area from that of the pin area.

### Floorplan run on OpenLANE & view in Magic

#### Steps to perform Floorplanning and Placement

**Floorplanning**</br>
To perform floor planning
```
run_floorplanning
```

![floorplan_cmd](./Day_2/Images/floorplan_cmd.png)

#### Floorplannong run on OpenLANE & view in Magic

![magic_1](./Day_2/Images/magic_1.png)

To view the floor planning in magic :
```
cd /home/ammula-shiva-kumar/Physical-Design-Using-Openlane/OpenLane/designs/picorv32a/runs/RUN_2023.09.10_16.53.19/results/floorplanning
magic -T /home/ammula-shiva-kumar/.volare/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read picorv32.def &
```

![magic_2](./Day_2/Images/magic_2.png)


**Placement**</br>
To perform placemnet 
```
run_placement
```

![placement_cmd](./Day_2/Images/placement_cmd.png)

To view the placement in magic :
```
cd /home/ammula-shiva-kumar/Physical-Design-Using-Openlane/OpenLane/designs/picorv32a/runs/RUN_2023.09.10_16.53.19/results/placement
magic -T /home/ammula-shiva-kumar/.volare/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.nom.lef def read picorv32.def &
```
#### Placement run on OpenLANE & view in Magic

![floorplan_magic](./Day_2/Images/floorplan_magic.png)


### CELL DESIGN AND CHARACETRIZATION FLOWS

Library is a place where we get information about every cell. It has differents cells with different size, functionality,threshold voltages. There is a typical cell design flow steps.
1. Inputs : PDKS(process design kit) : DRC & LVS, SPICE Models, library & user-defined specs.
2. Design Steps :Circuit design, Layout design (Art of layout Euler's path and stick diagram), Extraction of parasitics, Characterization (timing, noise, power).
3. Outputs: CDL (circuit description language), LEF, GDSII, extracted SPICE netlist (.cir), timing, noise and power .lib files

### Standard Cell Characterization Flow

A typical standard cell characterization flow that is followed in the industry includes the following steps:

1. Read in the models and tech files
2. Read extracted spice Netlist
3. Recognise behavior of the cells
4. Read the subcircuits
5. Attach power sources
6. Apply stimulus to characterization setup
7. Provide neccesary output capacitance loads
8. Provide neccesary simulation commands

Now all these 8 steps are fed in together as a configuration file to a characterization software called GUNA. This software generates timing, noise, power models.
These .libs are classified as Timing characterization, power characterization and noise characterization.


### TIMING CHARACTERIZATION

In standard cell characterisation, One of the classification of libs is timing characterisation.

#### Timing threshold definitions 
Timing defintion |	Value
-------------- | --------------
slew_low_rise_thr	| 20% value
slew_high_rise_thr | 80% value
slew_low_fall_thr |	20% value
slew_high_fall_thr |	80% value
in_rise_thr	| 50% value
in_fall_thr |	50% value
out_rise_thr |	50% value
out_fall_thr | 50% value

#### Propagation Delay and Transition Time 

**Propagation Delay** 
The time difference between when the transitional input reaches 50% of its final value and when the output reaches 50% of its final value. Poor choice of threshold values lead to negative delay values. Even thought you have taken good threshold values, sometimes depending upon how good or bad the slew, the dealy might be still +ve or -ve.

```
Propagation delay = time(out_thr) - time(in_thr)
```
**Transition Time**

The time it takes the signal to move between states is the transition time , where the time is measured between 10% and 90% or 20% to 80% of the signal levels.

```
Rise transition time = time(slew_high_rise_thr) - time (slew_low_rise_thr)

Low transition time = time(slew_high_fall_thr) - time (slew_low_fall_thr)
```

# DAY3 Design Library Cell using ngspice simulations

<details>
  <summary>CMOS inverter ngspice simulations </summary>
  ``ngspice``  is opesoure engine where simulations are done.

  ### IO Placer revision

 - PnR is a iterative flow and hence, we can make changes to the environment variables in the fly to observe the changes in our design. 
 - Let us say If I want to change my pin configuration along the core from equvi distance randomly placed to someother placement, we just set that IO mode variable on command prompt as shown below
 ```
 set ::env(FP_IO_MODE) 2
```
## SPICE Deck Creation and Simulation for CMOS inverter

- Before performing a SPICE simulation we need to create SPICE Deck
SPICE Deck provides information about the following:
- Component connectivity - Connectivity of the Vdd, Vss,Vin, substrate. Substrate tunes the threshold voltage of the MOS.
- component values - values of PMOS and NMOS, Output load, Input Gate Voltage, supply voltage.
- Node Identification and naming - Nodes are required to define the SPICE Netlist
     For example ```M1 out in vdd vdd pmos w = 0.375u L = 0.25u``` , ```cload out 0 10f```
- Simulation commands
- Model file - information of parameters related to transistors
Simulation of CMOS using different width and lengths. From the waveform, irrespective of switching the shape of it are almost same.

![242781674-5b494ae5-341a-41ff-a2bb-1db066fa2b72](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/ee5317df-1ef5-4140-88cc-562fecc2001c)

From the waveform we can see the characteristics are maintained  across all sizes of CMOS. So CMOS as a circuit is a robust device hence use in designing of logic gates. Parameters that define the robustness of the CMOS are

## Switching Threshold Vm

- The Switching Threshold of a CMOS inverter is the point where the Vin = Vout on the DC Transfer characreristics. 
- At this point, both the transistors are in saturation region, means both are turned on and have high chances of current flowing driectly from VDD to Ground called Leakage current.
 
![243084644-3393442e-1b4b-434a-bc7e-6e2ed4fde218](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/1dbabda5-c5bd-4676-a306-c0de5519c659)


Through transient analysis, we calculate the rise and fall delays of the CMOS by SPICE Simulation. As we know delays are calculated at 50% of the final values.


## Lab steps to git clone vsdstdcelldesign

- First, clone the required mag files and spicemodels of inverter,pmos and nmos sky130. The command to clone files from github link is:
```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```
once I run this command, it will create ``vsdstdcelldesign`` folder in openlane directory.

Inorder to open the mag file and run magic go to the directory

For layout we run magic command

``` magic -T sky130A.tech sky130_inv.mag & ```

Ampersand at the end makes the next prompt line free, otherwise magic keeps the prompt line busy. Once we run the magic command we get the layout of the inverter in the magic window

![Screenshot from 2023-09-10 12-01-56](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/f5ebf20c-53c3-4c3c-b5a1-5b45e8bf685d)


</details>

<details>
  <summary>Inception of Layout and CMOS Fabrication Process
</summary>
  
## Mask CMOS Fabrication

The 16-mask CMOS (Complementary Metal-Oxide-Semiconductor) fabrication process involves several crucial steps for creating integrated circuits. Let's break it down with some jargon:

1. **Substrate Selection**:
   - In the initial phase, the appropriate semiconductor substrate is chosen.

2. **Active Region Creation**:
   - To isolate the active regions for transistors, the process begins with the deposition of SiO2 and Si3N4 layers, followed by photolithography and silicon nitride etching.
   - This is known as LOCOS (Local Oxidation of Silicon), where oxide is grown in certain regions.
   - Subsequently, Si3N4 is removed using hot phosphoric acid.

3. **N-Well and P-Well Formation**:
   - The N-well and P-well regions are created separately.
   - P-well formation involves photolithography and ion implantation of p-type Boron material into the p-substrate.
   - N-well is formed similarly with n-type Phosphorus material.
   - High-temperature furnace processes drive-in diffusion to establish well depths, known as the tub process.

4. **Gate Formation**:
   - The gate is a pivotal CMOS transistor terminal that controls threshold voltages for transistor switching.
   - A polysilicon layer is deposited and photolithography techniques are applied to create NMOS and PMOS gates.
   - Important parameters for gate formation include oxide capacitance and doping concentration.

5. **Lightly Doped Drain (LDD) Formation**:
   - LDD is created to mitigate hot electron and short channel effects.

6. **Source & Drain Formation**:
   - Thin oxide layers are added to avoid channel effects during ion implantation.
   - N+ and P+ implants are performed using Arsenic implantation and high-temperature annealing.

7. **Local Interconnect Formation**:
   - Thin screen oxide is removed through etching in HF solution.
   - Titanium deposition through sputtering is initiated.
   - Heat treatment results in chemical reactions, producing low-resistant titanium silicon dioxide for interconnect contacts and titanium nitride for top-level connections, enabling local communication.

8. **Higher Level Metal Formation**:
   - To achieve suitable metal interconnects, non-planar surface topography is addressed.
   - Chemical Mechanical Polishing (CMP) is utilized by doping silicon oxide with Boron or Phosphorus to achieve surface planarization.
   - TiN and blanket Tungsten layers are deposited and subjected to CMP.
   - An aluminum (Al) layer is added and subjected to photolithography and CMP.
   - This constitutes the first level of interconnects, and additional interconnect layers are added to reach higher-level metal layers.

9. **Dielectric Layer Addition**:
   - Finally, a dielectric layer, typically Si3N4, is applied to safeguard the chip.

This complex process results in the creation of advanced integrated circuits with multiple layers of interconnects, essential for modern electronic devices.

<img width="1175" alt="Screenshot 2023-09-10 at 2 13 35 PM" src="https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/d2359062-24fa-45d1-9cb3-de8fa4ec796f">

## SKY130 basic layer layout and LEF using inverter

- From Layout, we see the layers which are required for CMOS inverter. Inverter is, PMOS and NMOS connected together.
- Gates of both PMOS and NMOS are connected together and fed to input(here ,A), NMOS source connected to ground(here, VGND), PMOS source is connected to VDD(here, VPWR), Drains of PMOS and NMOS are connected together and fed to output(here, Y). 
The First layer in skywater130 is ``localinterconnect layer(locali)`` , above that metal 1 is purple color and metal 2 is pink color.
If you want to see connections between two different parts, place the cursor over that area and press S one times. The tkson window gives the component name.

![Screenshot from 2023-09-10 15-17-48](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/69b54c77-b195-4ab3-b3f4-b68a75b45a28)

### Library exchange format (.lef)

- The layout of a design is defined in a specific file called LEF.
-  It includes design rules (tech LEF) and abstract information about the cells. 
    -  ```Tech LEF``` -  Technology LEF file contains information about the Metal layer, Via Definition and DRCs.
    -  ```Macro LEF``` -  Contains physical information of the cell such as its Size, Pin, their direction.
 
## Designing standard cell and SPICE extraction in MAGIC 

-  First we need to provide bounding box width and height in tkson window. lets say that width of BBOX is 1.38u and height is 2.72u. The command to give these values to magic is
   ``` property Fixed BBOX (0 0 1.32 2.72)  ```
- After this, Vdd, GND segments which are in metal 1 layer, their respective contacts and atlast logic gates layout is defined
Inorder to know the logical functioning of the inverter, we extract the spice and then we do simulation on the spice. To extract it on spice we open TKCON window, the steps are
- Know the present directory - ``pwd ``
- create an extration file -  the command is  `` extract all `` and  ``sky130_inv.ext`` files has been created
          
- create spice file using .ext file to be used with our ngspice tool  - the commands are  
      ``` ext2spice cthresh 0 rthresh 0 ``` - extracts parasatic capcitances also since these are actual layers - nothing is created in the folder
      ``` ext2spice ``` - a file ```sky130_inv.spice``` has been created.
  
![Screenshot from 2023-09-10 15-40-43](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/4a7bf59a-fabe-47b2-9a7a-dd006bd9f1bf)

</details>

<details>
  <summary> SKY130 Tech File Labs </summary>
  
## Create Final SPICE Deck

let us see what is inside the spice Deck
In the spice file subcircuit(subckt), pmos and nmos node connections are defined
   
For NMOS  ``` XO Y A VGND VGND sky130_fd_pr_nfet_01v8 ``` . The order is  ``` Cell_name Drain Gate Source Substrate model_name ``` .
For PMOS  ``` X1 Y A VPWR VPWR sky130_fd_pr_pfet_01v8 ``` . The order is   ``` cell_name Drain Gate Source Substrate model_name ```.
   
For transient anaylsis, we would like to define these following connections and extra nodes for these in spice file
  - VGND to VSS
  - Supply voltage from VPWR to Ground - extra nodes here will be 0 and VDD with a value of 3.3v 
  - sweep in/pulse between A pin and VGND (0)
Before, editing the file, make sure scaling is proper, we measure the value of the gride size from the magic layout and define using `` .option scale=0.01u`` in the Deck file.
  
Now keeping the connection in mind, define the required commands in the file. Along with this we need to include libs for nmos ``nshort.lib`` and pmos ``pshort.lib`` and define transient analysis commands too. We comment the subckt since we are trying to input the controls and transient analysis also. Model names are changed to ``nshort_model.0`` and ``pshort_model.0`` according to the libs of nmos and pmos.
  
These voltage sources and simulation commands are defined in the Deck file.

   ``
.include ./libs/pshort.lib
.include ./libs/nshort.lib
   VDD VPWR 0 3.3V
   VSS VGND 0 0V
   Va A VGND PULSE(0V 3.3V 0 0.1ns 0.1ns 2ns 4ns)
   .tran 1n 20n
   .control
   run
   .endc
   .end
   ``
   
![Screenshot from 2023-09-10 22-07-35](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/5d7b3380-f67b-4a2a-b771-147e423fd0e0)

## Using ngspice for spice simulation
  
Spice Deck is done and now to run spice simulation invoke ngspice in the tool and pass the source file. 
 
  ``` ngspice sky130_inv.spice ```
  
On the prompt you can see the values the ngspice has taken. To see the plot, use
   
   ``` plot y vs time a ``` 
   
![Screenshot from 2023-09-10 22-18-54](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/55cbe4f1-0e53-4dc8-9ec4-c5cb6b168c45)


## Standard cell characterization of CMOS Iinverter 
 
characterization of the inverter standard cell depends on Four timing parameters
 
 **Rise Transition**: Time taken for the output to rise from 20% to 80% of max value
 **Fall Transition**: Time taken for the output to fall from 80% to 20% of max value
 **Cell Rise delay**: difference in time(50% output rise) to time(50% input fall)
 **Cell Fall delay**: difference in time(50% output fall) to time(50% input rise)
 
 The above timing parameters can be computed by noting down various values from the ngspice waveform.
 
 ``` Rise Transition : 2.25421 - 2.18636 = 0.006785 ns / 67.85ps ```
 ``` Fall Transitio : 4.09605 - 4.05554 = 0.04051ns/40.51ps ```
 ```Cell Rise Delay : 2.21701 - 2.14989 = 0.06689ns/66.89ps ```
 ```Cell Fall Delay : 4.07816 - 4.05011 = 0.02805ns/28.05ps ```

 ## LAB exercise and DRC Challenges

## Intrdocution of Magic and Skywater DRC's

  - In-depth overview of Magic's DRC engine
  - Introduction to Google/Skywater DRC rules
  - Lab : Warm-up exercise : Fixing a simple rule error
  - Lab : Main exercie : Fixing or create a complex error

 # Sky130s pdk intro and Steps to download labs
  
  - setup to view the layouts
  - For extracting and generating views, Google/skywater repo files were built with Magic
  - Technology file dependency is more for any layout. hence, this file is created first.
  - Since, Pdk is still under development, there are some unfinished tech files and these are packaged for magic along with lab exercise layout and bunch of stuff into the tar ball
 
We can download the packaged files from web using ``wget `` command. wget stands for web get, a non-interactive file downloader command.
  
  ``` wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz```
  
The archive file drc_tests.tgz is downloaded into our user directory 
  
![wget](https://github.com/sindhuk95/SKY130_PD_WS_DAY3/assets/135046169/3358159c-b338-43f4-b811-58a11284e75b)

once extraction is done, drc_tests file is created and you will have all the information about magic layout for this lab exercise

Now run MAGIC

For better graphics use command ``magic -d XR ``

Now, lets see an example of simple failing set of rules of metal 1 layer.  you can either run this by magic command line `` magic -d XR met1.mag `` or from the magic console window, `` menu - file - open -load file9here, met1.mag) ``

![Screenshot from 2023-09-10 22-52-50](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/d5ecd2e6-21be-4794-aea1-48df708bb1d6)

We use following commands to see metal cut as shown.
```
cif see VIA2

```
![Screenshot from 2023-09-10 23-11-08](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/748bb43a-88a8-42ef-861a-a52242efa105)

## Load Sky130 tech rules for drc challenges 

First load the poly file by ``load poly.mag`` on tkcon window.

Finding the error by mouse cursor and find the box area, Poly.9 is violated due to spacing between polyres and poly.

![Screenshot from 2023-09-10 23-15-04](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/6800d982-37a6-4bd7-9764-b5b80dba8d90)

We find that distance between regular polysilicon & poly resistor should be 22um but it is showing 17um and still no errors . We should go to sky130A.tech file and modify as follows to detect this error.

![Screenshot from 2023-09-10 23-24-02](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/0d199111-ded8-4193-a024-544227ab142c)


In line

```
spacing npres *nsd 480 touching_illegal \
	"poly.resistor spacing to N-tap < %d (poly.9)"
```
change to

```
spacing npres allpolynonres 480 touching_illegal \
	"poly.resistor spacing to N-tap < %d (poly.9)"
```
Also,
```
spacing xhrpoly,uhrpoly,xpc alldiff 480 touching_illegal \

	"xhrpoly/uhrpoly resistor spacing to diffusion < %d (poly.9)"
```

change to 

```
spacing xhrpoly,uhrpoly,xpc allpolynonres 480 touching_illegal \

	"xhrpoly/uhrpoly resistor spacing to diffusion < %d (poly.9)"

```
![Screenshot from 2023-09-10 23-15-04](https://github.com/alwinshaju08/Physicaldesign_openlane/assets/69166205/13aa309a-c2a7-427a-aae9-780a32229150)


</details>


