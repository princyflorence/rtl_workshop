# ASIC Physical Design Journey — RTL to GDSII with OpenLane & SKY130

## About This Work

This document walks through a hands-on implementation of a digital design using the **ASIC Physical Design flow**, built on **OpenLane** and the **SkyWater SKY130 open-source PDK**.

It traces every major stage of turning a synthesized netlist into a manufacturable layout — floorplanning, power delivery, placement, clock tree construction, routing, timing sign-off, antenna repair, and final physical verification — with the goal of building real, practical fluency in open-source ASIC implementation.

## Goals of the Project

- Understand each stage of the ASIC physical design pipeline end to end.
- Build and validate a floorplan with an appropriate power delivery network.
- Place and legalize standard cells while managing timing and congestion.
- Construct a balanced clock distribution network (CTS).
- Complete global and detailed routing successfully.
- Run Static Timing Analysis and interpret setup/hold slack.
- Detect and repair antenna rule violations.
- Sign off the layout through DRC and LVS.
- Evaluate how open-source EDA tooling performs across a real ASIC flow.
- Explore how different configuration choices shift the final implementation.

---

## Toolchain Used

| Tool | Role in the Flow |
|---|---|
| OpenLane | End-to-end automated ASIC implementation |
| SKY130 PDK | Target silicon process technology |
| OpenROAD | Core physical implementation engine |
| Yosys | RTL-to-gate-level logic synthesis |
| OpenSTA | Static Timing Analysis engine |
| Magic | Design Rule Checking / layout viewer |
| Netgen | Layout-vs-Schematic verification |
| SPEF | Post-route parasitic data for timing |

## Contents

1. Setting Up the Floorplan
2. Building the Power Delivery Network
3. Standard Cell Placement
4. Clock Tree Synthesis
5. Global & Detailed Routing
6. Antenna Effect: Detection
7. Antenna Effect: Repair
8. Static Timing Analysis
9. Post-Route Parasitic Extraction
10. Logic Equivalence Verification
11. Sign-off: DRC & LVS
12. Exploring Design Alternatives in OpenLane
13. How the OpenLane Flow Works
14. OpenROAD's Role Inside OpenLane
15. Inside the SKY130 PDK
16. Configuring an OpenLane Run
17. Floorplan Variables Reference
18. Design for Test (DFT) Considerations
19. Physical Implementation, Stage by Stage
20. Running the Flow
21. Reading the Results
22. Verification Checklist
23. Design Space Exploration
24. Regression Testing in OpenLane
25. The Flow, End to End
26. Terminal Logs & Command Evidence
27. What I Learned
28. Closing Thoughts

## The Flow at a Glance

```text
Synthesis
    ↓
Floorplanning
    ↓
Power Planning
    ↓
Placement
    ↓
Clock Tree Synthesis
    ↓
Routing
    ↓
Parasitic Extraction
    ↓
Static Timing Analysis
    ↓
DRC / LVS / Antenna Checks
    ↓
Final Layout
```

Each block above hands off a progressively more "physical" version of the design to the next.

---

## 1. Setting Up the Floorplan

Floorplanning is where physical design begins in earnest — it fixes the die and core dimensions and lays out how the design will occupy its silicon footprint.

Key floorplanning knobs include:

- `FP_CORE_UTIL`
- `FP_ASPECT_RATIO`
- `FP_SIZING`
- `FP_IO_MODE`
- `FP_IO_HMETAL`
- `FP_IO_VMETAL`
- `FP_PDN_*`

Together these settings govern utilization, die/core aspect ratio, I/O pin placement, metal-layer assignment, and the shape of the power network.

<img width="1792" height="971" alt="Floorplan configuration" src="https://github.com/user-attachments/assets/ac0877fb-b69b-4be6-983e-550a19eb3485" />

## 2. Building the Power Delivery Network

Power planning constructs the power distribution network (PDN) that feeds every standard cell in the design.

It provides dependable paths for:

- VDD
- VSS
- Standard-cell power rails
- Core-level power distribution

A sound PDN is essential — without it, the rest of the design has nothing solid to build on.

<img width="1790" height="906" alt="Power delivery network" src="https://github.com/user-attachments/assets/0d50ac3f-e9c5-43fe-a05e-23882d779087" />

---

## 3. Standard Cell Placement

Placement decides exactly where each standard cell sits inside the floorplan. It happens in stages:

**Global Placement** — spreads cells across the core while balancing wirelength, timing, and congestion.

**Placement Optimization** — refines cell positions to improve timing and routability.

