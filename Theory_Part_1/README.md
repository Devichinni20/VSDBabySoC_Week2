# 🧠 VSDBabySoC — RISC-V Based Open-Source System on Chip

A compact and educational **System on Chip (SoC)** built around the **RVMYTH RISC-V processor**, featuring a **Phase-Locked Loop (PLL)** for stable clock generation and a **10-bit Digital-to-Analog Converter (DAC)** for digital-to-analog interfacing.  
Fabricated using **Sky130 technology**, this project serves as a hands-on platform for learning and experimenting with **open-source hardware design**.

---

## 📘 Table of Contents

1. [Overview](#overview)
2. [Understanding System on Chip (SoC)](#understanding-system-on-chip-soc)
3. [Types of SoCs](#types-of-socs)
4. [Introduction to VSDBabySoC](#introduction-to-vsdbabysoc)
5. [Phase-Locked Loop (PLL)](#phase-locked-loop-pll)
6. [Digital-to-Analog Converter (DAC)](#digital-to-analog-converter-dac)
7. [Key Takeaways](#key-takeaways)

---

## 🧩 Overview

**VSDBabySoC** integrates three essential IP cores — the **RVMYTH CPU**, **PLL**, and **10-bit DAC** — to form a minimal, functional SoC that demonstrates real-world **digital-to-analog conversion** and **clock synchronization**.

The system flow is as follows:
1. The **PLL** generates a precise, stable clock signal.
2. The **RVMYTH processor** cycles data through its registers (e.g., `r17`) for DAC input.
3. The **DAC** converts this digital stream into analog output (stored in `OUT`), which can drive analog devices such as TVs or mobile speakers.

---

## ⚙️ Understanding System on Chip (SoC)

A **System on a Chip (SoC)** is essentially a complete computing system built into a single integrated circuit. It contains everything a standalone computer would need — CPU, memory, I/O, and more.

### 🧠 Core Components

| Component | Function |
|------------|-----------|
| **CPU** | Executes instructions, handles data processing |
| **Memory (RAM/ROM)** | Temporary & permanent storage |
| **I/O Ports** | Interface for sensors, displays, etc. |
| **GPU** | Handles visual rendering and graphics |
| **DSP** | Processes audio and video efficiently |
| **Power Management** | Controls voltage and power distribution |

### 📸 SoC Architecture
<img src="https://github.com/Devichinni20/VSDBabySoC_Week2/blob/0b5f3e4de87b8f3b063ca802d2f3453d7038b465/Theory_Part_1/Images/Screenshot%202025-10-01%20204256.png" alt="SoC Architecture" width="700">

### 💡 Why SoCs Matter

- 🧱 **Compact** – Combines multiple functions into one chip  
- ⚡ **Energy Efficient** – Reduces interconnect distances and power loss    
- 🚀 **High Performance** – Faster internal communication  
- 💰 **Cost Effective** – Fewer components, lower production cost  
- 🧩 **Reliable** – Fewer failure points

---

## 🧱 Types of SoCs

SoCs can be broadly categorized based on their design intent and application:

| Type | Description | Common Use |
|------|--------------|-------------|
| **Microcontroller-based SoC** | Focused on control and automation | IoT devices, appliances |
| **Microprocessor-based SoC** | Designed for high-level processing and multitasking | Smartphones, tablets |
| **Application-Specific SoC** | Optimized for targeted high-performance tasks | AI accelerators, GPUs |

### 📸 SoC Design Flow
<img src="https://github.com/Devichinni20/VSDBabySoC_Week2/blob/b421c661a2a97e0a55f35acaef937e436df157e5/Theory_Part_1/Images/Screenshot%202025-10-01%20204519.png" alt="SoC Design Flow" width="700">
<img src="https://github.com/Devichinni20/VSDBabySoC_Week2/blob/b421c661a2a97e0a55f35acaef937e436df157e5/Theory_Part_1/Images/Screenshot%202025-10-01%20204800.png" alt="SoC Design Flow 2" width="700">

---

## 🧮 Introduction to VSDBabySoC

**VSDBabySoC** is a simple, open-source RISC-V-based SoC designed to integrate three open hardware IPs in a single layout for testing and learning.

### 🧠 Components

| Component | Description |
|------------|-------------|
| **RVMYTH (CPU)** | A RISC-V microprocessor responsible for data handling and control |
| **PLL (Phase-Locked Loop)** | Generates and stabilizes the SoC’s clock signal |
| **DAC (10-bit)** | Converts digital data from RVMYTH into analog output |

### 🔄 Functional Flow

1. **Initialization** – PLL activates upon receiving the reference signal.  
2. **Data Processing** – RVMYTH updates the `r17` register with values.  
3. **Analog Output** – DAC converts digital data into an analog waveform for external output.

### 📸 VSDBabySoC Block Diagram
<img src="https://github.com/Devichinni20/VSDBabySoC_Week2/blob/b421c661a2a97e0a55f35acaef937e436df157e5/Theory_Part_1/Images/Screenshot%202025-10-01%20210517.png" alt="VSDBabySoC Block Diagram" width="700">

---

## ⏱️ Phase-Locked Loop (PLL)

A **Phase-Locked Loop (PLL)** synchronizes an internal oscillator’s output frequency with a reference input signal to maintain a constant phase relationship.

### 🔧 Key Components

- **Phase Detector (PD)** – Compares input & output phase  
- **Loop Filter (LF)** – Smoothens the phase error signal  
- **Voltage-Controlled Oscillator (VCO)** – Adjusts frequency to maintain lock

### 📸 PLL Block Diagram
<img src="https://github.com/Devichinni20/VSDBabySoC_Week2/blob/b421c661a2a97e0a55f35acaef937e436df157e5/Theory_Part_1/Images/Screenshot%202025-10-01%20211339.png" alt="PLL Block Diagram" width="700">

### ⚙️ Why PLLs Are Crucial in SoCs

| Challenge | Solution via PLL |
|------------|------------------|
| Off-chip clock delay | On-chip synchronization |
| Clock jitter | Stable frequency generation |
| Multiple frequency domains | Frequency multiplication/division |
| Crystal deviation | Auto-correction of drift |
| Temperature drift | Frequency stability through feedback |

---

## 🎚️ Digital-to-Analog Converter (DAC)

A **DAC** transforms digital (binary) signals into analog voltage or current — enabling SoCs to communicate with analog systems.

### 🔍 Types of DACs

1. **Weighted Resistor DAC**  
   Converts digital input using resistors of different values.  
   <img src="https://github.com/Devichinni20/VSDBabySoC_Week2/blob/b421c661a2a97e0a55f35acaef937e436df157e5/Theory_Part_1/Images/Screenshot%202025-10-01%20222017.png" alt="Weighted Resistor DAC" width="600">

2. **R-2R Ladder DAC**  
   Simplifies design using repeating resistor structures.  
   <img src="https://github.com/Devichinni20/VSDBabySoC_Week2/blob/b421c661a2a97e0a55f35acaef937e436df157e5/Theory_Part_1/Images/Screenshot%202025-10-01%20222210.png" alt="R-2R Ladder DAC" width="600">

### 🧩 In VSDBabySoC

- **10-bit DAC** allows 1024 discrete analog levels.  
- It converts RVMYTH register data (`r17`) into a continuous analog waveform.  
- The generated analog output can drive **audio/video** devices like TVs or speakers.

---

## 🧾 Key Takeaways

✅ **Open-Source Hardware** — Built using RISC-V and Sky130 PDK  
✅ **Compact Design** — Combines CPU, PLL, and DAC in one chip  
✅ **Educational Purpose** — Ideal for digital-analog interface experiments  
✅ **Practical Outputs** — Capable of generating audio/video analog signals  

---

## 📂 Repository Structure

```bash
VSDBabySoC/
│
├── docs/                  # Documentation and images
├── rtl/                   # Verilog source files
├── sim/                   # Testbench and simulation scripts
├── synth/                 # Synthesis setup
├── results/               # Waveforms and output data
├── LICENSE
└── README.md
