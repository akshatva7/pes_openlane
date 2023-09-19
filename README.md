# pes_openlane

# Table of Contents
<details>
<summary>Day 1</summary>
<br>

- [How to Talk to Computers](#how-to-talk-to-computers)
- [SoC Design and OpenLANE](#soc-design-and-openlane)
- [Getting Familiar with the Open Source EDA Tools](#getting-familiar-with-the-open-source-eda-tools)
</details>

<details>
<summary>Day 2</summary>
<br>
  
- [Chip Floor Planning Considerations](#chip-floor-planning-considerations)
- [Library Binding and Placement](#library-binding-and-placement)
- [Cell Design and Characterization Flow](#cell-design-and-characterization-flow)
</details>

<details>
<summary>Day 3</summary>
<br>

- [Labs for CMOS inverter ngspice simulations](#labs-for-cmos-inverter-ngspice-simulations)
- [Inception of Layout and CMOS Fabrication Process](#inception-of-layout-and-cmos-fabrication-process)
- [Sky130 Tech File Labs](#sky130-tech-file-labs)
</details>

<details>
<summary>Day 4</summary>
<br>
  
- [Timing Modelling using Delay Tables](#timing-modelling-using-delay-tables)
</details>

# Day 1

## How to Talk to Computers
- First we look at the introduction to the RISC-V ISA(Instructiion Set Architecture). Supposing we need to execute a C program on a particular hardware. First the C-program is converted into Assembly Code( here for RISC-V processor). Then the assembly code is converted into binary. An RTL implements this code for the particular layout of the RISC-V processor and the output is visible.
- An application running on a system is usually written with the help of a high level language such as C,C++,Python etc. The code of these applications are compiled with the help of compilers running on a system software(OS). The compiler converts the high level code into assembly intructions for the particular processor. The assembler then converts the instructions into binary which is fed into the layout of the chip that processes every pattern of bits and the program is hence run.

## SoC Design and OpenLANE

**What is a PDK?**
- PDK stands for Process Design Kit.
- It is a collection of files used to model a fabrication process for the EDA tools used to design an IC
  - Process Design Rules.
  - Device Models
  - Digital Standard Cell Libraries
  - I/O Libraries
 
A simplified RTL to GDSII Flow is :
- Synthesis -> Floor/Power Planning -> Placement -> Clock Tree Synthesis -> Routing -> Signoff

- Synthesis - Converts RTL to a ciruit, out of compomments from the standard cell library.
- Floor and Power Planning - Obejctive here is to plan the silicon area and create robust power distribution network to power the chip.
  - Chip Floor Planning - Partition the chip die between different system building blocks and place the I/O pads.
  - Macro Floor Planning - We define the macro dimensions, pin locations and rows are defined.
  - Power Planning - The power distribution network is contructed.
- Placement - Placing the cells on the floorplan rows, aligned with the sites. There are 2 steps: Global and Detailed.
- Clock Tree Synthesis - To deliver the clock to all sequential elements.
- Routing - Implement the interconnect using the available metal layers.
- Sign Off - Perform physical verification such as DRC(Design Rule Check) and LVS(Layout vs Synthesis). Also perform STA(Static Timiing Analysis).

**OpenLANE ASIC Flow**

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/0ca2d9d4-3422-4714-8947-d6a0426575f3)

## Getting Familiar with the Open Source EDA Tools

### Design Preparation Step

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/4362b597-7df4-4999-b930-6097e3ca82b9)

- Let us first go the the working directory using the following commands
```
cd Desktop/work/tools/
```

```
cd openlane_working_dir/openlane/
```
- We now type the command ```docker```.
- This will open the shell as shown in the figure above
- Now we type
```
./flow.tcl -interactive
```
- If the 'interactive' keyword is not present, then the entire flow of the tool is run.


- Now we must import all the packages required to run the flow, we use the command:
```
package require openlane 0.9
```

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/42efcc21-5170-4141-a308-bfc345b5d1cb)

- We will be working with the 'picorv32a' design.
- The src folder has the verilog file and the sdc file of the design
- Now we do the design setup stage using the command:
```
prep -design picorv32a
```

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/914d607a-dd9e-4b4f-96d0-6b9b7e4d5e1c)

- After preparing the design, we can see that a new 'runs' folder is created.

- To synthesize the design we type
```
run_synthesis
```
- This command invokes yosys, runs the synthesis and the abc commands.
- A long process is observed after typing this command, which for a little over two mintues.

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/8d5d303a-1462-4927-bcff-28ff32bf4676)
- A synthesis successful message must be displayed.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/c0747e9d-b9ef-4609-ac75-005bb8fc46b6)
- The flop ratio can be calculated by using:

No. of flops/No. of cells = 1613/18036 = 0.0894

- In percentage there is 8.94% of the total number of cells are Flops

# Day 2
## Chip Floor Planning Considerations

**Utilization Factor and Aspect Ratio**

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/72957f9f-313a-4847-be97-2c7f15506417)
- We consider a simple netlist with a Launch and Capture Flop. It also has an AND and OR gate.
- We then convert it into squares since we need appropriate dimensions

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/a8604ec1-ea29-4e6d-8e2f-e6cab8286d6d)
- Let us consider the areas of the gates and Flops as 1 sq unit

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/a155adc9-fc25-4e79-90e2-9b377fea5930)
- Clubbing them together we get an area of 4 sq units

- The 'core' section of a chip is where the fundamental logic design is placed.
- The 'die' area contains the core and is a small semiconductor are on which the fundamental circuit is fabricated.

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/aee6154a-0525-4617-acfc-9c8c78189b8d)
- Now we put the netlist in the 'core' area and check the utilization.
- Here
```
Utilization Factor = Area Occupied by the Netlist/Total Area of the Core
```
- As we can see here, there is 100% utilization and ```Utilization Factor = 1```.
- In practical scenarios we don't go for such a high utilization factor.
- The 'Aspect Ratio = Height/Width = 1'.

