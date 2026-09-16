# Module 3 – RTL to Physical Design / CMOS Technology
# From Transistor to Silicon: CMOS Inverter Design, SKY130A Layout, and the 16-Mask Fabrication Process
---

## 📌 About This Module

This module walks through a complete, hands-on CMOS design journey — from transistor-level SPICE simulation of a CMOS inverter, through circuit characterization, SKY130A standard-cell layout, layout extraction and extracted-SPICE simulation, all the way to the physical fabrication process itself.

It opens with the design and analysis of a CMOS inverter in SPICE, characterizing its behavior through transient response, input/output waveforms, rise time, fall time, propagation delay, voltage transfer characteristics, switching threshold, and the impact of PMOS/NMOS sizing.

From there, the design moves into the SKY130A standard-cell physical design flow: setting up the design repository and technology files, examining the CMOS inverter layout, defining the standard-cell boundary, and wiring up power and ground connections.

The layout is then extracted to obtain the circuit's electrical representation, which becomes a SPICE-compatible netlist and gets simulated in NGSPICE. The resulting waveforms confirm that the extracted, physically implemented inverter still behaves correctly.

The module closes by tracing the major stages of the **16-mask CMOS fabrication process** — starting from silicon substrate preparation and moving through active-region formation, well formation, gate formation, LDD implantation, source/drain formation, contact formation, and metal interconnection.

Together, these pieces connect circuit-level design, simulation, physical implementation, layout extraction, post-layout verification, and the underlying fabrication technology into a single coherent picture.

---
---

## 🎯 What This Module Covers

- The transistor-level operation of a CMOS inverter.
- CMOS inverter simulation in SPICE.
- Transient input and output waveform analysis.
- Rise time, fall time, and propagation delay.
- The Voltage Transfer Characteristic (VTC).
- Switching threshold voltage and body effect.
- The impact of PMOS/NMOS transistor sizing.
- Implementing and analyzing a CMOS inverter standard cell in SKY130A.
- Standard-cell layout, cell boundary, and power/ground connectivity.
- Layout extraction and generating an extracted SPICE representation.
- Simulating the extracted circuit in NGSPICE.
- Verifying the functional behavior of the physically implemented inverter.
- The major stages of the 16-mask CMOS fabrication process.
- How circuit design, physical layout, extraction, and simulation all connect.

## 🛠️ Tools and Technologies Used

| Tool / Technology | Purpose |
|---|---|
| **NGSPICE** | Transient and extracted-layout SPICE simulation |
| **Magic VLSI** | CMOS layout creation, visualization and extraction |
| **OpenLane** | Physical design and standard-cell implementation environment |
| **SKY130A PDK** | CMOS technology files, device models and layout rules |
| **SPICE** | Circuit modelling and transistor-level characterization |
| **Linux Terminal** | Executing design, extraction and simulation commands |
| **Git** | Repository cloning and version control |
| **GitHub** | Project repository and documentation |
| **CMOS Technology** | Understanding transistor fabrication and process flow |

# CMOS Inverter Design, Characterization and 16-Mask CMOS Fabrication

## 📑 Contents

1. Simulating and Characterizing the CMOS Inverter in SPICE
2. The SKY130A Standard-Cell Design Flow
3. The 16-Mask CMOS Fabrication Process
4. Forming the Lightly Doped Drain
5. Forming Source and Drain
6. Building Contacts and Interconnects
7. Adding Higher-Level Metal
8. The Full Design-to-Fabrication Flow
9. Layout and Abstract View
10. Defining the Cell Boundary
11. Power and Ground Connectivity
12. Extracting the Layout
13. Generating the Extracted Netlist
14. Building the SPICE File
15. Running Transient Simulation in NGSPICE
16. Reading Input and Output Waveforms
17. Physical Verification and Layout Analysis
18. Standard Cell Layout Structure
19. Extraction and Parasitic Data
20. SPICE Model and Device Parameters
21. Setting Up the Simulation
22. How the CMOS Inverter Operates
23. Rise and Fall Behavior
24. Timing Behavior
25. Voltage Levels
26. Transistor Sizing and Performance
27. Correlating Layout with Simulation
28. The Complete Design Flow
29. Closing Notes

# 1. Simulating and Characterizing the CMOS Inverter in SPICE

## 1.1 Setting Up the CMOS Inverter and SPICE Model

The module opens by setting up a CMOS inverter for transistor-level simulation. A CMOS inverter pairs a **PMOS transistor tied to the supply voltage** with an **NMOS transistor tied to ground**.

