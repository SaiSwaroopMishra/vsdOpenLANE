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
    % echo $env(CLOCK_PERIOD) --will display 15.000

### Floorplanning @ OpenLANE flow - Hands-on Labs
First, you can explore configuration folder where are .tcl scripts to set variables to control the flow.

Pay attention on .tcl scripts priority. For example, _floorplaning.tcl_ has lower priority than _design.tcl_ that has lower priority than _sky130A...tcl_ script.

Next step on the flow is to perform the floorplaning with

        % run_floorplan
        
The next image shows the openlane terminal after floorplanning.

![s3](https://github.com/britovski/vsdOpenLANE/blob/main/images/s5.PNG)

Then, you can see the layout after the floorplanning using the MAGIC Layout tool.

The syntax to see all the layouts generated from openlane flow in each phase using MAGIC is:

        magic -T <.tech file> lef read <.lef file> def read <.def file>
        
You can use _sky130A.tech_ for Google-Skywater 130 _nm_, the _merged.lef_ file at _/tmp_ directory inside the design run directory and the .def file on results directory related to the design phase.

Each phase of the flow will provide a new .def file.

Below are the command used on the virtual machine lab.

![s4](https://github.com/britovski/vsdOpenLANE/blob/main/images/s6.PNG)

Note that was used _floorplan.def_ file for the layout in the next image.

![s5](https://github.com/britovski/vsdOpenLANE/blob/main/images/s7.PNG)

**You can press s to select top cell and then press v to center the layout.**

**You can explore the layout, selecting layers and cells and type _what_ in tkcon to know more about it.**

![s6](https://github.com/britovski/vsdOpenLANE/blob/main/images/s8.PNG)

**See the tap cells placed in the floorplanning, to avoid latch-up, connecting nwell to vdd and psub to gnd).**

Next design phase is the placement.

### Placement 
1) Before placement is required to bind netlist with physical cells. 

So, you need a **library**, that is a collection of blocks/cells with different flavors, sizes, *vt*, etc...).

2) Place the cells

3) Optimize the placement
 - check signal integrity;
 - add buffers for long paths;

4) After optimization, setup the timimg analysis with ideal clock. **we return to this on Day 4**

### Placement @ OpenLANE - Hands-on Labs
Considering global and detailed placement, command below from openlane performs the global placement:

        % run_placement
        
Image below shows the openlane console after that.

![s7](https://github.com/britovski/vsdOpenLANE/blob/main/images/s9.PNG)

The command to see the layout on MAGIC

![s8](https://github.com/britovski/vsdOpenLANE/blob/main/images/s10.PNG)

See the layout on MAGIC using the _placement.def_ file.

![s9](https://github.com/britovski/vsdOpenLANE/blob/main/images/s11.PNG)

**Note that openlane do power/ground distributio grid only after CTS, not at the floorplanning**

**You can change I0 placer modes on configuration file**

Before go ahead with timing analysis, first is needed to introduce the Cell Design and Characterization flow.

### Cell Design Flow (standard cells)

**Inputs:** PDKs, rules (DRC e LVS) and tech files, SPICE models, libraries and user defined specifications (supply voltages, layers, cell sizes, etc...).

**Design steps:** circuit design, layout design, characterization (timing, noise, power, .libs, functions, etc...).
- GUNA software for characterization
- timing characterization is based on threshold definitions, propagation delays and transition times.

**Outputs:** CDL, GDSII, LEF, extracted SPICE netlist (.cir)


## Day 3
Third day is focused on the design and characterization of one standard cell using MAGIC layout tool and characterization using ngspice with sky130 pdk. Also some Magic and DRC labs are presented.

### SPICE Simulations (pre-layout)
- SPICE deck / netlisting
- ngspice intro with simulation commands (source, run, setplot, display, plot...)
- static behaviour evaluation (example of CMOS INV robustness)
 1. switching thereshold (*Vm*)
- dynamic behaviour evaluation
 2. propagation delay (rise and fall)

### Art of Layout - Euler´s path and stick diagram
- pull-up + pull-down networks (concept introduced by building a complex logic circuit with 6 inputs and 1 output).
- pre-layout SPICE simulations (you can use virtual machine or cygdrive on windows for ngspice and MAGIC)
- discussion of stick diagram only vs using Euler´s path.
 * stick only needs lots of contacts/metal connections and diffusion breaks;
 * with Euler´s path is the best option.
- Euler´s path is the first step to input gate re-order (that is an optimization for stick diagram).
 * PMOS + NMOS network graphs (numbering nodes and edges as transistor labels).
- Do stick diagram with gates re-ordered.
- Abstract level layout can be done using optimized stick diagram.

### CMOS Fabrication Process

A 16-mask CMOS process is used as example to show how all layout regions are related to the fabrication process.

### Hands-on Labs

**First step is to clone vsdstdcelldesign repo**

    git clone https://github.com/nickson-jose/vsdstdcelldesign.git
 
After that, read the sky130_inv.mag layout using MAGIC.

![s10](https://github.com/britovski/vsdOpenLANE/blob/main/images/s12.PNG)

Image below show the layout.

![s11](https://github.com/britovski/vsdOpenLANE/blob/main/images/s13.PNG)

Explore the layout and layers. See the [repository](), especially if you want to build one.

In day 4, we will convert this layout cell to a LEF (you can use a .lef view to protect your IP), in order to use in openlane flow.

For now, is needed to perform parasitic extraction to characterize on ngspice.

In tkcon:

        extract all
        ext2spice cthresh 0 rthresh 0
        ext2spice
        
The extracted file can be seen in the image below

![s12](https://github.com/britovski/vsdOpenLANE/blob/main/images/s15.PNG)

 **Now setup the SPICE deck for sky130 technology with the following steps**
 
 - change scale to 0.01u
 - include pshort and nshort libs
 - put sources and simulation controls for ngspice
 
 The file with the needed modifications can be seen below.
 
 ![s13](https://github.com/britovski/vsdOpenLANE/blob/main/images/s16.PNG)
 
 Then, execute ngspice and do the plot for timing characterization:
 
        ngspice sky130_inv.spice
        > plot y vs time a

![s14](https://github.com/britovski/vsdOpenLANE/blob/main/images/s17.PNG)

Next step will create a .lef file to be used in openlane flow.

### Magic and DRC Labs @ sky130
Lab presented by Tim Edwards.
- Take tutorials 2 and 6 from [opencircuitdesign](http://opencircuitdesign.com/) Magic documentation.
- Take a look at sections Drc and cifoutput of the techfile.
- know the sky130 PDK (https://skywater-pdk.readthedocs.io/en/latest/ or https://github.com/google/skywater-pdk)
Download and install/setup labs from:

        http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz

and go through the exercises.

## Day 4