**Concept of Pre Placed Cells**

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/2481ab00-b371-44a4-b5f9-57088876377b)
- We take the above combinational logic as an example

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/7464edc3-edf9-46ae-8e96-6692ab4be304)
- We split the circuit into two parts, block 1 and block 2 as shown above

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/0e569427-b73c-4b4e-b489-519836d21bb0)
- We extend the I/O pins and black box the boxes.
- Now we separate the boxes and the get their respective I/O ports.
- The use of doing this is that the users can use the blocks multiple times and form the required final circuit with ease.
- They only need to implement the design once and it can be reused.
- These kind of IPs have user defined locations and are placed in the chip before automated placement and routing takes place. These are called pre-placed cells.

**Surrounding Pre-Placed Cells with Decoupling Capacitors**

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/590aa6a4-9446-43a6-ac6c-13795d1ac818)

- Huge capacitor filled with charge. The equivalent voltage across the capacitor is similar to what the power supply produces.
- We add the capacitor in parallel to the circuit.
- Everytime the circuit switches it draws current from the decoupling capacitor, whereas the outer network with the power supply and other componets is used to re-charge the capacitor

**Pin Placement**
- In pin placemnt step we use the HDL netlist to determine where a specific pin should be placed in the circuit.
- We join the common pins and try to keep the connections as effecient as possible.
- Pins are placed in the Die area.

**Steps to run FLoorplan using OpenLANE**
- To view floorplan we type
```
run_floorplan
```
in the OpenLANE shell.

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/ed828ecf-10eb-4bbf-a33a-6e48dcf558a2)

- To open the Floorplan we go to the required directory that is
```
vsduser@vsdsquadron:~/Desktop/work/tools/openlane_working_dir/openlane/designs/picorv32a/runs/11-09_15-36/results/floorplan
```
using the ```cd``` command.

