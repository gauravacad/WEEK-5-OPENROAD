# PART B 💎 OpenROAD Physical Design Stages  
This document describes key phases of the physical design process in OpenROAD—focusing on **Floorplanning** and **Placement**—which are essential for efficient, timing-closed, and manufacturable ASIC layouts.

---
# 💪 Floorplanning  
## 🧭 Definition  
**Floorplanning** is the process of defining the physical structure of a chip, including its shape, size, and spatial organization. It allocates space for all major components—macros, I/O pads, standard cells, and power networks—before detailed placement begins.  
A well-designed floorplan ensures optimal wire lengths, balanced power delivery, and minimal routing congestion.

---
## 🌆 Key Floorplan Components  
### **1. Die Area**  
- Represents the total physical silicon footprint of the chip.  
- Encloses all design elements including the core, I/O ring, and boundary structures.
### **2. Core Area**  
- The central region where all standard cells and macros are placed.  
- Determined by the **core utilization** (usually 70–85%), calculated as:  
**Core Utilization = (Total Standard Cell Area) / (Core Area)**
---
## 📍 I/O Pad and Pin Placement  
- I/O pads are positioned along the periphery of the die.  
- They serve as the interface between internal logic and external signals.  
- Proper pad placement ensures signal integrity and simplifies top-level routing.
---
## ✋ Macro Placement  
- **Macros** are large, pre-designed blocks such as SRAMs, PLLs, or IP cores.  
- Key guidelines for macro placement:  
- Position macros near the logic they interact with to minimize wire length.  
- Use **flylines** to visualize signal connectivity.  
- Leave spacing to avoid routing congestion and ensure power access.
---
## 📏 Standard Cell Row Creation  
- The core area is divided into **horizontal rows** that define legal placement sites for standard cells.  
- Each row includes **VDD and GND rails**, ensuring power connectivity for all cells.  
- Consistent row orientation improves routing efficiency.
---
## ⚡ Power Planning (PDN)  
- The **Power Distribution Network (PDN)** ensures uniform voltage delivery across the design.  
- Constructed using thick metal straps for **VDD** and **GND** in a grid pattern.  
- Prevents **IR drop** and **electromigration**, which can lead to timing instability.
---
## 🧱 Physical-Only Cells  
These are non-functional cells essential for maintaining the chip’s physical and electrical integrity:  
- **Tap Cells:** Connect wells and substrate to avoid latch-up.  
- **Endcap Cells:** Seal the ends of standard cell rows.  
- **Filler Cells:** Maintain metal continuity and meet density rules.  
- **Blockages:** Reserve space to control congestion near macros or power routes.
---
## 🩳 Summary  
A good floorplan minimizes wire length, optimizes timing, and provides robust power delivery.  
It lays the groundwork for effective placement and routing, directly influencing chip performance and manufacturability.


---
# 🅿️ Placement  
## 🎯 Definition  
**Placement** arranges all standard cells and smaller blocks within the defined core area of the floorplan. Its goal is to achieve the best physical layout that meets design constraints such as timing, congestion, and area utilization.
## 🧩 Placement Flow  
1. **Global Placement:** Roughly positions cells to minimize total wire length and timing violations.  
2. **Detailed Placement:** Fine-tunes cell locations to legal sites, ensuring design rule compliance.  
3. **Timing Optimization:** Adjusts positions for slack recovery and critical path balancing.  
---
### satges of placement 
## 🌍↔️ Global Placement: 
- Goal: Find the approximate best location for every standard cell to minimize overall wire length (often using the HPWL metric you saw earlier).
- Process: Uses complex algorithms (like quadratic placement or partitioning). At this stage, cells might overlap or not be perfectly aligned to the rows. It prioritizes optimal positioning based on connections.

## 📏✅Detailed Placement (Legalization): 
 - Goal: Take the rough locations from global placement and make them legal. This means snapping each cell precisely onto the standard cell rows defined in the floorplan and ensuring no cells overlap.
- Process: Makes small, local adjustments to cell positions while trying to preserve the wire length optimization achieved during global placement. It strictly adheres to the placement grid and design rules.

## 🔥 Objectives  
### **1. Minimize Wire Length**  
- Place connected cells close to each other to reduce interconnect length.  
- Shorter wires improve timing, reduce capacitance, and lower dynamic power.
### **2. Reduce Congestion**  
- Spread cells evenly across the core to prevent over-crowding.  
- Enables efficient signal routing and minimizes DRC violations.
### **3. Meet Timing Requirements**  
- Placement must maintain the correct signal propagation time between sequential elements.  
- Optimize cell positions to satisfy **setup** and **hold** constraints within each clock cycle.
---
## 📊 Outcomes  
- A **legalized, congestion-free cell layout** that honors timing and power constraints.  
- Ready for subsequent design phases such as **Clock Tree Synthesis (CTS)**, **Routing**, and **Signoff**.

