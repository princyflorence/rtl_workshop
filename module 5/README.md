# 🚀 Module 5 – Optimization in Synthesis



## 📖 Overview

This module explores **synthesis optimization techniques** in Verilog HDL using the **Yosys** synthesis tool. A series of combinational and sequential digital circuits were designed, simulated, synthesized, and analyzed to understand how synthesis tools:

- Remove redundant logic
- Minimize hardware resources
- Improve overall circuit efficiency
- Flag unintended storage elements (latches) caused by incomplete RTL coding

---

## 📑 Table of Contents

**🔹 IF Statement Behavior**

1. Incomplete IF Statement
2. RTL Schematic of Incomplete IF Statement
3. RTL Simulation of Incomplete IF Statement
4. RTL Schematic of Incomplete IF-ELSE Statement

**🔹 Case Statement Behavior**

5. Incomplete Case Statement
6. Complete Case Statement
7. Partial Case Assignment
8. Bad Case Assignment

**🔹 Combinational Circuit Verification**

9. Multiplexer (MUX)
10. Demultiplexer (DEMUX)
11. Ripple Carry Adder

**🔹 Summary**

12. Overall Results
13. Conclusion

**Total: 11 Experiments**

---

## 🎯 Objectives

- Understand core synthesis optimization techniques
- Design and simulate Verilog circuits
- Perform RTL synthesis using Yosys
- Observe optimizations applied by the synthesis tool
- Analyze generated RTL schematics
- Verify functional correctness using GTKWave
- Study optimized hardware implementation

---

## 🛠️ Tools & Technologies Used

| Tool | Purpose |
|------|---------|
| **Verilog HDL** | Hardware description language |
| **Icarus Verilog** | Compilation & simulation |
| **GTKWave** | Waveform analysis |
| **Yosys** | RTL synthesis & optimization |
| **SKY130 PDK** | Technology mapping (standard cell library) |
| **Ubuntu Linux** | Development environment |

---

## 1 – Incomplete IF Statement

### 📖 Overview
An **incomplete IF statement** assigns the output only when a specific condition is satisfied, leaving other conditions without assignment. During RTL simulation, the output retains its previous value whenever the condition is false. During synthesis, this behavior causes **latch inference**, since the tool must insert a latch to preserve the last known output. This experiment highlights why every possible condition must be handled in combinational logic.

### ⚙️ Simulation Commands
```bash
iverilog -o incom_if incom_if.v incom_if_tb.v
gtkwave incom_if.vcd
```

### 🖼️ Output


### 📊 Observation
- The output changes only when the select signal (`sel`) is high.
- When `sel` is low, the output retains its previous value since no assignment is made.
- This memory-retention behavior results in latch inference during synthesis.
- Demonstrates why complete conditional assignments are essential in combinational design.

### ✅ Result
RTL simulation confirmed that the output holds its previous value under unspecified conditions — illustrating latch inference caused by incomplete conditional assignments.

---

## 2 – RTL Schematic of Incomplete IF Statement

### 📖 Overview
This experiment synthesizes the incomplete IF statement using Yosys. Since the output isn't assigned for every condition, the tool infers a latch to preserve the previous value. The RTL schematic visually confirms this unintended storage element.

### ⚙️ Synthesis Commands
```bash
yosys
read_verilog incom_if.v
synth -top incom_if
show
```

### 🖼️ Output


### 📊 Observation
- The RTL schematic contains a latch inferred by the synthesis tool.
- The latch stores the previous output whenever the IF condition is not satisfied.
- Confirms that incomplete assignments introduce unintended memory elements.

### ✅ Result
The synthesized schematic confirms latch inference caused by an incomplete IF statement, reinforcing the need for full conditional coverage.

---

## 3 – RTL Simulation of Incomplete IF Statement

### 📖 Overview
This experiment examines the RTL simulation waveform of the incomplete IF statement in detail, observing how the output behaves when conditions are not fully specified.