- Then we type the command:
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.floorplan.def &
```

- The following layout is displayed

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/b7648d2b-1814-465e-b93d-1cdf49aa8b1d)
- We can press 's' and then 'v' to align the design to the center of the screen.

- We can right click on the mouse and pess 'z' to zoom into a desired part.

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/70c27130-e482-4c58-a13b-251a3ce90824)
- We can see here that the I/O ports are equidistant

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/e87939b1-9c94-4bc0-b782-b77e1d37f552)
- We can check the details of the ports as follows
  - Hover over a port with your crosshair and press 's' on your keyboard
  - Now open the tkcon command window and type ```what```.
  - This will show you the details of the selected port.

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/876f0bb0-6e34-45c2-9ba0-6f4b6c147bba)
- If we zoom in a little more, we can see the tap cells.
- They are present to prevent latch up conditions which occur in the CMOS devices

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/703252c8-fe34-48bb-bc2b-b9ef28f4dd51)
- These are the standard cells that are used in the design

## Library Binding and Placement

**Netlist Binding and Initial Place Design**

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/ae6fe3da-5dd4-4c5d-9eea-80e6af1e4ba6)
- In real life, the logic gates and cells do not have shapes, but are present in the form of rectangles and squares.
- Hence they have dimensions to them and the space where they are placed must be utilized carefully
- The above picture shows an example of a library.
- Library consists of various kinds of cells which have different shapes and sizes, flavours and different timing information.

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/ed45d3a0-50d7-4f6f-a3f1-91452fb741e4)
- The components of the netlist are placed in the core area.
- They are placed according to the convenience of distance from the pins.
- When sending signal from FF1 to FF2, according to the circuit requirements, there has to be a very fast propogation of signals. Hence, they are placed very close and buffers are added since there is a small delay for the signal from the pin to reach FF1. The buffers maintain signal integrity

**Viewing the Placement**
- To view the placement we type
```
run_placement
```
in the OpenLANE shell.

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/9fef6c00-fbf6-4db9-8a8b-d3ebf3f60503)
- This is the result displayed. As we can see the '/picorv32a.placement.def' file is read.
- We move one directory up from the 'floorplan' folder using
```
cd ..
```
- To view the placement design we use the command
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def
```

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/a0cbf93a-c94b-452e-942d-2585e5aa3f11)
- The above is displayed.
- All these standard cells were present at the initial layout of the floorplan.

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/f010d7f8-25b8-4ee3-b4f6-19f84bcb5a8a)
- If we zoom in we can see the placement of the standard cells in the standard cell rows.

## Cell Design and Characterization Flow

**Cell Design Flow**
- Inputs -> Process design kits(PDKs) : DRC and LVS rules, SPICE models, library and user-defined specs.
- Design Steps -> Circuit Design, Layout Design(Euler Path and Stick Diagram), Characterization.
- Outputs -> CDL(Circuit Description Language), GDSII, LEF, extracted spice netlist(.cir)

**Characterization Flow**
- This is for an inverter.
1) Read the model files.
2) Read the extracted SPICE netlist.
3) Recognize the behaviour of the buffer.
4) Attaching the necessary power sources
5) Apply the stimulus, which is the input signal to the circuit.
6) Read the sub-circuit of the inverter.
7) Provide necessary output capacitances.
8) Provide the necessary simulation commands

**Timing Characterization**
- slew_low_rise_thr = 20%
- slew_high_rise_thr = 80%
- slew_low_fall_thr = 20%
- slew_high_fall_thr = 80%
- in_rise_thr = 50%
- in_fall_thr = 50%
- out_rise_thr = 50%
- out_fall_thr = 50%

- Propogation delay = time(out_fall_thr) - time(in_rise_thr)

- Transition Time
  - On rise: time(slew_high_rise_thr) - time(slew_low_rise_thr)
  - On fall : time(slew_high_fall_thr) - time(slew_low_fall_thr)

# Day 3
## Labs for CMOS inverter ngspice simulations

## Inception of Layout and CMOS Fabrication Process
**SPICE Deck Creation for CMOS Inverter**
- SPICE Deck is a netlist that has information on:
  - component connectivity 
  - component values
  - identifying the nodes
  - giving a designation to the nodes

**SPICE Simulation and Switching Threshold**

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/15efd9e8-a64d-4d9a-ae76-3bfb92928f84)
- The CMOS on the right side has a bigger size than the one on the left.
- These waveforms tell us that the CMOS is a very robust device. The characteristics of the CMOS are maintained across a variety of sizes.
- The arrow is pointing to the point where 'Vin = Vout'.

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/ad3e438f-de14-4ec2-bd76-6d4fc4af7bfe)
- Above graph gives details on each point and its significance

**A Git Clone and some other Steps**