---
# 🧠 Summary of Both Stages  

| Stage | Objective | Key Outputs | Tools Involved |
|--------|------------|--------------|----------------|
| **Floorplanning** | Define chip area, place macros & power grid | Floorplan DEF, PDN structure | `initialize_floorplan`, `place_macros`, `pdngen` |
| **Placement** | Arrange standard cells for optimal timing & routing | Placed DEF, timing reports | `global_placement`, `detailed_placement`, `resizer` |

---
**In essence:**  
- **Floorplanning** builds the physical foundation of the chip.  
- **Placement** refines that foundation by positioning logic for maximum performance and routability.  
Both are critical to achieving a successful, manufacturable ASIC layout using OpenROAD.
---

## 🗃️Files needed
### 📚Placement tools need:
- Floorplan DEF/ODB: The output from the floorplanning stage, defining the core area, rows, macro locations, and I/O pins.
- Synthesized Netlist (.v): Describes which standard cells are used and how they are connected.
- Libraries (.lef, .lib): Provide the physical shapes (LEF) and timing information (LIB) for all standard cells. 

## 🛣️now we will do the floorpalnning and placement in openroad.
### 📍so I have done it for gcd_nangate45_clean.tcl
### 📂file_1 for floorplanning while working On First example design GCD_nand_gate45

```bash
# gcd flow pipe cleaner
source "helpers.tcl"
source "flow_helpers.tcl"
source "Nangate45/Nangate45.vars"

set design "gcd"
set top_module "gcd"
set synth_verilog "gcd_nangate45.v"
set sdc_file "gcd_nangate45.sdc"
set die_area {0 0 100.13 100.8}
set core_area {10.07 11.2 90.25 91}

#include -echo "flow_floorplan.tcl"
#include -echo "flow_pdn.tcl"
#include -echo "flow_global_placement.tcl"
include -echo "flow_detailed_placement.tcl"
```
### 🏃‍➡️run this command in the tereminal 

```bash
openroad -gui -log gcd_logfile.log gcd_nangate45_clean.tcl 
```

### 😎The GUI of OpenROAD Tool software Will Open

**ScreenShot:** The picture shows that openroad GUI is successfully running the Gcd_nandgate45.tcl example on Ubuntu 24 WSL.
<img width="870" height="762" alt="image" src="https://github.com/user-attachments/assets/9d075296-cfe2-4c95-a370-616152448835" />

**ScreenShot:** The picture shows that Core Area.
<img width="870" height="762"  alt="image" src="https://github.com/user-attachments/assets/eed5d953-617d-4bac-8df3-ec65b4c7e066" />

### 📂 file_2  🧩OpenROAD Flow Floorplan Script (flow_floorplan.tcl)
- This script defines the floorplanning stage in the OpenROAD flow.
- It sets up libraries, reads design sources, initializes the die/core area, places macros, and inserts tapcells.

``` bash
# Assumes flow helpers.tcl has been read.
read_libraries
read_verilog $synth_verilog
link_design $top_module
read_sdc $sdc_file

set_thread_count [cpu_count]

# Temporarily disable STA threading due to random failures
sta::set_thread_count 1

# Metrics for debugging
utl::metric "IFP::ord_version" [ord::openroad_git_describe]
utl::metric "IFP::instance_count" [sta::network_instance_count]

# Initialize the floorplan
initialize_floorplan -site $site \
                     -die_area $die_area \
                     -core_area $core_area

# Load track definitions
source $tracks_file

# Remove buffers inserted by synthesis
remove_buffers

# Source pre-placed macros if provided
if { $pre_placed_macros_file != "" } {
    source $pre_placed_macros_file
}

###########################################################
#                    Macro Placement
###########################################################
if { [have_macros] } {
    lassign $macro_place_halo halo_x halo_y
    set report_dir [make_result_file ${design}_${platform}_rtlmp]
    rtl_macro_placer -halo_width $halo_x -halo_height $halo_y \
                     -report_directory $report_dir
}

###########################################################
#                    Tapcell Insertion
###########################################################
eval tapcell $tapcell_args ;# tclint-disable command-args
```

🏗️ Functional Summary
| **Stage**                | **Command / Function**                    | **Purpose**                                                            |
| ------------------------ | ----------------------------------------- | ---------------------------------------------------------------------- |
| Library Loading          | `read_libraries`                          | Loads the required technology and standard cell libraries.             |
| Design Input             | `read_verilog`, `link_design`, `read_sdc` | Reads the synthesized netlist and timing constraints.                  |
| Threading Control        | `sta::set_thread_count 1`                 | Disables multi-threaded STA due to instability.                        |
| Metrics Logging          | `utl::metric`                             | Records OpenROAD version and instance counts for debugging.            |
| Floorplan Initialization | `initialize_floorplan`                    | Defines die and core areas and site grid.                              |
| Track Definition         | `source $tracks_file`                     | Reads routing track information from a technology file.                |
| Cleanup                  | `remove_buffers`                          | Removes unnecessary synthesis buffers before physical steps.           |
| Macro Placement          | `rtl_macro_placer`                        | Places large pre-designed blocks (macros) based on halo spacing.       |
| Tapcell Insertion        | `eval tapcell $tapcell_args`              | Inserts tapcells to prevent latch-up and maintain substrate integrity. |