### ⚙️ Simulation Commands
```bash
iverilog -o incom_if incom_if.v incom_if_tb.v
gtkwave incom_if.vcd
```

### 🖼️ Output

### 📊 Observation
- The output changes only when the select signal is active.
- When the condition is false, the output retains its previous value.
- Confirms the output is not assigned under all possible conditions.

### ✅ Result
The waveform validates latch-like behavior arising from incomplete conditional coding, consistent with the synthesized schematic.

---

## 4 – RTL Schematic of Incomplete IF-ELSE Statement

### 📖 Overview
This experiment synthesizes an incomplete IF-ELSE statement to observe its impact on generated hardware. Since not every condition assigns the output, a latch is again inferred.

### ⚙️ Synthesis Commands
```bash
yosys
read_verilog incom_if2.v
synth -top incom_if2
show
```

### 🖼️ Output


### 📊 Observation
- The RTL schematic represents the synthesized hardware for the IF-ELSE design.
- The synthesis process analyzes conditional assignments to generate corresponding logic.
- Proper conditional coverage ensures predictable, optimized hardware generation.

### ✅ Result
The RTL schematic was successfully generated, reinforcing the importance of correct conditional coding in digital design.

---

## 5 – Incomplete Case Statement

### 📖 Overview
Demonstrates the synthesis result of an **incomplete case statement**. Since not all input combinations are covered, the tool infers a latch to preserve the previous output value.

### 💻 Code
```verilog
module incomp_case (
    input  i0, input i1, input i2,
    input  [1:0] sel,
    output reg y
);
    always @(*) begin
        case(sel)
            2'b00 : y = i0;
            2'b01 : y = i1;
        endcase
    end
endmodule
```


### 📊 Observation
- The synthesized netlist includes a latch since not every `sel` value is covered.
- The waveform confirms the output holds its previous value for undefined combinations.

### ✅ Result
Both synthesis and simulation confirm latch inference due to incomplete case coverage — a potential source of synthesis-simulation mismatc.

---

## 6 – Complete Case Statement

### 📖 Overview
Demonstrates a **complete case statement**, where every input condition is defined (via a `default` branch), allowing Yosys to generate purely combinational logic with no latches.

### 💻 Code
```verilog
module comp_case (
    input  i0, input i1, input i2,
    input  [1:0] sel,
    output reg y
);
    always @(*) begin
        case(sel)
            2'b00   : y = i0;
            2'b01   : y = i1;
            default : y = i2;
        endcase
    end
endmodule
```

### 🖼️ Synthesized Netlist

### 🖼️ Simulation Waveform


### ✅ Result
The synthesized design contains only combinational logic — no latches — since all input combinations are covered, producing predictable, optimized hardware.

---

## 7 – Partial Case Assignment

### 📖 Overview
Demonstrates the effect of **partial assignments** within a case statement. When an output isn't assigned in every branch, synthesis infers latch-based storage to hold the previous value.

### 💻 Code
```verilog
module partial_case_assign (
    input  i0, input i1, input i2,
    input  [1:0] sel,
    output reg y, output reg x
);
    always @(*) begin
        case(sel)
            2'b00 : begin
                y = i0;
                x = i2;
            end
            2'b01 : y = i1;
            default : begin
                x = i1;
            end
        endcase
    end
endmodule
```

### 🖼️ Synthesized Netlist


### 🖼️ Simulation Waveform

### 📊 Observation
- Latches are inferred for `x` and `y` because their assignments are missing in some branches.
- The waveform confirms outputs hold their previous state whenever an assignment is skipped.

### ✅ Result
Both synthesis and simulation confirm that partial assignments introduce unintended sequential elements — a reminder to assign **every output in every branch**.

---

## 8 – Bad Case Assignment

### 📖 Overview
Verifies RTL functionality of the design using GTKWave. Different input combinations are applied via the testbench to confirm correct circuit behavior prior to synthesis.