**Detailed Placement** — legalizes final cell positions so they comply with the technology's physical rules.

<img width="1920" height="1080" alt="Cell placement" src="https://github.com/user-attachments/assets/d2610ebf-ec28-4a70-a43a-284c3080ed6b" />

## 4. Clock Tree Synthesis

CTS builds the clock distribution network that reaches every sequential element in the design. It aims to:

- Minimize clock skew
- Keep clock latency under control
- Maintain healthy clock signal transitions
- Deliver the clock reliably to every flip-flop

Buffers (and occasionally other cells) are inserted along the way to shape the tree correctly.

<img width="1733" height="903" alt="Clock tree synthesis" src="https://github.com/user-attachments/assets/f724ebf5-2401-45dd-b132-34c53b278570" />

## 5. Global & Detailed Routing

Routing lays down the physical metal wiring that connects placed cells together.

**Global Routing** — maps out approximate wire paths and flags congestion hotspots.

**Detailed Routing** — finalizes the actual metal connections in full compliance with technology design rules.

A clean route is a prerequisite for a valid, manufacturable layout.

<img width="1723" height="840" alt="Routing" src="https://github.com/user-attachments/assets/2f4dc4c1-bb8c-4098-9319-d58cfae02bd7" />

## 6. Antenna Effect: Detection

Long metal traces can accumulate charge during fabrication, and that charge can damage the gate oxide of nearby transistors — a failure mode known as the **antenna effect**.

Because of this risk, every route is checked for antenna violations once routing completes.

<img width="1701" height="931" alt="Antenna check" src="https://github.com/user-attachments/assets/bff7060f-045a-442e-8d7b-d3eaed75469e" />

## 7. Antenna Effect: Repair

Antenna diodes are the standard fix: they give accumulated charge a safe path to discharge, protecting the transistor gate.

```text
Antenna Violation
       ↓
Violation Detection
       ↓
Antenna Diode Insertion
       ↓
Routing / Optimization
       ↓
Antenna Re-check
```

## 8. Static Timing Analysis

STA confirms whether the implemented design still meets its timing budget. It's run at multiple points in the flow — notably right after synthesis and again after routing.

Key timing quantities include:

- Setup time
- Hold time
- Clock latency
- Data arrival time
- Data required time
- Slack

Positive slack is the signal that a given timing path is satisfied.

<img width="1598" height="904" alt="Static timing analysis" src="https://github.com/user-attachments/assets/e997a139-61e9-49e2-ac5f-2513f4c75a88" />

## 9. Post-Route Parasitic Extraction

Once routing is complete, real metal introduces parasitic resistance and capacitance that weren't visible earlier. These are extracted into a **Standard Parasitic Exchange Format (SPEF)** file.

That SPEF data feeds back into timing analysis, producing a far more realistic picture of post-route timing.

```text
Routed Design
     ↓
Parasitic Extraction
     ↓
SPEF
     ↓
OpenSTA
     ↓
Post-route Timing Analysis
```

## 10. Logic Equivalence Verification

Some physical design steps — optimization and CTS in particular — can modify the netlist. Logic Equivalence Check (LEC) confirms those modifications never altered the design's intended behavior.

```text
Reference Netlist
       |
       |  LEC
       |
Implemented Netlist
       ↓
Functional Equivalence
```

---

## 11. Sign-off: DRC & LVS

Physical verification is the final gate before a layout is considered manufacturable and correct.

**Design Rule Check (DRC)** confirms the layout obeys SKY130's manufacturing rules — minimum metal widths, spacing, via rules, layer-specific restrictions, and other geometric constraints.

**Layout Versus Schematic (LVS)** cross-checks the connectivity extracted from the layout against the intended netlist. A clean LVS run confirms the physical layout truly represents the intended circuit.

<img width="1766" height="857" alt="DRC and LVS" src="https://github.com/user-attachments/assets/aa87ef74-8d21-4d96-8702-cf8975b6d5a6" />

## 12. Exploring Design Alternatives in OpenLane

OpenLane makes it straightforward to sweep different implementation parameters and compare outcomes.

Settings like core utilization, aspect ratio, and other floorplan choices all shape the final result. Metrics worth tracking during exploration include:

- Area
- Cell count
- Utilization
- Timing
- Routing congestion
- Design-rule violations

Comparing runs side by side helps identify the configuration that best fits the design's goals.

<img width="1212" height="603" alt="Design exploration" src="https://github.com/user-attachments/assets/bfe05bcf-5c64-4b51-b930-442e476e1324" />

## 13. How the OpenLane Flow Works

