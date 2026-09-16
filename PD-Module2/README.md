# 🏗️ Physical Design – Module 2
## From Netlist to Floorplan: Chip Layout and Power Integrity Fundamentals

## 📌 About This Module

This module continues the Physical Design (PD) learning path, digging into the core concepts behind **ASIC chip floorplanning and power integrity**.

It follows the journey of a logical netlist as it becomes a physical layout — fixing core and die dimensions, computing cell area, choosing utilization and aspect ratio, and arranging standard cells alongside pre-placed IP blocks inside the floorplan.

Alongside floorplanning, the module works through key power-integrity ideas: **switching current, IR drop, inductive voltage drop, noise margin, and decoupling capacitors**. These concepts are then grounded in practice using the **OpenLane flow**, **OpenROAD-based layout tools**, and the **SKY130 technology and standard-cell library**.

Later sections cover **floorplanning, power planning, PDN construction, standard-cell placement, placement blockages, tap cells, LEF/technology files, timing constraints**, and how logical netlist cells get bound to physical library cells.

Taken together, this module builds a step-by-step picture of how an ASIC design moves from a logical netlist to a structured, physically realizable layout.

---

## 🎯 What This Module Covers

- Core fundamentals of ASIC physical design and chip floorplanning.
- Reading a logical netlist and translating logical cells into physical form.
- Computing the area occupied by standard cells and flip-flops.
- The relationship between **core area, die area, utilization factor, and aspect ratio**.
- The role and placement strategy for **pre-placed cells and IP blocks**.
- How floorplanning choices ripple into routing, congestion, timing, and performance.
- Switching current and its downstream effect on power integrity.
- **IR drop** and inductive voltage variation across the power distribution network.
- **Noise margin** and how supply noise interacts with digital logic.
- Why and where **decoupling capacitors** get placed.
- **Power planning and Power Distribution Network (PDN)** design.
- The OpenLane physical design configuration flow.
- **SKY130 technology, LEF files, and standard-cell libraries**.
- Standard-cell placement, placement blockages, and tap-cell insertion.
- How logical netlist cells are bound to physical library cells.
- The full progression: **Netlist → Floorplan → Placement → Physical Layout**.

---

## 🛠️ Tools and Technologies

| Tool / Technology | Purpose |
|---|---|
| **OpenLane** | Automated open-source RTL-to-GDSII ASIC design flow |
| **OpenROAD** | Physical design implementation and layout visualization |
| **SKY130 PDK** | Open-source 130 nm process design kit |
| **sky130_fd_sc_hd** | High-density standard-cell library |
| **LEF Files** | Physical abstraction of standard cells, pins, layers, and obstructions |
| **Tcl** | Configuration and automation of the OpenLane flow |
| **Verilog** | Hardware description of the design |
| **SDC** | Timing and clock constraint definition |
| **KLayout / Layout Viewer** | Visualization and inspection of physical layouts |
| **Linux / Ubuntu** | Development and execution environment |
| **GitHub** | Project documentation and version control |

---

## 📚 Contents

1. From Netlist to Core and Die Dimensions
2. Mapping Netlist Symbols to Physical Cells
3. Computing the Netlist's Physical Area
4. Utilization Factor and Aspect Ratio, Defined
5. A Worked Core/Die Sizing Example
6. Where Pre-placed Cells Belong
7. Positioning Pre-placed Cells Correctly
8. IP Blocks Inside the Floorplan
9. Ringing Pre-placed Cells with Decoupling Capacitors
10. Switching Current and the Voltage It Costs You
11. Noise Margin, Explained
12. The Fix: Decoupling Capacitors
13. Placing Decoupling Capacitors Around Blocks
14. Laying Out Decoupling Capacitors in the Floorplan
15. Power Network Behavior: Driver, Load, and a 16-bit Bus
16. Floorplanning, Revisited
17. Power Planning
18. Building the Power Distribution Network
19. Running Picorv32a Through the OpenLane Flow
20. OpenLane Configuration for sky130_fd_sc_hd
21. Reading the SkyWater PDK LEF File
22. OpenLane Floorplanning Configuration
23. Standard Cell Placement
24. Decoupling Capacitors, Revisited
25. Logical Cell Placement Blockages
26. Tap Cells
27. LEF and Technology Files
28. OpenLane Configuration, Revisited
29. Timing Constraints
30. OpenROAD Layout View
31. Binding the Netlist to Physical Library Cells
32. Placement, Revisited

## 1. From Netlist to Core and Die Dimensions

Physical design starts with reading the netlist and translating its logical representation into physical dimensions.

A netlist captures the connectivity between the components of an electronic design.

The example netlist used here includes:

* Flip-Flops (FF)
* AND gate
* OR gate
* Clock connection
* Data connections