### 💻 Code
```verilog
module bad_case (
    input  i0, input i1, input i2, input i3,
    input  [1:0] sel,
    output reg y
);
    always @(*) begin
        case(sel)
            2'b00 : y = i0;
            2'b01 : y = i1;
            2'b10 : y = i2;
            2'b11 : y = i3;
        endcase
    end
endmodule
```

### 🖼️ Output
<img width="958" height="930" alt="Bad case assignment - waveform" src="https://github.com/user-attachments/assets/fa936eb8-adfb-4bfa-a8a5-a29689682b1b" />

### 📊 Observation
The waveform shows the output changes correctly for every valid input combination, with no unexpected behavior during RTL simulation.

### ✅ Result
RTL simulation verifies the functional correctness of the design, matching expected behavior.

---

## 9 – Multiplexer (MUX) Verification

### 📖 Overview
Demonstrates the operation of a multiplexer, where select lines determine which input is routed to the output.

### 🖼️ Output
<img width="958" height="930" alt="MUX - simulation waveform" src="https://github.com/user-attachments/assets/ff95c75d-87d0-4f29-8751-25d15fadfdc8" />

### 📊 Observation
The output follows the selected input as the select signal changes, confirming correct switching between all input lines.

### ✅ Result
The multiplexer functions correctly; simulation validates proper data selection based on the select inputs.

---

## 10 – Demultiplexer (DEMUX) Verification

### 📖 Overview
Demonstrates the behavior of a demultiplexer, where a single input is routed to one of several outputs based on the select signal.

### 🖼️ Output


### 📊 Observation
Only the selected output receives the input signal, while all remaining outputs stay inactive.

### ✅ Result
The demultiplexer operates correctly, successfully routing the input to the selected output line.

---

## 11 – Ripple Carry Adder

### 📖 Overview
Verifies the functionality of an 8-bit Ripple Carry Adder, which performs binary addition by propagating the carry from one full-adder stage to the next.

### 🖼️ Output


### 📊 Observation
Sum and carry outputs are generated correctly across different input values, with carry propagation observed across all adder stages.

### ✅ Result
The Ripple Carry Adder correctly performs binary addition; simulation confirms accurate sum and carry generation for all tested cases.

---

## 📊 Overall Results

| Design | Latch Inferred? | Reason |
|---|---|---|
| Incomplete IF | ✅ Yes | Missing `else` branch |
| Incomplete IF-ELSE | ✅ Yes | Not all conditions assigned |
| Incomplete Case | ✅ Yes | Missing `default`/case branches |
| Complete Case | ❌ No | All conditions covered |
| Partial Case Assignment | ✅ Yes | Outputs not assigned in every branch |
| Bad Case (fully specified) | ❌ No | All 4 select values covered |
| MUX | ❌ No | Fully combinational |
| DEMUX | ❌ No | Fully combinational |
| Ripple Carry Adder | ❌ No | Fully combinational arithmetic |

All designs were successfully simulated and synthesized. Functional verification was performed using **GTKWave**, while RTL schematics generated by **Yosys** confirmed synthesis optimizations such as:

- Constant propagation
- Logic simplification
- Resource sharing
- Multiplexer optimization
- Efficient arithmetic implementation

The optimized circuits preserved expected functionality while eliminating unnecessary hardware resources.

---

## ✅ Conclusion

Module 5 provided hands-on experience with synthesis optimization using Verilog and Yosys. Working through incomplete IF/case statements alongside their complete counterparts made clear how missing conditional coverage leads to unintended latch inference, while fully specified designs synthesize into clean combinational logic. These experiments strengthened understanding of efficient digital design, RTL synthesis, and technology mapping for ASIC design using the **SKY130** standard-cell library.

**Key takeaway:** Always assign every output under every possible condition in combinational logic — incomplete coding is the single most common cause of unintended latch inference during synthesis.

---

## 👤 Author

**M.Princy Florence**
B.Tech – Electronics & Communication Engineering
Anurag University
