
# ⚙️ VSD Hardware Design Program — VSDBabySoC

A hands-on guide for **VSDBabySoC**, a RISC-V-based open-source SoC integrating the **RVMYTH core**, **PLL**, and **DAC** modules.  
Covers project setup, TL-Verilog to Verilog conversion, simulation, waveform analysis, and DAC output visualization.

---

## 📘 Table of Contents

1. [Project Structure](#project-structure)
2. [Environment Setup](#environment-setup)
3. [TLV to Verilog Conversion](#tlv-to-verilog-conversion)
4. [Simulation Workflow](#simulation-workflow)
    - [Pre-Synthesis Simulation](#pre-synthesis-simulation)
    - [Waveform Viewing in GTKWave](#waveform-viewing-in-gtkwave)
    - [DAC Output in Analog Mode](#dac-output-in-analog-mode)
5. [Troubleshooting Tips](#troubleshooting-tips)
6. [Why Pre-Synthesis vs Post-Synthesis?](#why-pre-synthesis-vs-post-synthesis)
7. [Summary](#summary)

---

## 🧱 Project Structure

```txt
VSDBabySoC/
├── src/
│   ├── include/             # Header files (*.vh)
│   │   └── sandpiper.vh
│   ├── module/              # Design & simulation modules
│   │   ├── vsdbabysoc.v     # Top-level SoC
│   │   ├── rvmyth.v         # RISC-V core (from TLV)
│   │   ├── rvmyth.tlv       # TL-Verilog source
│   │   ├── avsdpll.v        # PLL module
│   │   ├── avsddac.v        # DAC module
│   │   └── testbench.v      # Simulation testbench
├── output/                  # Simulation outputs
└── compiled_tlv/            # Optional intermediate files
````

---

## ⚙️ Environment Setup

Clone the project and navigate to the working directory:

```bash
cd ~/Desktop/vsdflow/VLSI
git clone https://github.com/manili/VSDBabySoC.git
cd ~/Desktop/vsdflow/VLSI/VSDBabySoC/
```

Check modules:

```bash
ls src/module/
```

---

## 🔧 TLV to Verilog Conversion

The RVMYTH core is in TL-Verilog (rvmyth.tlv) and must be converted to `.v` for simulation.

<details> 
<summary>Conversion Steps 🔧</summary>

```bash
# 1️⃣ Install virtual environment & pip
sudo apt update
sudo apt install python3-venv python3-pip

# 2️⃣ Create & activate virtual environment
cd ~/Desktop/vsdflow/VLSI/VSDBabySoC/
python3 -m venv sp_env
source sp_env/bin/activate

# 3️⃣ Install SandPiper-SaaS
pip install pyyaml click sandpiper-saas

# 4️⃣ Convert TLV → Verilog
sandpiper-saas -i ./src/module/*.tlv -o rvmyth.v --bestsv --noline -p verilog --outdir ./src/module/
```

✅ Output: `rvmyth.v` in `src/module/`.

```bash
ls src/module/ | grep rvmyth
```

💡 Activate env before every session:

```bash
source sp_env/bin/activate
```

Deactivate when done:

```bash
deactivate
```

</details>

---

## 🧠 Simulation Workflow

### 🔹 Pre-Synthesis Simulation

<details> 
<summary>Click to view commands</summary>

```bash
cd ~/Desktop/vsdflow/VLSI/VSDBabySoC/
mkdir -p output/pre_synth_sim

iverilog -o output/pre_synth_sim/pre_synth_sim.out \
  -DPRE_SYNTH_SIM \
  -I src/include \
  -I src/module \
  src/module/testbench.v

cd output/pre_synth_sim
./pre_synth_sim.out
```

* `-DPRE_SYNTH_SIM`: Enables pre-synthesis macros
* Generates `pre_synth_sim.vcd` for GTKWave

</details>

---

### 🔹 Waveform Viewing in GTKWave

```bash
cd ~/Desktop/vsdflow/VLSI/VSDBabySoC/
gtkwave output/pre_synth_sim/pre_synth_sim.vcd
```

Drag these signals:

* `CLK` → Clock (from PLL)
* `reset` → Reset input
* `RV_TO_DAC[9:0]` → 10-bit data bus
* `OUT` → DAC output

(Insert your waveform image here)
![Waveform Example](Images/your_waveform_image.png)

---

### 🔹 DAC Output in Analog Mode

Open VCD in GTKWave, then:

* Select `OUT` → Right-click → Data Format → Analog (Step)
* Add `CLK`, `reset`, `RV_TO_DAC[9:0]` for reference

(Insert your DAC images here)
![DAC Analog](Images/your_analog_waveform1.png)
![DAC Analog](Images/your_analog_waveform2.png)

---

## 🚑 Troubleshooting Tips

| Issue               | Cause               | Solution                               |
| ------------------- | ------------------- | -------------------------------------- |
| Module redefinition | Duplicate inclusion | Include module once (testbench or CLI) |
| File not found      | Wrong include path  | Use absolute paths with `-I`           |
| Virtual env missing | Not activated       | `source sp_env/bin/activate`           |

---

## 🎯 Why Pre-Synthesis vs Post-Synthesis?

| Stage                | Description           | Purpose                                        |
| -------------------- | --------------------- | ---------------------------------------------- |
| Pre-Synthesis        | RTL-level simulation  | Verify logic correctness                       |
| Post-Synthesis (GLS) | Gate-level simulation | Detect timing violations & hardware mismatches |
| Goal                 | Combined verification | Ensures final hardware matches design intent   |

---

## 🧩 Summary

* Set up environment and project structure
* Convert TL-Verilog → Verilog using SandPiper-SaaS
* Run pre-synthesis simulation
* Analyze digital and analog DAC outputs in GTKWave
* Understand pre- vs post-synthesis simulation goals

✅ Next Step: Add post-synthesis workflow, timing analysis, and corresponding waveforms for complete documentation.

```

This version is ready to paste directly into `README.md` on GitHub. It preserves collapsible sections, code blocks, tables, images, and formatting.  

If you want, I can also **add GitHub-friendly badges and folder icons** to make it look more professional. Do you want me to do that?
```