OpenLane is an automated RTL-to-GDSII flow that stitches together a full stack of open-source EDA tools into one cohesive ASIC implementation pipeline — synthesis, floorplanning, placement, CTS, routing, extraction, timing, and physical verification, all in sequence.

Its purpose is to make ASIC implementation reproducible and approachable without sacrificing rigor.

<img width="1219" height="589" alt="OpenLane flow" src="https://github.com/user-attachments/assets/debfde85-d360-427a-a17c-ebb72a3355c4" />

## 14. OpenROAD's Role Inside OpenLane

OpenROAD is the engine doing most of the physical implementation heavy lifting inside OpenLane, covering:

- Floorplanning
- Power planning
- Placement
- Optimization
- Clock Tree Synthesis
- Routing
- General physical implementation

Other open-source utilities plug into OpenLane alongside it to round out the full flow.

## 15. Inside the SKY130 PDK

The **SkyWater SKY130 Process Design Kit** is the target technology for this project. It supplies the technology-specific data the flow depends on:

- Standard-cell libraries
- Technology LEF files
- Liberty timing libraries
- Design rules
- Layer stack information
- Physical abstracts

These files are what let the design be synthesized, placed, routed, and verified against a real, manufacturable process.

<img width="1778" height="968" alt="SKY130 PDK" src="https://github.com/user-attachments/assets/ed3ac07d-3d18-4ac6-8b5d-6a026e70b6c7" />

## 16. Configuring an OpenLane Run

OpenLane is driven by configuration variables that control each stage of the flow. The configuration file captures design-specific parameters so the run can be reproduced reliably.

Major configuration categories include:

- Design and source configuration
- Clock configuration
- Floorplan configuration
- Placement configuration
- Routing configuration
- Timing constraints
- Power planning parameters

The right configuration depends on both the design itself and the target process.

## 17. Floorplan Variables Reference

| Variable | What It Controls |
|---|---|
| `FP_CORE_UTIL` | Target core utilization |
| `FP_ASPECT_RATIO` | Core aspect ratio |
| `FP_SIZING` | Floorplan sizing mode |
| `FP_IO_MODE` | I/O placement mode |
| `FP_IO_HMETAL` | Horizontal I/O metal layer |
| `FP_IO_VMETAL` | Vertical I/O metal layer |
| `FP_PDN_*` | Power distribution network settings |

Getting these right shapes available area, placement density, congestion, and final routability.

## 18. Design for Test (DFT) Considerations

Design for Testability techniques make a fabricated chip easier to test and diagnose after manufacturing.

Scan-based testing is the common approach, giving controllability and observability over internal sequential elements — and it's factored in before final verification wraps up.

<img width="1721" height="808" alt="DFT" src="https://github.com/user-attachments/assets/ee490ae6-ca55-47be-9799-80f8d6359be5" />

## 19. Physical Implementation, Stage by Stage

This stage bundles together the core physical design operations that turn a synthesized netlist into a routed layout:

- Floorplanning
- Power planning
- Placement
- Clock Tree Synthesis
- Routing
- Physical optimization

OpenLane automates all of it, applying SKY130-specific constraints throughout.

---

# 20. Running the Flow

The OpenLane flow was executed by supplying the RTL source, technology files, and configuration parameters, then launching the flow script — which invokes the correct tools for each stage automatically.

The run produces intermediate files, logs, reports, and the final physical design outputs, organized like this:

```text
Design
 ├── src/
 ├── config.tcl
 └── runs/
      └── <run_directory>/
           ├── results/
           ├── reports/
           ├── logs/
           └── tmp/
```

# 21. Reading the Results

Once the flow completes, its reports are reviewed to judge implementation quality.

| Metric | What It Tells You |
|---|---|
| Area | Physical footprint of the implemented design |
| Cell Count | Number of standard cells used |
| Utilization | Percentage of the core occupied by cells |
| Timing | Whether timing constraints are met |
| Slack | Available timing margin |
| Routing | Whether connectivity was completed successfully |
| DRC | Compliance with manufacturing rules |
| LVS | Layout-to-netlist connectivity match |
| Antenna | Fabrication-related antenna violations |

# 22. Verification Checklist

| Check | What It Confirms |
|---|---|
| STA | Timing constraints are met |
| LEC | Logical equivalence is preserved |
| DRC | Physical design rules are followed |
| LVS | Layout connectivity matches the netlist |
| Antenna Check | No antenna rule violations remain |

Together, these checks confirm the implementation is logically correct, physically valid, and ready for further sign-off work.

# 23. Design Space Exploration