- We need to perform a git clone here from a repository that we require, to do the future labs.
- We can type the following command
```
git clone https://github.com/nickson-jose/vsdstdcelldesign.git
```

- Now we need to copy the 'sky130A.tech' file into the directory we just cloned
- We can do this by using
```
cp sky130A.tech /home/vsduser/Desktop/work/tools/openlane_working_dir/openlane/vsdstdcelldesign
```
in the follwoing directory shown in the figure

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/c0cefbbc-dfd8-40b0-859e-3603e5589416)

**16 Mask CMOS Process**
1) Selecting a Substrate - Selecting the appropriate substrate to synthsize the design on.
2) Creating active reagion for transistors - Adding layers of SiO2(40nm), Si3N4(80nm) and photoresist(1um). On top of the photoresist we put a mask layer. Pass UV light and remove the mask. Resist is removed. LOCOS(Local Oxidation of Silicon) is performed. Si3N4 is etched.
3) N-Well and P-Well formation - The next masks are used to create the source and drain regions of the MOSFETs. Boron is used to make P-Well using ion implantation. Phosphorus is used to create N-Well. Put the MOSFET in a Drive In furnace.
4) Formation of Gate - Gate formation involves depositing a gate oxide, defining gate patterns using photolithography, depositing gate material, etching to create gates, doping the substrate and insulating the gates.
5) Lightly Doped Drain Formation(LDD) - Lightly doped drain (LDD) formation involves implanting the drain and source regions of a MOSFET transistor with a lighter concentration of dopants to reduce hot electron effect and short channel effect and enhance device performance.
6) Source and Drain Formation - Source and drain formation in a MOSFET transistor typically involves doping the silicon substrate with chemicals such as arsenic or phosphorous for n-type regions (source and drain) and boron for p-type regions (source and drain). High temperature annealing is performed.
7) Steps to form Contacts and Interconnects(local) - Titanium is deposited with a process known as sputtering. Wafer is heated to about 650 - 700 C in an N2 ambient furnace for 60 seconds. TiSi2 contacts are formed.  TiN is also formed used for local communication. TiN is etched using RCA cleaning.
8) Higher Level Metal Formation - Forming contacts and interconnects locally involves depositing a dielectric material like silicon dioxide, patterning it using photolithography, etching contact holes, depositing a barrier metal (e.g., titanium or titanium nitride), filling with a conductor (e.g., aluminum or copper) using chemical vapor deposition (CVD), and then planarizing through chemical-mechanical polishing (CMP).

**Sky130 Basic Layers Layout and LEF using Inverter**
- Now let us look at the layout of a CMOS inverter. To open this we type the command

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/f488e2ef-1afe-42a0-9028-835ae446a797)
```
 magic -T sky130A.tech sky130_inv.mag &
```
![image](https://github.com/akshatva7/pes_openlane/assets/135726741/a9cad291-c120-491b-b8e1-a704a22d724c)

- The following layout is displayed.
![image](https://github.com/akshatva7/pes_openlane/assets/135726741/dad32c61-2a53-4114-b599-93a4025481da)

- We can get to know the details of the inverter by hovering the mouse cursor over it and pressing 's' on the keyboard. Then we can type ```what``` in the tkcon.
- Pressing 's' three times will show what parts are connected to the selected part.

- We shall look at the difference between LEF and Layout. The above image is a Layout.
- LEF represents abstract component data in a machine-readable format for IC libraries, while layout is the physical geometric arrangement of these components on a semiconductor chip.

**Steps to Create Standard Cell Layout and Extract Spice Netlist**

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/cd23b6c5-5683-4299-b401-4936472f337b)
![image](https://github.com/akshatva7/pes_openlane/assets/135726741/cae149da-cb11-465b-bfba-80670ee17f00)
- DRC errors can be viewed in the tkcon.

To extract Spice Netlist we perform the following steps in the tkcon window:

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/02025ca9-7763-406e-a026-454a38f5c50d)
- We use the commands
```
ext2spice cthresh 0 rthresh 0 -> this is done to copy the parasitic capacitances
```
- The next command is
```
ext2spice
```
![image](https://github.com/akshatva7/pes_openlane/assets/135726741/9b92eadc-cf3f-4481-be1b-fe8b14d4628e)
- We can see that a sky130_inv.spice file is created

## Sky130 Tech File Labs

**Create Final SPICE Deck**
- To start off we look at the minimum value of the layout window.

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/3d79c52b-0377-4ebb-9d57-0db499c500bf)
- We can use 'g' on the keyboard to activate the grid and after selecting a grid by right clicking on the mouse, we type ```box``` in tkcon window to check the minimum value of the layout window.

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/9cae262f-fb5d-4959-a5a9-8e93869a4189)
- Next we need to open the spice file using the command
```
gedit sky130_inv.spice
```
- We need to configure it to the above specifications.