The SPICE model supplies the electrical characteristics of the MOS devices, letting the inverter be analyzed under realistic device parameters. Transistor dimensions, supply voltage, and input waveform are all chosen before simulation runs.

Getting this setup right matters — the accuracy of the output waveform and delay measurements depends entirely on correct device models and circuit parameters. NMOS and PMOS dimensions are chosen to match the intended inverter design.

---

## 1.2 Configuring the Simulation Environment and Device Parameters

Next comes configuring the simulation environment and setting the transistor parameters. The width-to-length ratio of the PMOS and NMOS devices directly shapes the inverter's switching behavior.

The chosen dimensions set the relative drive strength of the pull-up and pull-down networks — proper sizing is what produces balanced rise and fall characteristics.

<img width="1920" height="1080" alt="Device parameters and simulation configuration" src="https://github.com/user-attachments/assets/49059bca-d76c-4928-a2fa-280ebf9307f5" />

**Figure 2: Device parameters and simulation configuration**

These parameters are what SPICE uses to compute the transient response and voltage-transfer characteristics of the inverter.

---

## 1.3 Running the SPICE Simulation

With the circuit and device parameters defined, the SPICE simulation runs — solving the inverter's electrical behavior as the input voltage changes over time.

The input signal drives the shared gate terminal of the PMOS and NMOS transistors. Depending on the input voltage, one transistor turns ON while the other turns OFF, producing the inverted output.

<img width="1920" height="1080" alt="Execution of CMOS inverter SPICE simulation" src="https://github.com/user-attachments/assets/939cf014-7cdb-481e-9814-b36432bc3de8" />

**Figure 3: Execution of CMOS inverter SPICE simulation**

This step confirms the inverter functions correctly before moving into detailed timing and static characterization.

---

## 1.4 Reading the Input and Output Waveforms

The transient simulation produces both input and output waveforms. The output is complementary to the input, demonstrating the CMOS inverter's fundamental behavior.

When the input is LOW, the PMOS conducts and the output is pulled toward the supply voltage. When the input goes HIGH, the NMOS conducts instead and the output is pulled toward ground.

<img width="1920" height="1080" alt="CMOS inverter input and output waveforms" src="https://github.com/user-attachments/assets/f5eef6f2-4ef7-42f5-8e06-9d0101f20d30" />

**Figure 4: CMOS inverter input and output waveforms**

This waveform confirms correct logical inversion and supplies the data needed to measure propagation delay and transition times.

---

## 1.5 Analyzing the Transient Response

The transient response shows how quickly the inverter reacts to a changing input signal.

The output doesn't switch instantaneously — the transistor network and load capacitance both need finite time to charge or discharge. That delay is a key digital-circuit performance parameter.

<img width="1920" height="1080" alt="Transient response of the CMOS inverter" src="https://github.com/user-attachments/assets/093dfd64-273a-4b05-9e0b-a35b6f7cbbed" />

**Figure 5: Transient response of the CMOS inverter**

This waveform is used to pull out rise time, fall time, and propagation delay.

---

## 1.6 Zooming Into the Waveform

Examining the simulation waveform over a narrower time window makes the transition points of the input and output signals easier to pinpoint.

The time gap between the corresponding input and output transitions is the inverter's propagation delay.

<img width="1920" height="1080" alt="Detailed observation of the transient waveform" src="https://github.com/user-attachments/assets/163d8c7a-308b-4075-93cb-1089ac653b46" />

**Figure 6: Detailed observation of the transient waveform**

Precise waveform reading is essential for reliable timing measurements and fair comparisons across different sizing conditions.

---

## 1.7 How PMOS/NMOS Sizing Changes Behavior

CMOS inverter performance hinges heavily on the relative sizing of its PMOS and NMOS transistors, so several width ratios are simulated side by side.

A wider transistor delivers more drive current and can shorten the time needed to charge or discharge the load — but oversizing also adds capacitance, which can work against overall performance.

<img width="1920" height="1080" alt="Effect of transistor sizing on inverter behavior" src="https://github.com/user-attachments/assets/c5589897-81eb-474e-8f26-f2641be5417f" />

**Figure 7: Effect of transistor sizing on inverter behavior**

Comparing these results helps pin down a PMOS-to-NMOS ratio that gives balanced inverter operation.

---

## 1.8 Evaluating Static Behavior

Static characterization relies on the **Voltage Transfer Characteristic (VTC)** of the CMOS inverter.

The VTC plots output voltage against input voltage, and reveals three key operating regions: logic HIGH, the transition region, and logic LOW.

<img width="1920" height="1080" alt="Static voltage-transfer characteristic" src="https://github.com/user-attachments/assets/2fc3f090-13f4-42ff-9857-66aa0516e2b9" />