These standard cells and flip-flops are converted into physical dimensions once floorplanning begins.

<img width="1920" height="1080" alt="Netlist to physical dimensions" src="https://github.com/user-attachments/assets/99014082-f6ff-4e35-88ee-ec74ea45e9ad" />

---

## 2. Mapping Netlist Symbols to Physical Cells

Once the netlist is understood, its logical components get represented as physical standard cells.

The highlighted elements here include:

* Flip-Flops
* Standard cells
* AND/OR logic cells

Every logical cell occupies real physical area on silicon.

So before the core dimensions can be fixed, the total area occupied by every cell has to be calculated first.

<img width="1920" height="1080" alt="Mapping netlist to cells" src="https://github.com/user-attachments/assets/7f6fdf41-93b2-4a8a-a023-0f82d896f96b" />

---

## 3. Computing the Netlist's Physical Area

In this example, each standard cell and flip-flop is treated as a unit square:

```text
Width  = 1 unit
Height = 1 unit

Area = Width × Height
     = 1 × 1
     = 1 sq. unit
```

Summing the area of every standard cell and flip-flop gives the total netlist area — which is, in turn, the total cell area the core needs to accommodate.

<img width="1920" height="1080" alt="Netlist area calculation" src="https://github.com/user-attachments/assets/d657d5b0-e0b9-4d39-a04f-93521dcb6056" />

## 4. Utilization Factor and Aspect Ratio, Defined

Two parameters drive the core's dimensions:

**Utilization Factor**

```
Utilization Factor =
Area Occupied by Netlist
-------------------------
Total Area of Core
```

<img width="1920" height="1080" alt="Utilization factor" src="https://github.com/user-attachments/assets/211c4821-e0ff-4a35-88b0-c22edb6ab923" />

This factor reflects how much of the core's total area is actually occupied by placed cells.

**Aspect Ratio**

```
Aspect Ratio = Height / Width
```

In this example, core and die dimensions are chosen to hit a target utilization factor and aspect ratio — the diagram shows a core of roughly 4 × 2 units, with the die wrapping around it.

## 5. A Worked Core/Die Sizing Example

A second example shows how core and die dimensions shift as the utilization factor changes.

Physical dimensions need to leave enough room for:

* Standard-cell placement
* Routing
* Power distribution
* Decoupling cells
* Other physical-design needs

A lower utilization factor frees up more space inside the core, which tends to ease placement and routing congestion.

<img width="1920" height="1080" alt="Core and die sizing example" src="https://github.com/user-attachments/assets/f4c04f0c-9bda-410b-8c49-5ebc565f7a0e" />

## 6. Where Pre-placed Cells Belong

Certain cells and blocks can't be left to the automated placement tool — these are known as **pre-placed cells**.

Common examples:

* Memory
* Clock-gating cells
* Comparators
* Multiplexers
* Other large IP blocks

These get fixed, user-defined locations and are placed before automated placement and routing ever run.

<img width="1838" height="580" alt="Pre-placed cell locations" src="https://github.com/user-attachments/assets/7f1ef943-2c7e-4b80-936b-75a91e54e2b6" />

## 7. Positioning Pre-placed Cells Correctly

Where a pre-placed cell ends up matters — its position shapes the rest of the physical design.

This example shows two blocks, each holding multiple logic cells, arranged so the required I/O connections stay intact. I/O pins can be extended outward to make inter-block connectivity clearer.

Careful placement here helps cut down on:

* Routing congestion
* Wire length
* Timing problems
* Unnecessary routing detours

<img width="869" height="734" alt="Pre-placed cell positioning" src="https://github.com/user-attachments/assets/c3672a74-a831-4d00-9375-848b05389ac1" />

## 8. IP Blocks Inside the Floorplan

Modern ASIC designs often incorporate several pre-designed IP blocks — memory, clock-gating cells, comparators, multiplexers, and the like.

Arranging these blocks within the chip is what floorplanning actually is.

These IPs get user-defined locations and are dropped into the chip ahead of automated placement and routing — meaning floorplanning is what determines the physical organization of a chip's major blocks.

<img width="1175" height="581" alt="IP blocks in floorplan" src="https://github.com/user-attachments/assets/90606d97-76e4-4046-aea0-fc6383f393e2" />

## 9. Ringing Pre-placed Cells with Decoupling Capacitors

Pre-placed blocks can see heavy switching activity, which means they may demand a large instantaneous current.

Placing decoupling capacitors around these blocks helps improve power integrity. The floorplan in this example contains:

* Core
* Die
* Block A
* Block B
* Block C
* Decoupling capacitor regions

The decoupling capacitors act as a local charge reserve near the blocks, which reduces supply-voltage fluctuation during switching.

