# VSDBabySoC: Physical Design & Multi-Corner Timing (SKY130 HD)

A study of the physical design flow and multi-corner static timing analysis (STA) for VSDBabySoC, built on the SKY130 HD standard-cell library.

---

## At a Glance

| Item | Detail |
|---|---|
| Library | SKY130 HD |
| Clock constraint | 11 ns (about 90.91 MHz) |
| Corners analyzed | 16 |
| Setup WNS > 0 | 10 corners |
| Setup WNS < 0 | 6 corners |
| Best setup WNS | +8.22 ns |
| Worst setup WNS | -31.85 ns |
| Setup closure across all corners | **Not demonstrated** |

---

## Part A: Physical Design

### The RTL-to-GDSII pipeline

The design moves through these steps, in order:

1. Synthesis
2. Floorplanning
3. Power planning
4. Placement
5. Clock Tree Synthesis (CTS)
6. Routing
7. Parasitic extraction
8. Static Timing Analysis
9. Physical verification
10. GDSII generation

> **Note:** This list shows the full flow only. A stage is counted as completed only when a screenshot or report backs it up.

### Evidence from the flow

![Physical design flow screenshot](https://github.com/user-attachments/assets/bf8b8862-08d4-41b0-94ea-e0033a9290aa)

---

## Part B: Timing Concepts

| Term | Meaning |
|---|---|
| **Setup check** | Data must reach the capturing element early enough before the active clock edge. |
| **Hold check** | Data must stay stable for the required time after the active clock edge. |
| **WNS** | Worst Negative Slack: the single most critical slack value. |
| **TNS** | Total Negative Slack: the sum of all negative slacks. |
| **Slack** | Positive means the path meets timing; negative means a violation. |

![Setup and hold timing illustration](https://github.com/user-attachments/assets/f2bda23e-3d80-48dd-bb35-20603e092040)

---

## Part C: Multi-Corner STA Results

STA verifies timing against the constraints without needing simulation test vectors. Running it across several process, voltage and temperature (PVT) corners shows how the design behaves under different operating conditions.

The latest report covers **16 SKY130 HD corners** at an **11 ns clock**.

### Setup outcome

| Result | Count / Value |
|---|---|
| Passing corners (positive WNS) | 10 |
| Failing corners (negative WNS) | 6 |
| Best case | +8.22 ns |
| Worst case | -31.85 ns |

Because six corners show negative slack, these results do **not** prove setup timing closure for all 16 corners.

### Report screenshots

![STA report, multi-corner summary](https://github.com/user-attachments/assets/8bc64a12-c768-41c4-bb8d-d4f194ba209d)

![STA report, additional detail](https://github.com/user-attachments/assets/6183cc76-6b61-458b-8da9-38db51745fd1)

---

## Takeaway

This repository documents the physical design concepts and multi-corner timing analysis explored for VSDBabySoC on the SKY130 HD library. The main lesson: timing closure should only be claimed after setup is checked across every PVT corner, not just the favorable ones.