**Figure 8: Static voltage-transfer characteristic**

A steep transition region signals sharp switching behavior — and a well-designed CMOS inverter delivers clear logic levels with healthy noise margins.

---

## 1.9 Checking Inverter Robustness

VTC curves for different sizing ratios are compared to gauge how robust the inverter's switching behavior is.

The switching point shifts as the relative strength of the PMOS and NMOS devices changes, and comparing these curves reveals how sizing affects the logic threshold and the inverter's symmetry.

<img width="1920" height="1080" alt="CMOS inverter robustness evaluation" src="https://github.com/user-attachments/assets/821c8469-2f7e-4a3f-b127-48a50eee15b1" />

<p align="center">
<img src="images/09.jpg" width="800">
</p>

**Figure 9: CMOS inverter robustness evaluation**

This comparison helps pick a device ratio that gives stable switching and acceptable noise margins.

---

## 1.10 Measuring Switching Threshold Voltage

The switching threshold voltage is the input voltage at which the inverter flips from a HIGH output to a LOW output.

This threshold depends on transistor characteristics, device sizing, body voltage, and fabrication parameters — the body effect in particular matters whenever the source-to-body voltage isn't zero.

<img width="1920" height="1080" alt="Switching threshold analysis" src="https://github.com/user-attachments/assets/c9664ecf-4838-4fbc-8fc8-c6b5db446aee" />

**Figure 10: Switching threshold analysis**

Calculated and simulated values are compared here to see how well theory lines up with actual device behavior.

---

## 1.11 Reading the Voltage Transfer Characteristic

The voltage-transfer curve gives a complete static picture of the CMOS inverter.

At low input voltage, PMOS is ON and NMOS is OFF, so the output sits close to the supply voltage. At high input voltage, the roles flip — NMOS is ON, PMOS is OFF, and the output approaches ground.

<img width="1920" height="1080" alt="CMOS inverter VTC" src="https://github.com/user-attachments/assets/1338976a-8783-4ca5-9e82-476e90324e4d" />

**Figure 11: CMOS inverter VTC**

The sharp transition in this curve reflects the inverter's high voltage gain around the switching point.

---

## 1.12 Comparing Sizing Conditions

A second sizing configuration is tested to see how changing transistor dimensions shifts the inverter's switching characteristics.

Adjusting the PMOS/NMOS ratio rebalances the pull-up and pull-down networks, which can move the switching threshold and change rise/fall delays.

<img width="1920" height="1080" alt="Comparison of inverter sizing conditions" src="https://github.com/user-attachments/assets/2299ffa1-5019-49fb-b46b-df5d86c3f42d" />

**Figure 12: Comparison of inverter sizing conditions**

This comparison shows why transistor sizing deserves real attention during standard-cell design.

---

## 1.13 Cross-Checking Against Calculated Values

Simulation results are checked against calculated values as an extra sanity check on the waveform-derived measurements.

This numerical cross-check helps confirm that the extracted parameters line up with theoretical expectations.

<img width="1920" height="1080" alt="Verification of calculated simulation parameter" src="https://github.com/user-attachments/assets/53f7e58a-ceef-4f41-b9fb-6e8cbdc32188" />

**Figure 13: Verification of calculated simulation parameter**

This kind of verification builds confidence in the extracted timing and electrical characteristics.

---

## 1.14 Wrapping Up the Inverter Characterization

The final characterization step brings together the behavior of the chosen CMOS inverter configuration.

Voltage-transfer characteristic, switching behavior, and timing response are considered together to judge whether the selected transistor sizing hits the desired performance target.

<img width="1920" height="1080" alt="Final CMOS inverter characterization" src="https://github.com/user-attachments/assets/7c2cf640-3a16-4c18-a33a-a0c48b71354d" />

**Figure 14: Final CMOS inverter characterization**

These results form the foundation for moving into physical implementation of the standard cell.

---

# 2. The SKY130A Standard-Cell Design Flow

## 2.1 Cloning the Design Repository

Setting up the standard-cell design environment starts with cloning the required repository into the OpenLane working directory.

Git pulls in the source files, configuration files, technology information, and other supporting resources needed for the flow.

<img width="958" height="934" alt="Cloning the standard-cell design repository" src="https://github.com/user-attachments/assets/a9b140d8-4ff0-4579-b243-ceae1a28b9f7" />

**Figure 15: Cloning the standard-cell design repository**

Cloning the repository ensures the design environment has everything needed for the layout and physical-design steps that follow.

---

## 2.2 Bringing In the SKY130A Technology File

