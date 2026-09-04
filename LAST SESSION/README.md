<div align="center">

# 🧠 RTL Design Workshop
### A Five-Day Deep Dive into Verilog, Synthesis & Gate-Level Verification

**Author:** Princy

![Verilog](https://img.shields.io/badge/HDL-Verilog-blue)
![Yosys](https://img.shields.io/badge/Synthesis-Yosys-orange)
![SKY130](https://img.shields.io/badge/PDK-SKY130-red)
![Days](https://img.shields.io/badge/Days-5%2F5%20Completed-brightgreen)

*From a single 2:1 MUX to loop-based adders — a day-by-day record of learning*
*how RTL becomes real hardware.*

</div>

---

## 📖 What This Is

This repository is Princy's personal log from the **RTL Design Workshop** — five days spent moving through Verilog design, simulation, synthesis, timing libraries, logic optimization, and gate-level verification. Every day is captured with the concepts covered, hands-on labs, waveforms, screenshots, and the observations that came out of them.

---

## 🗺️ Workshop Progress

| Day | Focus | Status |
|---|---|---|
| **1** | Verilog basics, Icarus Verilog, GTKWave & Yosys synthesis | ✅ |
| **2** | Timing libraries, synthesis methods, flip-flop coding styles | ✅ |
| **3** | RTL/logic optimization, constant propagation | ✅ |
| **4** | RTL-to-gate-level simulation, MUX pitfalls, sim-synth mismatch | ✅ |
| **5** | IF-ELSE / CASE, latch inference, looping constructs | ✅ |

---

## 🔁 The Big Picture: RTL Design Flow

```text
RTL Design
   ↓
Verilog Coding
   ↓
RTL Simulation
   ↓
Waveform Analysis
   ↓
Synthesis & Optimization
   ↓
Technology Mapping
   ↓
Gate-Level Netlist
   ↓
Gate-Level Simulation
   ↓
Verification
```

Every day in this workshop is really just a closer look at one link in this chain.

---

## 📂 Repository Layout

```text
RTL_Design_Workshop
│
├── README.md
│
├── Day_1
│   └── README.md
│
├── Day_2
│   └── README.md
│
├── Day_3
│   ├── images
│   └── README.md
│
├── Day_4
│   ├── images
│   └── README.md
│
└── Day_5
    ├── images
    └── README.md
```

---

## 🟢 Day 1 — First Contact: RTL Design, Simulation & Synthesis

The starting point: getting a design simulated, a testbench working, and a first look at what synthesis actually does.

**Covered:**
- Simulator, design, and testbench roles
- Icarus Verilog simulation basics
- 2:1 multiplexer implementation
- GTKWave waveform reading
- Intro to Yosys and RTL synthesis
- Understanding `.lib` files
- Faster vs. slower cell flavors, and choosing cells for the job
- Yosys synthesis flow, statistics, and the resulting gate-level view
- Reading a generated netlist

**Toolkit:** Verilog · Icarus Verilog · GTKWave · Yosys · Linux/Ubuntu · Git & GitHub

📄 **[Full Day 1 writeup →](./Day_1/README.md)**

---

## 🟢 Day 2 — Timing Libraries, Synthesis & Flip-Flop RTL

Where the cells synthesis picks from actually come from, and how flip-flops get coded correctly.

**Covered:**
- SKY130 technology library
- `.lib` timing data and PVT (Process, Voltage, Temperature) conditions
- Hierarchical vs. flattened synthesis
- Async reset, async set, and sync reset D flip-flops
- Icarus Verilog + GTKWave workflow
- Yosys synthesis, `dfflibmap`, and technology mapping via `abc`
- Reading the resulting gate-level structure

**Toolkit:** Verilog · Icarus Verilog · GTKWave · Yosys · SKY130 Standard Cell Library · Linux/Ubuntu · Git & GitHub

📄 **[Full Day 2 writeup →](./Day_2/README.md)**

---

## 🟢 Day 3 — RTL & Logic Optimization

How synthesis simplifies a design without changing what it *does*.

**Covered:**
- RTL vs. logic optimization
- AND / OR / three-input AND logic
- Constant propagation
- DFF optimization across three constant-value scenarios
- Counter optimization
- Why optimization matters — and how optimized vs. unoptimized logic compares

**Optimization flow:**

```text
RTL Code → Logic Optimization → Constant Propagation → Redundant Logic Removal → Optimized Hardware
```

📄 **[Full Day 3 writeup →](./Day_3/README.md)**

---

## 🟢 Day 4 — RTL to Gate-Level Simulation

Where things get interesting: what happens when RTL simulation and gate-level simulation *disagree*.

**Covered:**
- The RTL-to-GLS flow, end to end
- Ternary-operator MUX and how a MUX actually works
- RTL simulation → synthesis → gate-level simulation
- Incomplete sensitivity lists and the classic "bad MUX" trap
- Writing it correctly with `always @(*)`
- The blocking-assignment caveat
- Blocking vs. non-blocking assignments
- Simulation-synthesis mismatch: what causes it, how to spot it

**Simulation flow:**

```text
RTL → RTL Simulation → Synthesis → Gate-Level Netlist → Gate-Level Simulation → Comparison
```

📄 **[Full Day 4 writeup →](./Day_4/README.md)**

---

## 🟢 Day 5 — RTL Coding Styles & Looping Constructs

Closing out with control-flow constructs and how they translate into hardware.

**Covered:**
- IF-ELSE and CASE statement coding styles
- Inferred latches — and how to avoid them by accident
- Labs 1–2: incomplete IF statements
- Labs 3–5: CASE statements
- Lab 6: overlapping CASE conditions
- Redundancy optimization during synthesis
- Looping constructs (Labs 7–10): loop-based MUX, loop-based DEMUX
- Ripple Carry Adder (RCA) design

**Hardware design flow:**

```text
RTL Coding → Simulation → Synthesis → Hardware Inference → Netlist & Waveform Analysis
```

📄 **[Full Day 5 writeup →](./Day_5/README.md)**

---

## 🧰 Tools & Technologies

| Category | Tool |
|---|---|
| HDL | Verilog |
| Simulation | Icarus Verilog |
| Waveform Viewer | GTKWave |
| Synthesis | Yosys |
| Standard Cells | SKY130 |
| Environment | Linux / Ubuntu |
| Version Control | Git & GitHub |

---

## 🎯 What This Workshop Built Up

By the end of five days, this covered practical ground in:

- Writing and simulating Verilog RTL, with testbenches and waveform analysis
- Running synthesis in Yosys and reading `.lib` timing data
- Working with SKY130 standard cells, hierarchical vs. flattened synthesis
- Coding D flip-flops across different reset/set styles
- Applying RTL/logic optimization — constant propagation, redundancy removal
- Running gate-level simulation and catching simulation-synthesis mismatches
- Using blocking vs. non-blocking assignments correctly
- Writing complete sensitivity lists with `always @(*)`
- Spotting and avoiding unintended latch inference
- Using IF-ELSE / CASE statements correctly, including overlapping conditions
- Building hardware with looping constructs — MUX, DEMUX, and a Ripple Carry Adder
- Comparing RTL behavior against synthesized hardware, every step of the way

---

## 📑 Full Documentation Index

| Day | Link |
|---|---|
| 1 | [RTL Design, Simulation & Synthesis](./Day_1/README.md) |
| 2 | [Timing Libraries, Synthesis & Flip-Flop RTL](./Day_2/README.md) |
| 3 | [RTL & Logic Optimization](./Day_3/README.md) |
| 4 | [RTL to Gate-Level Simulation](./Day_4/README.md) |
| 5 | [RTL Coding Styles & Looping Constructs](./Day_5/README.md) |

---

<div align="center">

### ✍️ Author

**Princy**

*Documented one day, one lab, one waveform at a time.*

</div>