## 🔭Obseravtions 
- The layout view shows a defined Die Area (outer white box) and Core Area (inner area with rows).
- Inside the core area, We can clearly see the horizontal Standard Cell Rows (green lines) have been created.
- This indicates the Floorplan Initialization stage is complete.
## ⏭️ Now, We will run the next stage 



### 📂 file_4 flow_pdn.tcl

<img width="1317" height="890" alt="Screenshot 2025-10-25 044310" src="https://github.com/user-attachments/assets/30513068-5307-439a-9309-f7afbbb2712c" />

##  📂now again if we run with the gcd_nangate45_clean.tcl


### 🔋 power line(shown img)

<img width="1345" height="649" alt="Screenshot 2025-10-23 221602" src="https://github.com/user-attachments/assets/8be6d8f9-a933-48b8-b42c-f1a82c58b36f" />



###  ⛱️ground line (pink_line)

<img width="533" height="506" alt="Screenshot 2025-10-23 221614" src="https://github.com/user-attachments/assets/e2b6ab9d-ac41-454d-b181-7788d0ced47b" />

## 🔭Obseravtions

- In this we can see we have placed sucessfully power and ground lines on the chip.
- With the power infrastructure laid out, the standard cell rows (green/blue lines) now have access to power and ground. The design is structurally ready for the Placement stage, where the logic cells will be placed onto these rows and connected to these power lines.


## ⏭️now our next step is the placement stage 

### 📂 file_4 flow_global_placement.tcl

<img width="1360" height="1003" alt="Screenshot 2025-10-25 045304" src="https://github.com/user-attachments/assets/6d3d8106-ebe8-403d-bb01-5e1d5f6e4826" />


<img width="1856" height="754" alt="Screenshot 2025-10-25 045329" src="https://github.com/user-attachments/assets/310c208c-c044-4ea4-b3f8-cc7dd1a78480" />


### 🔮reseult we get 

<img width="845" height="814" alt="Screenshot 2025-10-25 050318" src="https://github.com/user-attachments/assets/b1362a41-742b-40fb-a24b-284fd097e1f0" />






<img width="1856" height="1011" alt="Screenshot 2025-10-23 225225" src="https://github.com/user-attachments/assets/e2230332-a2b4-4855-8721-5f37d7712030" />

## 🔭Obseravtions 

- in this we can see that all the standered cells are placed but they are overlapping ,becuase we have not done yet the proper detailed placement

- there are the I/O pins at the sides of the chip ,it is like and small uppar part of arrow.
  
  - the standard cells (small red blocks) are now placed onto the horizontal rows within the core area. This confirms that both Global Placement and Detailed Placement (legalization) have finished. 

- The cells appear somewhat clustered towards the center and middle rows, leaving some rows less densely populated. This distribution is determined by the global placement algorithm trying to minimize wire length based on the netlist connections.


## ⏭️now we will do the detailed placement



### 📂 file_5 flow_detalied_placement.tcl

<img width="967" height="641" alt="image" src="https://github.com/user-attachments/assets/ecc8d6d1-58d5-4475-a33f-a11f43e34b6a" />


###  🎩result we get

<img width="967" height="778" alt="Screenshot 2025-10-25 051321" src="https://github.com/user-attachments/assets/f6a2c71c-3f31-4239-9d6c-da8a3d5b8416" />




<img width="1851" height="1049" alt="Screenshot 2025-10-23 231413" src="https://github.com/user-attachments/assets/e640a19a-76ba-450e-a8d9-2a512dadc0cf" />



**ScreenShot:** The picture shows that openroad GUI is successfully Mapped
<img width="870" height="762"  alt="image" src="https://github.com/user-attachments/assets/9bf5231e-ec0a-4e5b-9aba-51e1f9171cdc" />

## 🔭Observations 

- in this we can see that standered cell are not overlapping as it was case in the previous image 
- The layout view clearly shows the standard cells (small red blocks) placed onto the horizontal standard cell rows (green/blue lines)

- Timing Analysis Results : Worst Negative Slack (WNS) for Setup Timing. Since it's positive (+0.106 ns), the design currently meets its setup timing requirements.

- This usually represents the worst Hold Slack. The small negative value (-0.009 ns) indicates very minor hold time violations. These are typically fixed later during clock tree synthesis (CTS) or optimization.



