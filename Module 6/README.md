<div align="center">

# ⚡ VSDBabySoC
### RTL → Gate-Level Design, Synthesis & Verification Journal

**Author:** Princy

![Verilog](https://img.shields.io/badge/HDL-Verilog-blue)
![Yosys](https://img.shields.io/badge/Synthesis-Yosys-orange)
![SKY130](https://img.shields.io/badge/PDK-SKY130-red)
![Status](https://img.shields.io/badge/Status-Documented-brightgreen)

*A hands-on record of taking an RTL design all the way to a gate-level netlist —*
*and proving, waveform by waveform, that nothing broke along the way.*

</div>

---

## 📌 About This Document

This repository is Princy's working log of the **RTL-to-Gate-Level flow** built around **VSDBabySoC** — a compact SoC combining a PLL, a RISC-V core (`rvmyth`), and a DAC.

Rather than just handing over a final netlist, this document walks through *why* each stage exists: RTL simulation, synthesis, logic optimization, technology mapping, netlist generation, and Gate-Level Simulation (GLS) — including a short **Good MUX** case study on how coding style shapes synthesized hardware.

> 💡 **Guiding question throughout:** does the design still *behave* the same after every transformation, even when its *structure* changes completely?

---

## 🗂️ Table of Contents

| # | Section |
|---|---------|
| 1 | [Repository Layout](#-1-repository-layout) |
| 2 | [Toolchain](#-2-toolchain) |
| 3 | [The Flow at a Glance](#-3-the-flow-at-a-glance) |
| 4 | [Case Study: Good MUX](#-4-case-study-good-mux) |
| 5 | [VSDBabySoC Architecture](#-5-vsdbabysoc-architecture) |
| 6 | [Design Under Test](#-6-design-under-test) |
| 7 | [Synthesis Walkthrough](#-7-synthesis-walkthrough) |
| 8 | [Netlist Generation](#-8-netlist-generation) |
| 9 | [Gate-Level Simulation](#-9-gate-level-simulation) |
| 10 | [RTL vs Gate-Level, Side by Side](#-10-rtl-vs-gate-level-side-by-side) |
| 11 | [Key Takeaways](#-11-key-takeaways) |
| 12 | [One-Shot Synthesis Script](#-12-one-shot-synthesis-script) |

---

## 📁 1. Repository Layout

```text
VSDBabySoC/
│
├── README.md
│
├── src/
│   ├── module/
│   │   ├── vsdbabysoc.v
│   │   ├── rvmyth.v
│   │   └── clk_gate.v
│   │
│   └── testbench/
│       └── testbench.v
│
├── netlist/
│   └── babysoc_netlist.v
│
├── images/
│   ├── babysoc_rtl.png
│   ├── rvmyth_hierarchy.png
│   ├── synthesis_statistics.png
│   ├── abc_technology_mapping.png
│   ├── post_optimization_statistics.png
│   ├── pre_synth_gls.png
│   └── post_synth_gls.png
│
└── mux/
    ├── good_mux.v
    ├── bad_mux.v
    ├── good_mux_rtl_simulation.png
    ├── good_mux_graphical_rep.png
    ├── good_mux_netlist.png
    └── good_mux_rtl_vs_gls.png
```

---

## 🛠️ 2. Toolchain

| Tool | Role in the Flow |
|---|---|
| **Icarus Verilog** | Runs RTL and gate-level simulations |
| **GTKWave** | Visualizes waveforms |
| **Yosys** | Performs RTL synthesis and netlist generation |
| **ABC** | Logic optimization and technology mapping |
| **SKY130 PDK Libraries** | Supplies technology-specific standard cells |
| **Git / GitHub** | Version control and documentation hosting |

---

## 🔄 3. The Flow at a Glance

```text
 RTL Design → RTL Simulation → Functional Verification → Synthesis
      → Logic Optimization → Technology Mapping → Gate-Level Netlist
      → Gate-Level Simulation → Pre/Post-Synthesis Comparison
```

The whole point of this pipeline: confirm that whatever the RTL *promised*, the synthesized hardware *delivers* — identical behavior, regardless of how different the internal structure ends up looking.

---

## 🔀 4. Case Study: Good MUX

Before tackling VSDBabySoC itself, Princy started small: a 2:1 multiplexer, used to study how RTL phrasing changes what synthesis actually builds.

```text
                 ┌─────────────┐
        A ──────►│             │
        B ──────►│    2:1 MUX  ├────► Y
      SEL ──────►│             │
                 └─────────────┘
```

**Expected truth table**

| SEL | Y |
|---|---|
| 0 | A |
| 1 | B |

**Lesson learned:**

```text
RTL Coding Style → Synthesis Interpretation → Inferred Hardware
```

A combinational block must fully specify its output for *every* input combination — anything left ambiguous gets resolved by the synthesizer, not always the way you'd expect.

**Evidence gathered:**
- 📊 RTL simulation waveform — `mux/good_mux_rtl_simulation.png`
- 🧩 Graphical structural view — `mux/good_mux_graphical_rep.png`
- 🔧 Synthesized netlist — `mux/good_mux_netlist.png`
- ✅ RTL vs GLS waveform match — `mux/good_mux_rtl_vs_gls.png`

The GLS waveform confirmed the synthesized MUX preserved the intended behavior exactly.

---

## 🧩 5. VSDBabySoC Architecture

```text
             ┌──────────────┐
             │    avsdpll   │   Clock generation
             │     PLL      │
             └──────┬───────┘
                    │  Clock
                    ▼
             ┌──────────────┐
             │    rvmyth    │   Digital processing
             │  RISC-V Core │
             └──────┬───────┘
                    │  Digital Output
                    ▼
             ┌──────────────┐
             │    avsddac   │   Analog interface
             │     DAC      │
             └──────┬───────┘
                    │
                    ▼
                   OUT
```

Three blocks, three jobs: the **PLL** clocks the system, **rvmyth** does the compute, and the **DAC** hands the result off as an analog-ready output.

---

## 🎯 6. Design Under Test

| Item | File |
|---|---|
| Top module | `vsdbabysoc` |
| Main RTL | [`src/module/vsdbabysoc.v`](src/module/vsdbabysoc.v) |
| RISC-V core | [`src/module/rvmyth.v`](src/module/rvmyth.v) |
| Clock gating | [`src/module/clk_gate.v`](src/module/clk_gate.v) |
| Testbench | [`src/testbench/testbench.v`](src/testbench/testbench.v) |

The testbench drives stimulus into VSDBabySoC and captures its output for comparison at every later stage.

---

## ⚙️ 7. Synthesis Walkthrough

### 7.1 Load the RTL

```bash
yosys
read_verilog src/module/vsdbabysoc.v
read_verilog -I src/include src/module/rvmyth.v
read_verilog -I src/include src/module/clk_gate.v
```
![RVMYTH hierarchy](images/rvmyth_hierarchy.png)

### 7.2 Load Technology Libraries

```bash
read_liberty -lib src/lib/avsdpll.lib
read_liberty -lib src/lib/avsddac.lib
read_liberty -lib src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

### 7.3 Run Synthesis

```bash
synth -top vsdbabysoc
```
![VSDBabySoC RTL](images/vsdbabysoc_rtl.png)

**Post-synthesis statistics** (wires, wire bits, cells, sequential vs. combinational elements):

![Synthesis Statistics](images/synthesis_statistics.png)

### 7.4 Map Sequential Cells

```bash
dfflibmap -liberty lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

### 7.5 Optimize

```bash
opt
```

Optimization pass covers constant propagation, dead-logic removal, Boolean simplification, and connection cleanup.

![Post Optimization Statistics](images/post_optimization_statistics.png)

### 7.6 Technology-Map with ABC

```bash
abc -liberty lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

```text
Generic Logic → ABC → SKY130 Standard Cells
```

![ABC Technology Mapping](images/abc_technology_mapping.png)

### 7.7 Visualize the Result

```bash
show vsdbabysoc
```

<img width="808" height="166" alt="Synthesized structural view" src="https://github.com/user-attachments/assets/4db39dbd-c05c-4894-81a8-887bd1f7d9fb" />

---

## 🧱 8. Netlist Generation

Before export, the design is flattened and swept clean:

```bash
flatten          # remove hierarchical boundaries
setundef -zero   # resolve undefined values to 0
clean -purge     # drop unused objects
rename -enumerate  # normalize internal naming
```

Final write-out:

```bash
write_verilog -noattr netlist/babysoc_netlist_new.v
```

📄 Result: [`netlist/baby_soc_netlist_new.v`](netlist/baby_soc_netlist_new.v) — the synthesized implementation, not the original RTL.

---

## 🔍 9. Gate-Level Simulation

```text
RTL → RTL Simulation → Reference Behaviour → Synthesis
   → Gate-Level Netlist → GLS Simulation → Functional Comparison
```

### 9.1 Pre-Synthesis GLS (Reference Run)

```bash
iverilog -o ./pre_synth_sim.out -DPRE_SYNTH_SIM \
  src/module/testbench.v -I src/include -I src/module/
```

| Flag / Arg | Purpose |
|---|---|
| `-o ./pre_synth_sim.out` | Names the simulation binary |
| `-DPRE_SYNTH_SIM` | Selects the pre-synthesis testbench path |
| `-I src/include`, `-I src/module/` | Include search paths |

![Pre-Synthesis Waveform](images/pre_synth_gls.png)

### 9.2 Post-Synthesis GLS

```bash
sudo iverilog -DPOST_SYNTH_SIM -DFUNCTIONAL \
  -I src/include/ \
  -I ../../sky130RTLDesignAndSynthesisWorkshop/my_lib/verilog_model/ \
  -I src/module/ \
  src/module/testbench.v
```

This run swaps in the **gate-level netlist** plus the SKY130 functional Verilog models instead of the original RTL logic.

![Post-Synthesis GLS](images/post_synth_gls.png)

### 9.3 Pre vs. Post Comparison

![Comparison](images/pre_post_gls_comparison.png)

```text
Expected RTL Behaviour → Synthesized Implementation → Observed GLS Behaviour
```

Internal signals may shift — that's expected. What matters is that the **observable** signals (inputs, reset, clock behavior, outputs) line up.

---

## ⚖️ 10. RTL vs Gate-Level, Side by Side

| Aspect | RTL | Gate-Level |
|---|---|---|
| Abstraction | Higher | Lower |
| Orientation | Behavior | Structure |
| Readability | Easier | More detailed |
| Technology | Independent | Mapped |
| Describes | Intended functionality | Cell connectivity |
| Verification role | Functional check | Post-synthesis check |

**Example — same intent, different shape:**

```verilog
if (sel)
    y = b;
else
    y = a;
```

may become a dedicated MUX cell, or a combination of simpler standard cells — the synthesizer decides. So:

```text
RTL Structure ≠ Gate-Level Structure
RTL Functionality = Gate-Level Functionality   ← this one has to hold
```

**Why internal waveforms can legitimately differ:**
- Logic optimization & constant propagation
- Logic merging / redundant-logic removal
- Technology mapping & hierarchy flattening
- Internal signal renaming

A different internal signal is not automatically a bug — only a mismatch on the *observable* outputs is.

---

## 🧠 11. Key Takeaways

- **RTL coding style matters.** The MUX study showed that how you *write* the logic shapes what synthesis *builds*.
- **Structure changes; behavior shouldn't.** The gate-level netlist can look nothing like the RTL and still be correct.
- **Optimization is not risk-free by default — it's verified.** Redundant logic gets trimmed, but GLS is what proves nothing essential went with it.
- **The library shapes the silicon.** Whatever cells SKY130 offers is what technology mapping has to work with.
- **GLS closes the loop.** RTL sim checks intent; GLS checks the thing that actually gets fabricated.
- **Signal renaming ≠ signal loss.** Compare meaning, not naming.

---

## 🚀 12. One-Shot Synthesis Script

```bash
yosys

read_verilog src/module/vsdbabysoc.v
read_verilog -I src/include src/module/rvmyth.v
read_verilog -I src/include src/module/clk_gate.v

read_liberty -lib lib/avsdpll.lib
read_liberty -lib lib/avsddac.lib
read_liberty -lib lib/sky130_fd_sc_hd__tt_025C_1v80.lib

synth -top vsdbabysoc

dfflibmap -liberty lib/sky130_fd_sc_hd__tt_025C_1v80.lib

opt

abc -liberty lib/sky130_fd_sc_hd__tt_025C_1v80.lib

check

show vsdbabysoc

flatten
setundef -zero
clean -purge
rename -enumerate

write_verilog -noattr netlist/babysoc_netlist_new.v
```

---

<div align="center">

### 📚 Closing Note

This journal isn't just "write RTL, get a netlist." It's proof that the intended behavior survives synthesis, optimization, and technology mapping — verified stage by stage, waveform by waveform.

**— Documented by Princy**

</div>
