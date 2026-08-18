# 450MHz-to-500MHz-Delay-Locked-Loop-in-gpdk180

Cadence Virtuoso implementation of a 450–500 MHz Delay-Locked Loop (DLL) in gpdk180, achieving < 25ps static phase offset across all extreme PVT corners[cite: 7, 8].

## Project Overview
This repository contains the full-custom transistor-level design and simulation documentation for a Delay-Locked Loop (DLL)[cite: 7]. Developed as part of the EE698G Circuit Design for Frequency and Phase Synthesis coursework at IIT Kanpur, the DLL is implemented in the gpdk180 pdk[cite: 7, 8].

The system is optimized to precisely lock to reference frequencies between 450 MHz and 500 MHz across all Process, Voltage, and Temperature (PVT) corners while maintaining a critically damped transient response[cite: 7, 8].

## Design Specifications
The circuit was designed to meet the following parameters:
* **Reference Frequency Range**: 450 MHz to 500 MHz[cite: 7].
* **Supply Voltage ($V_{DD}$)**: 1.8V nominal (Tested at 1.71V to 1.89V)[cite: 7].
* **PVT Corners**: Evaluated across Fast-Fast (0°C, 1.89V), Nominal (25°C, 1.8V), and Slow-Slow (70°C, 1.71V) extremes[cite: 7].
* **Static Phase Offset (SPO)**: Strictly $< 25 ps$ under all conditions[cite: 7].
* **Charge Pump Current ($I_{CP}$)**: $20 \mu A$[cite: 7].

---

## System Architecture

The DLL architecture consists of three primary custom-designed sub-blocks operating in a closed negative-feedback loop:

### 1. Voltage-Controlled Delay Line (VCDL)
* **Architecture**: A 20-stage Current-Starved Inverter chain[cite: 8].
* **Design Strategy**: Designed without explicit passive capacitors[cite: 8]. Device sizing is optimized such that the intrinsic input gate capacitance of the subsequent stage acts as the primary load[cite: 8].
* **Performance**: Achieves a monotonically decreasing delay profile covering a 2.0 ns to 2.22 ns target range as the control voltage sweeps from 400 mV to 1.4 V[cite: 8].

### 2. Phase Frequency Detector (PFD)
* **Architecture**: Utilizes a highly optimized True Single-Phase Clock (TSPC) D-Flip-Flop topology[cite: 8].
* **Design Strategy**: Upgraded from a standard Master-Slave D-Flip-Flop to resolve severe internal propagation delay bottlenecks[cite: 8]. A custom memory-wipe NMOS was integrated to prevent "ghost pulse" asynchronous reset errors inherent to dynamic logic[cite: 8]. Asymmetric transistor stacking ensures rapid D-to-Q propagation while minimizing load on the reference clock[cite: 8].

### 3. Source-Switched Charge Pump (CP) & Loop Filter
* **Architecture**: Regulated source-switched topology designed via $g_m/I_D$ methodology to deliver a highly precise $20 \mu A$ current[cite: 8].
* **Design Strategy**: An internal operational amplifier actively tracks the control voltage at intermediate nodes to suppress channel-length modulation, ensuring perfect UP/DN current matching[cite: 8].
* **Loop Filter**: A $5 pF$ capacitor was utilized to deliberately restrict the theoretical loop bandwidth to $< 1.2 MHz$, effectively suppressing discrete-time sampling effects and ensuring a critically damped transient settling without ringing[cite: 8].

---

## 📘 Detailed Mathematical Calculations & Sizing
> For in-depth theoretical calculations, transistor sizing parameters, VCDL gain extraction ($K_{vcdl}$), closed-loop bandwidth ($f_{bw}$) derivations, and divergence analysis, **please refer to the attached Project Report PDF (`ProjectDLL_SamarthWalse_251040092.pdf`) included in this repository.**

---

## Simulation Results (Virtuoso ADE L)
The DLL was rigorously simulated to ensure robust locking across the extreme PVT corners.

* **Tracking Bandwidth**: Achieved a closed-loop bandwidth of 1.107 MHz in the Fast corner and 0.936 MHz in the Slow corner, successfully suppressing PFD sampling ripple[cite: 8].
* **Static Phase Offset (SPO)**: The custom TSPC PFD and regulated Charge Pump minimized dynamic non-idealities, resulting in an SPO of just 3.09 ps (Fast corner) and 6.69 ps (Slow corner)—well below the 25 ps requirement[cite: 8].
* **Steady-State Power**: Consumes 5.01 mW (Fast corner) and 13.67 mW (Slow corner)[cite: 8]. *(Note: The SS corner consumes more power because it requires a significantly higher control voltage to force the inherently slow delay elements to meet the strict 2.0 ns target[cite: 8]).*
* **Transient Stability**: Both minimum and maximum frequency bounds demonstrated critically damped, ringing-free settling to the locked state[cite: 8].

---

## Result Imagery

* **VCDL Single Stage & Full Architecture Schematic:**
<img width="1910" height="654" alt="vcdl_schematic" src="https://github.com/user-attachments/assets/placeholder-link-1" />
&nbsp;

* **TSPC PFD Architecture:**
<img width="1910" height="654" alt="pfd_schematic" src="https://github.com/user-attachments/assets/placeholder-link-2" />
&nbsp;

* **Source-Switched Charge Pump:**
<img width="1910" height="654" alt="charge_pump_schematic" src="https://github.com/user-attachments/assets/placeholder-link-3" />
&nbsp;

* **VCDL Delay, Gain, and Power Characteristics:**
<img width="1910" height="654" alt="vcdl_characteristics" src="https://github.com/user-attachments/assets/placeholder-link-4" />
&nbsp;

* **Fast Corner Locking Transient (450 MHz, 0°C, 1.89V):**
<img width="1910" height="654" alt="fast_transient_settling" src="https://github.com/user-attachments/assets/placeholder-link-5" />
&nbsp;

* **Fast Corner Steady-State SPO:**
<img width="1910" height="654" alt="fast_spo" src="https://github.com/user-attachments/assets/placeholder-link-6" />
&nbsp;

* **Slow Corner Locking Transient (500 MHz, 70°C, 1.71V):**
<img width="1910" height="654" alt="slow_transient_settling" src="https://github.com/user-attachments/assets/placeholder-link-7" />
&nbsp;

* **Slow Corner Steady-State SPO:**
<img width="1910" height="654" alt="slow_spo" src="https://github.com/user-attachments/assets/placeholder-link-8" />
&nbsp;
