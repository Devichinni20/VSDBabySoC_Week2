
# VSD Hardware Design Program: VSDBabySoC Post-Synthesis Simulation

  

Post-synthesis simulation is critical for validating both **functionality and timing** after RTL code has been synthesized into standard cells.

---

## Table of Contents

1. [Introduction](#introduction)
2. [Pre-Synthesis vs Post-Synthesis Simulation](#pre-synthesis-vs-post-synthesis-simulation)
3. [Synthesis Flow](#synthesis-flow)
   - [Step 1: Load RTL Modules](#step-1-load-rtl-modules)
   - [Step 2: Load Liberty Files](#step-2-load-liberty-files)
   - [Step 3: Synthesize the Design](#step-3-synthesize-the-design)
   - [Step 4: Map Flip-Flops to Standard Cells](#step-4-map-flip-flops-to-standard-cells)
   - [Step 5: Optimization & Technology Mapping](#step-5-optimization--technology-mapping)
   - [Step 6: Clean-Up & Renaming](#step-6-clean-up--renaming)
   - [Step 7: Check Statistics](#step-7-check-statistics)
   - [Step 8: Write Synthesized Netlist](#step-8-write-synthesized-netlist)
4. [Post-Synthesis Simulation](#post-synthesis-simulation)
   - [Step 1: Compile Testbench](#step-1-compile-testbench)
   - [Step 2: Navigate to Simulation Directory](#step-2-navigate-to-simulation-directory)
   - [Step 3: Run Simulation](#step-3-run-simulation)
   - [Step 4: View Waveforms in GTKWave](#step-4-view-waveforms-in-gtkwave)
5. [Comparing Pre- and Post-Synthesis Outputs](#comparing-pre--and-post-synthesis-outputs)

---

## Introduction

Post-synthesis simulation validates that the **synthesized gate-level netlist** behaves identically to the original RTL, both logically and in terms of timing.  

It identifies issues such as:

- Unintended latches or glitches
- Race conditions due to gate delays
- Timing violations

This ensures a robust design before hardware implementation.

---

## Pre-Synthesis vs Post-Synthesis Simulation

- **Pre-Synthesis Simulation**  
  Focuses on **logical correctness** of RTL. Fast and ideal for detecting functional errors early.

- **Post-Synthesis Simulation**  
  Validates **logic and timing** of the synthesized netlist, including gate delays and technology-specific constraints.

Performing both ensures the final design is functionally correct and meets timing requirements.

---

## Synthesis Flow

All commands assume the working directory:  

```bash
devichinni20@devi-VirtualBox:~Desktop/vsdflow/VLSI/VSDBabySoC/
````

### Step 1: Load RTL Modules

1. Launch Yosys:

```bash
yosys
```

2. Read top-level module:

```yosys
read_verilog src/module/vsdbabysoc.v
```
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-03%2019-57-42.png)
3. Copy include files:

```bash
cp -r src/include/sp_verilog.vh .
cp -r src/include/sandpiper.vh .
cp -r src/include/sandpiper_gen.vh .
```
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-03%2019-56-08%20(Copy).png)
4. Read dependent modules:

```yosys
read_verilog -I src/include/ src/module/rvmyth.v
read_verilog -I src/include/ src/module/clk_gate.v
```

> ⚠️ Ensure include files are in the working directory to avoid parsing errors.

---

### Step 2: Load Liberty Files

```yosys
read_liberty -lib src/lib/avsdpll.lib
read_liberty -lib src/lib/avsddac.lib
read_liberty -lib src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

---
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-03%2019-58-09.png)
### Step 3: Synthesize the Design

```yosys
synth -top vsdbabysoc
```

---

### Step 4: Map Flip-Flops to Standard Cells

```yosys
dfflibmap -liberty src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib
```

---
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-03%2020-00-10.png)
### Step 5: Optimization & Technology Mapping

```yosys
opt
abc -liberty src/lib/sky130_fd_sc_hd__tt_025C_1v80.lib -script +strash;scorr;ifraig;retime;{D};strash;dch,-f;map,-M,1,{D}
```
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-03%2020-06-39.png)
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-03%2020-06-55.png)
**Optimization Steps:**

| Command      | Purpose                                                    |
| ------------ | ---------------------------------------------------------- |
| strash       | Reduce logic redundancy                                    |
| scorr        | Sequential sweeping to remove redundant logic              |
| ifraig       | Equivalence checking & incremental optimization            |
| retime;{D}   | Move registers to optimize timing                          |
| dch,-f       | Delay-aware combinational optimization                     |
| map,-M,1,{D} | Map logic to standard cells minimizing area & timing-aware |

---
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-03%2020-06-12.png)
### Step 6: Clean-Up & Renaming

```yosys
flatten
setundef -zero
clean -purge
rename -enumerate
```

| Command           | Purpose                                       |
| ----------------- | --------------------------------------------- |
| flatten           | Flatten hierarchy into a single-level netlist |
| setundef -zero    | Replace undefined values (`x`) with `0`       |
| clean -purge      | Remove unused wires, cells, and modules       |
| rename -enumerate | Unique numbering for internal wires and cells |

---
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-03%2020-08-45.png)
### Step 7: Check Statistics

```yosys
stat
```
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-03%2020-13-19.png)
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-03%2020-13-29.png)
> Review the number of cells, wires, memories, and other statistics.

---

### Step 8: Write Synthesized Netlist

```yosys
write_verilog -noattr output/post_synth_sim/vsdbabysoc.synth.v
```

---
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-03%2020-30-45.png)
## Post-Synthesis Simulation

### Step 1: Compile Testbench

1. Copy standard cell and primitive models:

```bash
cp -r ~/VLSI/sky130RTLDesignAndSynthesisWorkshop/my_lib/verilog_model/sky130_fd_sc_hd.v src/module/
cp -r ~/VLSI/sky130RTLDesignAndSynthesisWorkshop/my_lib/verilog_model/primitives.v src/module/
```

2. Copy synthesized netlist to module directory:

```bash
cp -r output/post_synth_sim/vsdbabysoc.synth.v src/module/
```

3. Compile testbench:

```bash
iverilog -o output/post_synth_sim/post_synth_sim.out \
-DPOST_SYNTH_SIM -DFUNCTIONAL -DUNIT_DELAY=#1 \
-I src/include -I src/module src/module/testbench.v
```

> ⚠️ Fix any syntax errors in `sky130_fd_sc_hd.v` if encountered, e.g.:

```verilog
`endif // SKY130_FD_SC_HD__LPFLOW_BLEEDER_FUNCTIONAL_V
```

---
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-04%2001-21-42.png)
### Step 2: Navigate to Simulation Directory

```bash
cd output/post_synth_sim/
```

---

### Step 3: Run Simulation

```bash
./post_synth_sim.out
```

---

### Step 4: View Waveforms in GTKWave

```bash
gtkwave post_synth_sim.vcd
```

---
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-04%2001-31-27.png)
## Comparing Pre- and Post-Synthesis Outputs

To verify **functional equivalence**, compare the GTKWave outputs from pre-synthesis and post-synthesis simulations.

![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/VirtualBox_opensource_tool_ubuntu_02_10_2025_16_06_27.png)
![](https://github.com/Devichinni20/VSDBabySoC_Week2/blob/50d77c4df705b8d04dc50609ef74c10a4014d2f9/Labs_Part_2/Images/Screenshot%20from%202025-10-04%2001-41-19.png)

✅ Matching waveforms confirm that the synthesis process **preserves functionality**.

---

## Notes & Best Practices

* Always perform **pre-synthesis simulation first** to catch RTL errors early.
* Use **post-synthesis simulation** to verify timing and gate-level behavior.
* Keep include files and standard cell models up-to-date to avoid compilation issues.
* Document any modifications to standard libraries for reproducibility.

```

