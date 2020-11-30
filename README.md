# vsdOpenLANE
Workshop on Advanced Physical Design using OpenLANE flow and Skywater 130 nm open source PDK repo

## Description
This repository is a summary of the 5-day workshop of Advanced Physical Design using OpenLANE flow and Google-Skywater 130 nm open source Process Design Kit. It is organized day-by-day as follows:

## Day 1

First day is used to introduce the cloud based structure of the workshop. The learning platform is divided in assessments (MCQs) and learning skills (DXSKXs). Also, a web based linux virtual machine is provided to labs hands-on execution.

Cloud based learning is guided through learning skils as follows:

### Fundamentals
Some concepts introduced:
- Board design x chip design
- packages x chip/die (composed by PADs + core) x IC
- Foundry IP's x IP's x Macros

### RISC-V ISA
An introduction to RISC-V architeture is done.
- Translation from high-level language to machine code is presented: C --> ASM --> binary code (compilator + assembler)
- Implementation of the RISC-V ISA can be performed using HDL in the front-end side and layout in the back-end side. For exemple: picorv32 CPU core.
- (Apps. --> System Softwares --> Hardware) real execution flow.
SoC is introduced by Raven and picoSoC examples (RISC-V based SoCs).
- SoC is related to the synthesizable logic in a chip. Ex: Raven Chip = Raven SoC + RAM blocks + analog IP's.

### SoC Design and OpenLANE
The goal is to present the three pillars of open source ASIC Design: EDA tools, RTL cores and PDK; all open source! 

The ASIC flow is also known as:
- RTL to GDSII;
- Physical Design; or
- Automated Place and Route (PnR).

ASIC flow has some steps:
- Synthesis (RTL/verilog synthesis and cell mapping);
- Floorplanning and power planning;
- Placement;
- Clock Tree Synthesis (CTS);
- Routing;
- Sign-off (STA + LVS + DRC Clean).

An example of ASIC Design using _open everything_ (RTL+EDA+PDK open sources) are the _striVe_ SoC.

The input of the ASIC flow is the RTL and the output is GDSII. All the steps need to be assisted using the Process Design Kit (PDK). Below image shows the ASIC flows implemented by OpenLANE and the open source EDA tools used in each design step.

![openlane_flow](https://github.com/efabless/openlane/blob/master/doc/openlane.flow.1.png)

The main goal of the OpenLANE is to produce clean GDSII with no human intervention (fully automated).
- two modes: autonomous or interactive;

More about OpenLANE can be found on [github](https://www.udemy.com/course/vsd-a-complete-guide-to-install-openlane-and-sky130nm-pdk/).

<!--An introduction to IC Design flow and tools are performed. The goal is to show the RTL2GDS flow using opensource tools.
- Beginning with logic synthesis (using Yosys);
- Floorplanning;
- Placement (graywolf);
- Clock Tree Synthesis - CTS;
- Routing (Qrouter);
- Static Timing Analysis - STA (Opentimer);
* MAGIC layout viewer and editor is presented. Also used for equivalent circuit extraction and DRC;
* ngspice is presented for pré and post-layout simulations;
* eSIM is also presented for SPICE simulations with schematic capture functionality and also with PCB design capabilities;
Last but not least, Qflow is presented as a complete tool chain (using tools as Yosys and graywolf) for complete RTL2GDS flow.
-->

After an OpenLANE presentation, some hands-on activities are performed to show directory structure and simple commands to begin in the flow.

### Hands-on Labs
Some labs are performed using web based linux virtual machine with pre-configured open source tools based on OpenLANE flow.

More about the tools installation can be found on [this udemy course](https://www.udemy.com/course/vsd-a-complete-guide-to-install-openlane-and-sky130nm-pdk/) by Kunal Ghosh and Nickson Jose.

**First step is to get familiar with basis linux commands**

    cd
    ls
    ...

**Then openlane directory and skywater pdk directory are presented as the main lab structure**

    openlane_working_dir
    openlane_working_dir\openlane_FLOW
    openlane_working_dir\pdks
    openlane_working_dir\pdks\skywater-pdk
    openlane_working_dir\pdks\open_pdks
    openlane_working_dir\pdks\sky130A
    openlane_working_dir\pdks\sky130A\libs.ref
    openlane_working_dir\pdks\sky130A\libs.tech
    
**Then openlane flow has began** (virtual machine starts in the docker, so is not needed to initialize it).

    @ openlane_working_dir\openlane_FLOW directory
    ./flow.tcl -interactive
    % package require openlane 0.9
    % prep -design picorv32a
 
The above commands begins the OpenLANE flow in interactive mode for picorv32a. Below image shows this start.

![s1](https://github.com/britovski/vsdOpenLANE/blob/main/images/s3.PNG)

We can also explore the openlane runs directory to know the structure (\reports, \results, \tmp, ...)

First step of the flow is the _synthesis_. You can run at openlane % as

       % run_synthesis

and get results about synthesis, mapping and post-synthesis timing analysis. Below is possible to see openlane terminal after the command.


![s2](https://github.com/britovski/vsdOpenLANE/blob/main/images/s4.PNG)


## Day 2

Second day presents the Chip floorplanning as the preparation before automatic placement and routing. Also presents the requirement of library cells and it design flow.

The fundamentals of this phase is the same as the previous beginPD workshop, as follows:
### Chip Floorplaning
Second workshop day begins with **Chip Floorplanning concept**. Some steps are introduced as part of Chip Floorplanning phase:
1) Define width and height of core and die based on standard cell dimensions
- Place all standard cells inside the core
- notions of utilization factor and aspect ratio.
2) Define location of pre-placed cells
- separate circuits in to blocks or modules
- select available IP's
-- The arrangement of the cells (IP's, blocks, modules) is referred as floorplanning.
-- pre-placed cells are placed in user-defined locations before automated place and route.
3) Surround pre-placed cells with decoupling capacitors.
Noise margin concept is presented (NMh for '1' and NMl for '0')
- Vdd or Vss could drop without decoupling caps between Vdd and Vss;
-- Decoupling caps. are huge caps between Vdd and Vss and need to be placed near the circuit/block.
4) Power planning
- If many blocks discharges from '1' to '0' at same time in a single ground cause a bump (Gnd bounce). If from '0' to '1' a voltage droop (for single Vdd).
- Istead of single supply lines, use multiple arrays of power supply (Vdd and Vss points). 
Observation --> As we can see in the Day 5, OpenLANE flow do the power planning after CTS phase.
5) Pin placement
- Connectivity is described using VHDL or verilog;
- try to put pins near blocks;
- bigger PADs for clock pins.
6) Logical Cell placement blockage --> add PAD ring blocks.

**After floorplanning. We are ready for placement and routing steps**


### Some tips on OpenLANE flow
Some tips are provided to use 'OpenLANE' regarding 'data preparation'.

**Use labels or tags with 'prep -design' command to reuse in a easy way in other design flow steps**

    % prep -design picorv32a -tag trial_run1
    % prep -design picorv32a -tag trial_run1 -overwrite

The last command has the option to overwrite and make changes in previous design files.

    % set env(VARIABLE) new_value --to change script/design VARIABLE on the fly
    % echo $env(VARIABLE) --to check VARIABLE values

Example:

    % set env(CLOCK_PERIOD) 15.000
    % echo $env(VARIABLE) --will display 15.000

### Floorplanning @ OpenLANE flow