<img width="976" height="707" alt="Decoupling capacitors around blocks" src="https://github.com/user-attachments/assets/649a14fb-7099-47ea-a629-a24f177578f1" />

## 10. Switching Current and the Voltage It Costs You

A complex digital circuit can demand a large amount of instantaneous current during switching — this is the **peak switching current**.

The power supply network itself carries parasitic resistance and inductance. When switching current flows through these, a voltage drop follows.

Resistive voltage drop:

```
V = I × R
```

Inductive voltage variation:

```
V = L × di/dt
```

As a result, the resistance and inductance in the power network can leave the circuit with less voltage than the ideal supply value.

<img width="1309" height="700" alt="Switching current and voltage drop" src="https://github.com/user-attachments/assets/4c5a373c-cb90-42e4-bc8f-f3ad0b2d2792" />

## 11. Noise Margin, Explained

Noise margin is a circuit's tolerance for unwanted voltage disturbances without flipping the interpreted logic value.

**Noise Margin High**

```
NMH = VOH(min) − VIH(min)
```

**Noise Margin Low**

```
NML = VIL(max) − VOL(max)
```

The diagram illustrates different noise bumps against the available noise-margin levels. A small bump stays within the safe region and causes no logic error — but noise that exceeds the margin risks being read as an unintended logic transition.

<img width="1084" height="584" alt="Noise margin diagram" src="https://github.com/user-attachments/assets/eb0fa1ce-b52d-4bd0-b78b-58f30ebdd24e" />

## 12. The Fix: Decoupling Capacitors

One way to soften the impact of switching-current demand is to add a decoupling capacitor in parallel with the circuit.

When the circuit switches and needs a burst of current, the capacitor supplies it locally — and the power network then recharges the capacitor afterward.

```
Switching occurs
       ↓
Circuit demands current
       ↓
Decoupling capacitor supplies charge
       ↓
Power network replenishes the charge
```

This dampens the effect of sudden current demand on supply voltage.

<img width="1291" height="698" alt="Decoupling capacitor solution" src="https://github.com/user-attachments/assets/45ff5865-832c-46ff-bea8-be3e88695fa6" />

## 13. Placing Decoupling Capacitors Around Blocks

Decoupling capacitors can ring the pre-placed blocks that need them most, for example:

```
+--------------------------------+
|                                |
|        DECAP1                  |
|        Block A   Block B       |
|        DECAP2                  |
|        Block C                 |
|        DECAP3                  |
|                                |
+--------------------------------+
```

Keeping decoupling capacitors close to the blocks with the highest instantaneous current draw provides a local current source and improves overall power integrity.

<img width="1342" height="583" alt="Decoupling capacitor placement around blocks" src="https://github.com/user-attachments/assets/37256ea8-fe0c-4be8-b6c9-793212230bb4" />

## 14. Laying Out Decoupling Capacitors in the Floorplan

The floorplan can be organized into distinct regions for blocks and decoupling capacitors — in this example:

```
DECAP1
Block A
Block B
DECAP2
Block C
DECAP3
```

Decoupling capacitors are placed strategically around the pre-placed cells. Keeping them close cuts the distance the current needs to travel, which in turn reduces the impact of parasitic resistance and inductance along that path.

<img width="973" height="573" alt="Decoupling capacitor floorplan layout" src="https://github.com/user-attachments/assets/b7a7290e-28e2-416d-be5e-697e99b83ac1" />

## 15. Power Network Behavior: Driver, Load, and a 16-bit Bus

A final example shows the power network connecting multiple driver and load circuits.

The power distribution network carries its own resistance and inductance, while each connected circuit brings associated capacitance and switching demand.

The diagram also traces a signal path for a multi-bit bus — here, the blue path represents a 16-bit bus.

The core takeaway: switching activity across many signals at once can generate a large instantaneous current demand. That's why careful power-network design and decoupling matter for stable supply voltage and reliable circuit behavior.

<img width="1144" height="600" alt="Power network with 16-bit bus" src="https://github.com/user-attachments/assets/20c93dc0-0302-476e-855b-faa3aee40e1c" />

## 📐 16. Floorplanning, Revisited

Floorplanning is one of the earliest major physical-design stages, and it fixes:

* Die dimensions
* Core dimensions
* Core utilization
* Aspect ratio
* Standard-cell placement region
* I/O placement region
* Power distribution requirements

**Key Floorplan Parameters**

The configuration in this project touches on:

```
FP_CORE_UTIL
FP_ASPECT_RATIO
FP_SIZING
DIE_AREA
FP_IO_HMETAL
FP_IO_VMETAL
FP_IO_MODE
```

**Core Utilization**

