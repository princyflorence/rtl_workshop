# Module 3 – Sequential Logic, FSMs & Synthesis Fundamentals

In Module 3, I moved beyond combinational design and explored how circuits behave **over time** — using clocks, memory elements, state machines, and finally how RTL code gets translated into real hardware through synthesis.

---

### Topics Covered

- Synthesis flow: RTL to Gate-level translation
- Timing basics: setup time, hold time, and clock period
- Finite State Machine (FSM) design: Moore vs Mealy
- Flip-Flops and Latches: D, JK, T, SR types
- Sequential vs Combinational circuits: key differences
- FSM coding styles in Verilog (1-always, 2-always, 3-always)
- Clock and Reset strategies: synchronous vs asynchronous reset
- Shift registers and counters as sequential building blocks

---

## 🔄 Why Sequential Logic?

Combinational circuits (covered in earlier days) have no memory — their output depends only on the current input. Real digital systems, however, need to **remember state**: a counter needs to know its last value, a traffic light controller needs to know which phase it's in. This is where sequential logic comes in, built around **clock-driven memory elements**.

---

## 🧱 Flip-Flops and Latches

The fundamental storage element in RTL design.

| Element | Behavior |
|---|---|
| **Latch** | Level-sensitive; transparent while enable is active |
| **D Flip-Flop** | Edge-triggered; stores input value on clock edge |
| **JK Flip-Flop** | Toggles, sets, or resets based on J/K inputs |
| **T Flip-Flop** | Toggles output on every clock edge when enabled |

Most RTL synthesis targets use **D flip-flops** as the default storage primitive since they map cleanly to standard cell libraries.

---

## ⏱ Synchronous vs Asynchronous Reset

- **Synchronous reset**: reset takes effect only on a clock edge — cleaner for timing analysis, generally preferred in ASIC flows.
- **Asynchronous reset**: reset takes effect immediately, independent of the clock — useful for power-up initialization but can introduce timing closure challenges.

---

## 🔁 Shift Registers & Counters

Built directly from chained flip-flops:

- **Shift Register**: data moves one bit per clock cycle — used in serial-to-parallel conversion.
- **Counter**: increments/decrements a stored value each cycle — a basic building block for timers, address generators, and control logic.

---
<img width="2119" height="1077" alt="image" src="https://github.com/user-attachments/assets/7de76b6f-a773-48b3-9645-6a04ac8b8d6d" />


## 🚦 Finite State Machines (FSMs)

An FSM is a design pattern for sequential circuits with distinct **states**, **transitions**, and **outputs**.

| Type | Output Depends On |
|---|---|
| **Moore FSM** | Current state only |
| **Mealy FSM** | Current state + current input |

**Common Verilog coding styles:**

1. **1-always block** – combines state register, next-state logic, and output logic in one block
2. **2-always block** – separates the sequential state register from the combinational next-state/output logic
3. **3-always block** – further separates next-state logic and output logic into distinct blocks for clarity and easier debugging

The 2- and 3-always styles are generally preferred in industry RTL for better synthesis predictability and readability.
<img width="2124" height="1175" alt="image" src="https://github.com/user-attachments/assets/dde5d21e-7e07-4877-ac1f-f02b66222fce" />

---

## 🏗 Synthesis: From RTL to Gates

Synthesis is the process of converting Verilog RTL code into a **gate-level netlist** made of standard cells (AND, OR, flip-flops, etc.) from a target technology library.

**Basic flow:**
1. RTL code (behavioral Verilog)
2. Synthesis tool maps logic to a technology library
3. Gate-level netlist generated
4. Timing and area reports produced
<img width="2090" height="1190" alt="image" src="https://github.com/user-attachments/assets/52e1fc75-64db-466c-962b-7bb4aad3efbf" />

---

## ⏳ Timing Fundamentals

Two concepts are critical once sequential elements are involved:

- **Setup time**: minimum time data must be stable *before* the clock edge
- **Hold time**: minimum time data must remain stable *after* the clock edge

Violating either causes **metastability** — an unreliable, unpredictable output state that can propagate errors through a design.

---
<img width="2125" height="1196" alt="image" src="https://github.com/user-attachments/assets/3d5aae89-6fe9-4683-bca2-d6382a9fbbf6" />

---

## 🛠 Tools Used

- Verilog
- Icarus Verilog (iverilog)
- GTKWave
- Yosys (for synthesis exploration)