Different physical design configurations produce meaningfully different results. OpenLane's exploration tooling helps study how configuration choices affect:

- Area
- Utilization
- Timing
- Cell density
- Routing congestion
- Overall implementation quality

For instance, adjusting core utilization or aspect ratio shifts placement density and routing congestion in predictable ways — exploring that trade-off space helps land on a configuration that balances area, timing, and routability.

<img width="1750" height="808" alt="Design space exploration" src="https://github.com/user-attachments/assets/e6d74730-d544-42f8-a10d-2644b5389858" />

# 24. Regression Testing in OpenLane

Regression testing catches unexpected failures introduced by changes to the flow, tools, or configuration.

OpenLane's built-in regression mechanism makes it possible to evaluate a design against multiple flow configurations, which strengthens the reliability and reproducibility of the whole implementation process.

<img width="1684" height="865" alt="Regression testing" src="https://github.com/user-attachments/assets/9e52a336-587c-4242-8bfb-9a150235e13c" />

# 25. The Flow, End to End

```text
             RTL Design
                  |
                  ↓
              Synthesis
                  |
                  ↓
            Floorplanning
                  |
                  ↓
           Power Planning
                  |
                  ↓
              Placement
                  |
                  ↓
                 CTS
                  |
                  ↓
               Routing
                  |
                  ↓
        Parasitic Extraction
                  |
                  ↓
                 STA
                  |
                  ↓
          Antenna Checking
                  |
                  ↓
             DRC / LVS
                  |
                  ↓
          Final Layout / GDSII
```

---

# 26. Terminal Logs & Command Evidence

Screenshots below document the commands run during setup and flow execution.

## 26.1 PDK Directory and Library Setup

<img width="1920" height="944" alt="PDK directory setup" src="https://github.com/user-attachments/assets/a8a3b5ec-b2ac-43b3-811b-9c1cd731fd47" />

## 26.2 SKY130 Standard Cell Library Verification

<img width="1920" height="944" alt="Standard cell library check" src="https://github.com/user-attachments/assets/63055d56-984d-423b-b45f-b854a97b8d76" />

## 26.3 OpenLane Environment Setup

<img width="958" height="934" alt="OpenLane environment setup" src="https://github.com/user-attachments/assets/47d4cd2f-bf4d-400d-acea-3d675280dcab" />

## 26.4 LEF and Library File Verification

<img width="958" height="934" alt="LEF file verification" src="https://github.com/user-attachments/assets/cb04b5da-6a0b-4ca3-a46d-0b51dc3d2b6f" />

## 26.5 OpenLane Flow Execution

<img width="958" height="934" alt="Flow execution log" src="https://github.com/user-attachments/assets/a1c296bc-188e-4f0b-9f56-9437b015eaa1" />

## 26.6 OpenLane Configuration

<img width="958" height="934" alt="OpenLane README configuration" src="https://github.com/user-attachments/assets/821dbbb2-71aa-4183-9082-214bc288ec2f" />

## 26.7 Floorplan Configuration File

<img width="958" height="934" alt="Floorplan tcl configuration" src="https://github.com/user-attachments/assets/e85fb721-a204-4c96-b3d5-51f745cbf3b8" />

# 27. What I Learned

This project built practical understanding of:

- ASIC physical design methodology
- The OpenLane automated implementation flow
- SKY130 technology and its PDK files
- Floorplan configuration and core utilization
- Power distribution network planning
- Global and detailed placement
- Clock Tree Synthesis
- Global and detailed routing
- The antenna effect and diode-based repair
- Parasitic extraction via SPEF
- Static Timing Analysis with OpenSTA
- Logic Equivalence Checking
- Design Rule Checking
- Layout Versus Schematic verification
- Design-space exploration and optimization

More broadly, it clarified how each physical design stage feeds the next, and how implementation parameters ripple through to the final result.

# 28. Closing Thoughts

This project offered hands-on exposure to the complete ASIC Physical Design flow using OpenLane and the SKY130 PDK — from the first floorplanning decision through final sign-off.

Working through floorplanning, placement, clock distribution, routing, timing closure, and physical verification made clear how tightly these stages are coupled, and how much a single configuration choice can ripple through the rest of the flow.

It also reinforced how capable the open-source EDA ecosystem has become for real ASIC implementation work — and left me with a much more concrete, practical understanding of RTL-to-GDSII physical design.

## 👤 Author

**Princy Florence**
B.Tech – Electronics & Communication Engineering
Anurag University
[RTL Workshop Repository](https://github.com/madapaamrutha-svg/RTL_Workshop)
