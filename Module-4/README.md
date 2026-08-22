

# ⚡ Combinational & Sequential Logic Optimization

### Module 4 

Synthesis doesn't stop at mapping RTL to gates — this module explores how **Yosys** simplifies, prunes, and reshapes a design afterward, targeting the **SKY130** standard-cell library throughout.

<p>
  <img src="https://img.shields.io/badge/Tool-Icarus%20Verilog-blue?style=for-the-badge" alt="Icarus Verilog">
  <img src="https://img.shields.io/badge/Tool-GTKWave-orange?style=for-the-badge" alt="GTKWave">
  <img src="https://img.shields.io/badge/Tool-Yosys-2ea44f?style=for-the-badge" alt="Yosys">
  <img src="https://img.shields.io/badge/PDK-SKY130-red?style=for-the-badge" alt="SKY130">
  <img src="https://img.shields.io/badge/Language-Verilog-9cf?style=for-the-badge" alt="Verilog">
</p>

`Part of the` [**RTL Workshop**](https://github.com/ArpithaGarrepalli/RTL_Workshop) `series`

<br>

[Concepts](#-concepts) · [Labs](#-labs) · [Results](#-results-at-a-glance) · [Takeaways](#-takeaways) · [Credits](#-credits)

</div>

---

## 🧠 Concepts

<details>
<summary><b>What logic optimization actually means</b></summary>
<br>

Mapping RTL to gates is only step one. Once that mapping exists, Yosys re-scans the netlist looking for anything that can be simplified, deleted, or rearranged — without changing what the circuit actually does. The payoff: smaller, faster, lower-power hardware than a literal RTL-to-gates translation would give you.

</details>

<details>
<summary><b>Combinational optimization</b></summary>
<br>

Pure combinational logic gives Yosys the most freedom — it can rewrite Boolean expressions however it likes, as long as the truth table doesn't change. Redundant terms, dead branches, and inefficient gate choices all get cleaned up here.

**What it buys you:** fewer gates · simpler expressions · smaller area · shorter delay · lower power

<img width="1254" height="660" alt="Combinational optimization example" src="https://github.com/user-attachments/assets/9cd8f384-c8fc-49cc-a7ce-c0c695ab1e37" />

</details>

<details>
<summary><b>Sequential optimization</b></summary>
<br>

A stricter case — every flip-flop's state behavior has to survive exactly as written. Within that constraint, Yosys still deletes flip-flops that don't affect any output, propagates known constants through registers, and repositions logic for better timing.

**Typical moves:** drop redundant flip-flops · propagate constants forward · remove unreachable logic · tighten timing

<img width="1214" height="651" alt="Sequential optimization example" src="https://github.com/user-attachments/assets/b4a6ee3d-b644-400a-94a0-b6def0613459" />

**Worked example — `dff_const1.v`**

```verilog
module dff_const1(input clk, input reset, output reg q);
always @(posedge clk, posedge reset)
begin
	if(reset)
		q <= 1'b0;
	else
		q <= 1'b1;
end
endmodule
```

<img width="1912" height="1016" alt="dff_const1 schematic" src="https://github.com/user-attachments/assets/764344e4-3ff5-4a23-b7a6-f266d9459768" />

`q` still toggles between two values depending on `reset` — yet the synthesized netlist is already tighter than a naive reading of the RTL suggests.

<img width="1912" height="1011" alt="dff_const1 waveform" src="https://github.com/user-attachments/assets/0fd6f6a5-501f-463e-8232-1274d0841128" />

</details>

<details>
<summary><b>Constant propagation</b></summary>
<br>

If a signal's value is knowable in advance, there's no reason to build hardware to compute it. Yosys substitutes the constant everywhere the signal fans out, then deletes whatever gates are left with nothing to do.

**Benefits:** less logic to route · smaller cell count · better timing margins · lower power

<img width="1771" height="835" alt="Constant propagation netlist" src="https://github.com/user-attachments/assets/cddcdd00-a958-469c-97e1-8ca63e8b0d76" />

</details>

<details>
<summary><b>Unused output elimination</b></summary>
<br>

Any wire or register that never feeds a real output contributes nothing — Yosys removes it outright, keeping gate count tied to what actually matters. Lab 7 (the counter) shows this in a very literal way.

<img width="890" height="826" alt="Unused output elimination" src="https://github.com/user-attachments/assets/4908022b-0bd3-4ddb-a0ee-2368dbb0b2b8" />

</details>

<details>
<summary><b>State optimization</b></summary>
<br>

FSMs often carry states that are functionally equivalent or simply unreachable. Yosys can merge the equivalent ones and drop the unreachable ones, shrinking the FSM's footprint.

**Involves:** merging identical states · leaner state encoding · simpler next-state logic

</details>

<details>
<summary><b>Logic cloning</b></summary>
<br>

The opposite move — instead of deleting hardware, cloning **duplicates** a cell that's driving too much fan-out. Splitting the load across copies shortens the delay on the path that was suffering.

</details>

<details>
<summary><b>Retiming</b></summary>
<br>

Retiming shifts flip-flops across the surrounding combinational logic without changing what the circuit computes. The goal is to rebalance delay across pipeline stages, raising the achievable clock frequency. Unlike everything else here, retiming only ever moves **register placement**.

</details>

<details>
<summary><b>Yosys optimization passes, at a glance</b></summary>
<br>

| Pass | Purpose |
|---|---|
| Constant propagation | Substitute known-constant signal values |
| Dead logic removal | Delete logic with no path to an output |
| Boolean simplification | Reduce Boolean expressions |
| Unused wire removal | Drop unreferenced signals |
| Unused cell removal | Drop unreferenced gates/cells |
| Expression simplification | Collapse equivalent expressions |
| Resource sharing | Reuse hardware across similar ops |

</details>

---

## 🧪 Labs

<details open>
<summary><b>01 · AND gate → <code>and2</code></b> <sub>(opt_check)</sub></summary>
<br>

```verilog
module opt_check (input a, input b, output y);
assign y = a & b;
endmodule
```

```bash
yosys
read_verilog opt_check.v
synth -top opt_check
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

<img width="1912" height="1025" alt="opt_check schematic" src="https://github.com/user-attachments/assets/758cfa83-9708-4c4a-b3d9-8e1d60604b17" />

> **Result:** maps cleanly to a single SKY130 `and2` cell.

</details>

<details>
<summary><b>02 · OR gate → <code>or2</code></b> <sub>(opt_check2)</sub></summary>
<br>

```verilog
module opt_check2 (input a, input b, output y);
assign y = a | b;
endmodule
```

```bash
yosys
read_verilog opt_check2.v
synth -top opt_check2
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

<img width="1915" height="1015" alt="opt_check2 schematic" src="https://github.com/user-attachments/assets/debda19d-e03d-42e0-b903-82b4d46716e3" />

> **Result:** maps to a single SKY130 `or2` cell.

</details>

<details>
<summary><b>03 · Three-input AND → <code>and3</code></b> <sub>(opt_check3)</sub></summary>
<br>

```verilog
module opt_check3 (input a, input b, input c, output y);
assign y = a & b & c;
endmodule
```

```bash
yosys
read_verilog opt_check3.v
synth -top opt_check3
abc -liberty ../lib/sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

<img width="1916" height="1017" alt="opt_check3 schematic" src="https://github.com/user-attachments/assets/139f6627-60eb-48cd-b81d-1a7397d44939" />

> **Result:** one SKY130 `and3` cell — not two chained `and2`s.

</details>

<details>
<summary><b>04 · Async reset, output still depends on it</b> <sub>(dff_const1)</sub></summary>
<br>

`dff_const1` and `dff_const2` (Labs 04–05) both drive `q` toward a fixed value, but differ in how much that value actually depends on `reset` — which changes how far Yosys can optimize each one.

```bash
vim dff_const1.v
```

<img width="1918" height="1016" alt="dff_const1 and dff_const2 source" src="https://github.com/user-attachments/assets/afc1eba2-d24b-4f9c-b4de-c7d5d26650e9" />

```bash
iverilog -o dff_const1.out dff_const1.v tb_dff_const1.v
gtkwave tb_dff_const1.vcd
```

<img width="1912" height="1011" alt="dff_const1 simulation" src="https://github.com/user-attachments/assets/0fd6f6a5-501f-463e-8232-1274d0841128" />

```bash
yosys
read_liberty -lib sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const1.v
synth -top dff_const1
abc -liberty sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

<img width="1912" height="1016" alt="dff_const1 synthesized" src="https://github.com/user-attachments/assets/764344e4-3ff5-4a23-b7a6-f266d9459768" />

</details>

<details>
<summary><b>05 · Fully constant output, reset dropped</b> <sub>(dff_const2)</sub></summary>
<br>

`dff_const2` takes it further — `q` is `1'b1` on *both* branches, so `reset` has no real influence on the output.

```bash
iverilog -o dff_const2.out dff_const2.v tb_dff_const2_.v
gtkwave tb_dff_const2_.vcd
```

<img width="1918" height="1022" alt="dff_const2 simulation" src="https://github.com/user-attachments/assets/6ea7c5cd-7546-4df8-b78b-10a295ecc131" />

```bash
yosys
read_liberty -lib sky130_fd_sc_hd__tt_025C_1v80.lib
read_verilog dff_const2.v
synth -top dff_const2
abc -liberty sky130_fd_sc_hd__tt_025C_1v80.lib
show
```

<img width="1917" height="1017" alt="dff_const2 synthesized" src="https://github.com/user-attachments/assets/97d05fa9-af67-4421-8eec-15dcba996191" />

> **Result:** reset-independent output → Yosys drops the reset logic that Lab 04 still needed. Visibly smaller circuit.

</details>

<details>
<summary><b>06 · Synchronous reset flip-flop</b> <sub>(dff_const3)</sub></summary>
<br>

```verilog
module dff_const3(input clk, input reset, output reg q);
always @(posedge clk)
begin
    if(reset)
        q <= 1'b0;
    else
        q <= 1'b1;
end
endmodule
```

`reset` only appears *inside* the clocked block here, not the sensitivity list — synchronous, unlike the async reset in Labs 04–05.

```bash
iverilog -o dff_const3.out dff_const3.v dff_const3_tb.v
gtkwave dff_const3.vcd
```

<img width="1918" height="1022" alt="dff_const3 simulation" src="https://github.com/user-attachments/assets/c4e60126-3d23-403b-9b41-3fc9f35b185b" />

```bash
yosys
read_verilog dff_const3.v
synth -top dff_const3
show
```

<img width="1918" height="1017" alt="dff_const3 synthesized" src="https://github.com/user-attachments/assets/68e3b604-d529-4d94-a00c-96dc5b719767" />

</details>

<details>
<summary><b>07 · 3-bit counter, 1 bit actually used</b> <sub>(counter_opt)</sub></summary>
<br>

Only the LSB of a 3-bit counter is ever wired to an output — a direct test of unused-output elimination.

```verilog
module counter_opt(input clk, input reset, output q);
reg [2:0] count;
assign q = count[0];
always @(posedge clk, posedge reset)
begin
    if(reset)
        count <= 3'b000;
    else
        count <= count + 1;
end
endmodule
```

```bash
yosys
read_verilog counter_opt.v
synth -top counter_opt
show
```

<img width="1037" height="701" alt="counter_opt schematic" src="https://github.com/user-attachments/assets/7b8dd0f8-921d-4d52-b72e-871841630305" />

`count[1]` and `count[2]` never reach an output, so Yosys builds no flip-flops for them:

```bash
write_verilog -noattr counter_opt_net.v
gvim counter_opt_net.v
```

<img width="1771" height="835" alt="counter_opt netlist" src="https://github.com/user-attachments/assets/db902d0b-353f-4cda-b1ca-63c04cde1fab" />

<img width="890" height="826" alt="counter_opt final gate count" src="https://github.com/user-attachments/assets/f5ed2dd2-3108-450e-9734-7e2ac2b1c096" />

> **Result:** the RTL describes 3 bits of state — only **1** flip-flop is actually synthesized.

</details>

---

## 📊 Results at a Glance

| # | Lab | Focus | Result |
|:-:|---|---|---|
| 01 | opt_check | AND gate | → `and2` |
| 02 | opt_check2 | OR gate | → `or2` |
| 03 | opt_check3 | 3-input AND | → `and3` (not chained) |
| 04 | dff_const1 | Const. propagation | Reset-dependent → reset logic kept |
| 05 | dff_const2 | Const. propagation | Reset-independent → reset logic dropped |
| 06 | dff_const3 | Sync reset | Reset lives only in the clocked block |
| 07 | counter_opt | Unused bits | 3 bits described → 1 flip-flop built |

---

## 🎯 Takeaways

- Combinational and sequential optimization play by different rules — sequential has to preserve register behavior exactly.
- Constant propagation showed up directly in the `dff_const1` vs `dff_const2` contrast.
- Dead logic and unused outputs get stripped automatically — `counter_opt` cut three flip-flops down to one.
- Async reset (`dff_const1`/`dff_const2`) vs sync reset (`dff_const3`) map to hardware differently.
- Basic gates (AND, OR, 3-input AND) mapped cleanly onto SKY130 standard cells.
- State optimization, logic cloning, and retiming are covered conceptually, even without a dedicated lab each.
- Every claim here was checked against real synthesized netlists, schematics, and waveforms — not taken on faith.

> Yosys isn't just translating RTL into gates one-for-one — it's actively folding in constants, dropping dead logic, and eliminating registers, all while guaranteeing the circuit behaves exactly like the original description. Balancing **area, timing, and power** that way is the whole point of synthesis.

---



## 👤 Credits

**M.Princy Florence**
BTECH-ECE