`FP_CORE_UTIL` sets the approximate percentage of core area that standard cells will occupy. Getting this value right matters — too high, and routing gets difficult; too low, and chip area balloons unnecessarily.

<img width="1229" height="583" alt="Floorplanning parameters" src="https://github.com/user-attachments/assets/50da70e4-0414-4910-86fd-9266ffe07301" />

## ⚡ 17. Power Planning

Power planning builds the power-distribution network (PDN) that delivers stable supply voltage across the entire chip.

The screenshots show a power grid made of horizontal and vertical metal structures:

```
VDD
│
├── Horizontal Power Rails
│
├── Vertical Power Rails
│
└── Standard Cell Power Connections

VSS
│
├── Horizontal Ground Rails
│
├── Vertical Ground Rails
│
└── Standard Cell Ground Connections
```

This grid reduces voltage drop and delivers reliable power to cells spread across the core.

<img width="977" height="592" alt="Power planning grid" src="https://github.com/user-attachments/assets/6c65e942-8ee9-48a6-9e1b-b0d39fb58b5b" />

## 🔌 18. Building the Power Distribution Network

This section looks at how power structures are physically organized across the chip core.

The PDN is made up of:

* Horizontal power straps
* Vertical power straps
* Standard-cell power rails
* VDD connections
* VSS connections
* Power grid intersections

A well-built PDN matters because it:

* Reduces IR drop
* Improves power integrity
* Keeps supply voltage uniform
* Supports reliable standard-cell operation

<img width="1035" height="582" alt="Power distribution network" src="https://github.com/user-attachments/assets/d20df03f-6735-4feb-9788-f91c82fa8c63" />

## 19. Running Picorv32a Through the OpenLane Flow

OpenLane is an automated, open-source RTL-to-GDSII hardware design framework — it transforms human-readable Verilog RTL into the final physical layout blueprint (GDSII) needed to manufacture a silicon chip.

**Picorv32a**, an optimized RISC-V CPU core, serves as the design benchmark for this run.

The `config.tcl` file is the configuration hub for the whole process: it sets critical parameters — target layout names, input file paths, required clock speeds — that guide synthesis, placement, and routing without manual intervention.

<img width="958" height="934" alt="config.tcl file" src="https://github.com/user-attachments/assets/6290e875-280f-4602-b43a-8b427850888a" />

## 20. OpenLane Configuration for sky130_fd_sc_hd

Inside the OpenLane flow, HDL code becomes a physical layout — and that transformation leans heavily on `.tcl` configuration files that set constraints and optimization targets for synthesis, floorplanning, placement, and routing.

This particular configuration targets the **sky130_fd_sc_hd** standard-cell library (SkyWater 130nm High Density) and sets several foundational parameters:

**Synthesis & Timing Control** — `SYNTH_MAX_FANOUT` caps how many gate inputs a single logic output can drive, balancing signal integrity against delay. `CLOCK_PERIOD` sets the target clock cycle time in nanoseconds, defining the performance target for Static Timing Analysis.

**Floorplanning & Density** — utilization variables set how much of the core area standard cells will occupy. `FP_CORE_UTIL` establishes the initial budget, while `PL_TARGET_DENSITY` dynamically computes the target placement density — keeping cells tightly but safely packed without creating unroutable congestion later in implementation.

<img width="958" height="934" alt="sky130_fd_sc_hd configuration" src="https://github.com/user-attachments/assets/9421bc1b-2684-44ce-87c5-92d51a2d3396" />

## 21. Reading the SkyWater PDK LEF File

This section examines the structural and physical parameters defined in `merged_upadded.lef`, part of the open-source SkyWater PDK digital design flow.

**What a LEF File Is**

A LEF (Library Exchange Format) file holds the abstract physical layout data for a cell library. It gives place-and-route tools what they need — cell boundaries, pin locations, metal layers — without exposing the full internal silicon-level geometry.

The file snippet shown defines the technology's foundational attributes, manufacturing grid, and macro site definitions needed for structural placement:

**Technology Units** — establishes the scaling units for the design data, mapping database units to real physical measurements (for example, 1000 database microns per millimeter, resistance in Ohms, time in nanoseconds, and capacitance in picofarads).

**Manufacturing Grid** — defines the minimum grid spacing that every physical shape in the layout must align to, so that placement, routing, and cell geometry stay consistent with what the fabrication process can actually produce.

<img width="958" height="934" alt="SkyWater PDK LEF file" src="https://github.com/user-attachments/assets/9421bc1b-2684-44ce-87c5-92d51a2d3396" />

---

## 👤 Author

**Princy Florence**
B.Tech – Electronics & Communication Engineering
Anurag University
[RTL Workshop Repository](https://github.com/madapaamrutha-svg/RTL_Workshop)