Once the repository is cloned, the required SKY130A technology file is copied into the appropriate standard-cell design directory.

This technology file carries the information the layout and physical-design tools need to correctly interpret process layers and device structures.

<img width="958" height="934" alt="Copying the SKY130A technology file" src="https://github.com/user-attachments/assets/c4e9d76b-11d9-481b-a26e-665468b324a9" />

**Figure 16: Copying the SKY130A technology file**

Getting the technology file in the right place is essential before the standard-cell layout can even be opened in SKY130A.

---

## 2.3 Viewing the CMOS Inverter Layout

The CMOS inverter layout opens in the layout editor, revealing the physical arrangement of the PMOS and NMOS devices, contacts, diffusion regions, polysilicon, and metal layers.

This layout is the physical realization of the transistor-level CMOS inverter designed earlier during simulation.

<img width="958" height="934" alt="SKY130A CMOS inverter layout" src="https://github.com/user-attachments/assets/5742fab9-ac85-4562-aa2d-be6d7464c84b" />

**Figure 17: SKY130A CMOS inverter layout**

A solid layout has to satisfy the technology's design rules while staying compact and maintaining correct electrical connectivity.

---

# 3. The 16-Mask CMOS Fabrication Process

The module's closing section walks through the major stages of manufacturing a CMOS integrated circuit using a 16-mask process.

The fabrication sequence repeats a cycle of oxidation, photolithography, implantation, deposition, etching, and metallization — with each mask defining a specific region needed to build the CMOS devices.

---

## 3.1 Choosing the Silicon Substrate

Fabrication begins with selecting a P-type silicon substrate.

The substrate gives CMOS devices their mechanical and electrical foundation, and it's chosen with a controlled doping concentration, resistivity, and crystal orientation.

<img width="1920" height="1080" alt="Selection of P-type silicon substrate" src="https://github.com/user-attachments/assets/98d1d7a1-5e96-431d-a2b9-e2f73c52b3a8" />

**Figure 18: Selection of P-type silicon substrate**

Starting with a tightly controlled substrate matters because its properties directly shape device characteristics like threshold voltage, junction behavior, and leakage.

---

## 3.2 Forming Active Regions – Mask 1

The first mask defines the active regions where transistors will eventually form.

Field oxide is grown over the areas that need electrical isolation, using the **LOCOS process** — Local Oxidation of Silicon — to separate active device areas from one another.

<img width="1920" height="1080" alt="Active region formation using Mask 1" src="https://github.com/user-attachments/assets/1aff10a7-95a3-42ad-b3d5-5c0309004cc7" />

**Figure 19: Active region formation using Mask 1**

The field oxide blocks unwanted conduction between neighboring devices, providing electrical isolation across the chip.

LOCOS also leaves behind its characteristic **bird's-beak** shape at the boundary of the field oxide.

---

## 3.3 Forming the P-Well – Boron Implantation

Next comes forming the well regions needed for complementary MOS devices.

Boron, a P-type dopant, is implanted into the target region to form the P-well, with implantation energy and dose tightly controlled to hit the required doping profile.

<img width="1920" height="1080" alt="P-well formation using boron implantation" src="https://github.com/user-attachments/assets/08190702-815f-4c72-b610-97fb8e0166ca" />

**Figure 20: P-well formation using boron implantation**

The P-well becomes the body region for the NMOS transistor — getting this step right is essential for hitting the target threshold voltage and isolation.

---

## 3.4 Forming the N-Well – Phosphorus Implantation

Phosphorus, an N-type dopant, is implanted into the target region to form the N-well.

The N-well provides the body region the PMOS transistor needs in a CMOS process.

<img width="1920" height="1080" alt="N-well formation using phosphorus implantation" src="https://github.com/user-attachments/assets/3eea2bb2-1a04-4bab-bbce-cebec3cfb9a1" />

**Figure 21: N-well formation using phosphorus implantation**

Together, the N-well and P-well regions let both PMOS and NMOS transistors coexist on the same silicon substrate.

---

## 3.5 Forming the Gate

With the wells in place, a thin gate oxide layer is grown across the active regions — this oxide is what electrically isolates the gate from the transistor channel beneath it, and its thickness is tightly controlled since it directly affects threshold voltage and device performance.

A layer of polysilicon is then deposited over the entire wafer and patterned using a dedicated gate mask, defining exactly where each transistor's gate will sit.

Etching removes the unwanted polysilicon, leaving behind the gate electrodes for both the NMOS and PMOS transistors — completing the structure that will control current flow through each device's channel.

---

## 👤 Author

**Princy Florence**
B.T