**Characterize Inverter using Sky130 Models**
![image](https://github.com/akshatva7/pes_openlane/assets/135726741/3161fa00-0af0-4d8c-9504-31b6752a9d57)
- We now plot the graph for output vs input sweeping the time.
- We first use the command
```
ngspice sky130_inv.spice
```
- In the ngspice shell we use the command
```
plot y vs time a
```
![image](https://github.com/akshatva7/pes_openlane/assets/135726741/b63006e2-378b-4586-882b-b2206a68e341)
- The following graph is displayed.

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/6e3a3d52-7554-4642-b971-e9b139156f82)
![image](https://github.com/akshatva7/pes_openlane/assets/135726741/0e6c002d-f86b-468d-8a4c-fae0efa78b07)
- Rise Time -> time taken to rise from 20% to 80% of the max value -> 2.25075e-09 - 2.184e-09 = 0.006675e-09 s.

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/f63b5f9e-764a-458b-b4d4-4b8b73094e02)
![image](https://github.com/akshatva7/pes_openlane/assets/135726741/d3b58b62-079a-45ff-9080-54510b1751c9)
- Propogation Delay/Cell Rise Delay -> 2.21379e-09 - 2.15e-09 = 0.06379e-09 s.

**Sky130 PDKS and Steps to Download Magic Tool**
![image](https://github.com/akshatva7/pes_openlane/assets/135726741/01ff2db6-6a8f-4b26-9518-225502e0dbe8)
- Enter the command
```
 wget http://opencircuitdesign.com/open_pdks/archive/drc_tests.tgz
```

- Move the file to desktop using
```
mv drc_tests.tgz Desktop/
```

![image](https://github.com/akshatva7/pes_openlane/assets/135726741/316a094f-e1e3-4742-8e18-7ad23737a2be)
- Extract the file using
```
tar xfz drc_tests.tgz 
```
- Do ```ls``` to view all the files in it.

To open the software we type
```
magic -d XR
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/8571a471-4937-4b89-8197-99c2648dfa66)
- We click 'file' and open the 'met3.mag' file.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/cbec69ce-fedb-41cd-af31-0dd9750c1ed9)
![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/b9551c50-a44b-472b-bf63-d4979c2f8caa)
- If we select an area and type ```drc why``` in the tkcon wndow, it will show us the DRC error.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/53fdfab7-7d24-4638-9fb3-855589c89b83)
- To add contact cuts to metal3, first select an area using left and right click. Then hovering over the m3contact we click middle mouse button.

**Fixing DRC Errors**
- There is a DRC error in the poly.mag file in 'poly.9'.
- Open the sky130A.tech file in the editor and make the following changes

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/d786e946-95e0-42c8-a0bd-a83a57697e04)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/4274217a-ba1b-499d-b81a-fa26b4262301)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/efafb2d3-520d-4405-b1ae-731b0308ac99)
- Now open the tkcon window and type
```
load tech sky130A.tech
drc check
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/ebd7630c-128d-46ba-adb7-a30169ba7fe6)
- As we can see the error is fixed.

**DRC Error as Geometrical Construct**
- We open the nwell.mag file.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/4c49e5c5-4738-449c-9df0-048d2034825d)
- We type the above commands

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/40097614-0dfe-4479-b85c-4418e7b43787)
- The following is displayed

**Find Missing or Incorrect Rules and Fix Them**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/338b998a-9a6b-4485-8d2b-095fbbcf3d83)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/cc00bc2f-31ed-45b3-96d8-a12b28697bd4)
- As we can see this is an incorrect implementation and the above rule is violated.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/d689590d-b9a7-43b2-b1da-72e95a9f4dcb)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/71d62451-245b-4010-8246-234727062c8b)
- We make the following changes

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/08900ffa-8f91-4550-be8c-375b4cb42866)
- Now we select the nwell.4 and type the following commands
```
tech load sky130A.tech
drc check
drc style drc(full)
drc check
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/dbb58cd3-4845-4d28-a22b-0228b8260cf6)
- As we can see the error still persists
- We can fix it by the following method.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/4bc70446-9a20-47cd-95b9-d850e170887a)
- Select the existing nwell.4 and make a copy of it by selecting it and clicking 'c'.
- Now select a small area on the nwell.4 and add an 'nsubstratecontact' by hovering over it and clicking middle mouse button.

# Day 4
## Timing Modelling using Delay Tables

**Convert Grid Info to Track Info**

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/0f4f84e4-2465-4275-b548-8c5f4fe3020a)
- We must go to the following directory and type
```
less tracks.info
```
![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/3ff966c6-0567-4a44-8639-e128d040b188)
- The 'tracks.info' file is used during the routing stage.
- Routes are the metal traces.
- Since the PNR is an automated flow, we need to specify where all we want the routes to go.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/15744b5a-0fc9-4ad5-b4ce-dfefd26e860d)
- Now we converge the grid definition in the layout to track definition, by typing the following command

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/dbc80960-e5cb-4b75-9f8d-d30c0e48febb)
- The following is the result.
- This shows that the routing of 'li1' layer can happen only along this grid

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/026c5733-e48a-4454-8bed-82a3dd6e1697)
- Having the ports at the intersection of horizontal and vertical tracks ensure that the route can reach that port from the 'y' as well as 'x' direction.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/a1022ba3-b42d-4fed-987f-d4823c0e5550)
- The next requirement is that the width of the cell should be the odd multiple of xpitch which is '0.46' as seen in the 'tracks.info' file.
- As we can see it encloses two full boxes and two halves of one box, totally making three boxes as indicated by the white rectangle.

**Convert Magic Layout to Standard Cell LEF**
- In the tkcon window of the 'sky130_inv.mag' file we type
```
save sky130_vsdinv.mag
```
to make our own .mag file

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/c5cbd3be-e520-4548-b863-51c211664688)
- To make the .lef file we type
```
lef write
```
to make our own lef file.

- Type ```less sky130_vsdinv.lef```.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/4da168e6-7999-4a15-a973-03a767694c51)
- The .lef file is as follows

**Steps to Include New Cell in Synthesis**
![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/a737393f-5802-4fcc-ac89-8fdbccec7307)
- We copy the .mag file that we created to the 'src' folder of picorv32a folder.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/7d6338d2-212a-40c7-853a-3054453f8d82)
- We then perform this copy command.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/acb2c280-ba86-4f84-947e-450fc242eee8)
- Next we modify the 'config.tcl' file in the picorv32a folder as follows.
- Open the OpenLANE interactive window and retrieve the 0.9 package.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/2d3fb8cd-43e6-46c2-b2de-69a338320fe4)
- Type the following
```
prep -design picorv32a -tag 16-09_19-58 -overwrite
set lefs [glob $::env(DESIGN_DIR)/src/*.lef]
add_lefs -src $lefs 
```
- Next we type ```run_synthesis```.

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/e3fdfe4f-0139-4bd1-8617-b9aa6c54d0e2)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/527db989-1cd9-403a-8b02-3028400befc0)
- The following results are displayed.

- To run floorplan and placement we type
```
init_floorplan
run_placement
```
- Now to view the design we type the command
```
magic -T /home/vsduser/Desktop/work/tools/openlane_working_dir/pdks/sky130A/libs.tech/magic/sky130A.tech lef read ../../tmp/merged.lef def read picorv32a.placement.def &
```

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/84fad4e0-bda4-4026-a9a4-4dc9bc9edec6)

![image](https://github.com/AniruddhaN2203/pes_pd/assets/142299140/7fd1b736-d6dd-4a98-8567-1ee6d0cc5813)
- The following is displayed.
- Zooming into the design using 'z' we can see the sky130_vsdinv than we defined.
- We have plugged in our custon cell in the OpenLANE flow.
