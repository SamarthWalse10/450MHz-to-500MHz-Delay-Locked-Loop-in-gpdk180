# 450MHz-to-500MHz-Delay-Locked-Loop-in-gpdk180

Cadence Virtuoso implementation of a 450–500 MHz Delay-Locked Loop (DLL) in gpdk180, achieving < 25ps static phase offset across all extreme PVT corners.

## Project Overview
This repository contains the full-custom transistor-level design and simulation documentation for a Delay-Locked Loop (DLL). Developed as part of the EE698G Circuit Design for Frequency and Phase Synthesis coursework at IIT Kanpur, the DLL is implemented in the gpdk180 pdk.

The system is optimized to precisely lock to reference frequencies between 450 MHz and 500 MHz across all Process, Voltage, and Temperature (PVT) corners while maintaining a critically damped transient response.

## Design Specifications
The circuit was designed to meet the following parameters:
* **Reference Frequency Range**: 450 MHz to 500 MHz.
* **Supply Voltage ($V_{DD}$)**: 1.8V nominal (Tested at 1.71V to 1.89V).
* **PVT Corners**: Evaluated across Fast-Fast (0°C, 1.89V), Nominal (25°C, 1.8V), and Slow-Slow (70°C, 1.71V) extremes.
* **Static Phase Offset (SPO)**: Strictly $< 25 ps$ under all conditions.
* **Charge Pump Current ($I_{CP}$)**: $20 \mu A$.

---

## System Architecture

The DLL architecture consists of three primary custom-designed sub-blocks operating in a closed negative-feedback loop:

### 1. Voltage-Controlled Delay Line (VCDL)
* **Architecture**: A 20-stage Current-Starved Inverter chain.
* **Design Strategy**: Designed without explicit passive capacitors. Device sizing is optimized such that the intrinsic input gate capacitance of the subsequent stage acts as the primary load.
* **Performance**: Achieves a monotonically decreasing delay profile covering a 2.0 ns to 2.22 ns target range as the control voltage sweeps from 400 mV to 1.4 V.

### 2. Phase Frequency Detector (PFD)
* **Architecture**: Utilizes a highly optimized True Single-Phase Clock (TSPC) D-Flip-Flop topology.
* **Design Strategy**: Upgraded from a standard Master-Slave D-Flip-Flop to resolve severe internal propagation delay bottlenecks. A custom memory-wipe NMOS was integrated to prevent "ghost pulse" asynchronous reset errors inherent to dynamic logic. Asymmetric transistor stacking ensures rapid D-to-Q propagation while minimizing load on the reference clock.

### 3. Source-Switched Charge Pump (CP) & Loop Filter
* **Architecture**: Regulated source-switched topology designed via $g_m/I_D$ methodology to deliver a highly precise $20 \mu A$ current.
* **Design Strategy**: An internal operational amplifier actively tracks the control voltage at intermediate nodes to suppress channel-length modulation, ensuring perfect UP/DN current matching.
* **Loop Filter**: A $5 pF$ capacitor was utilized to deliberately restrict the theoretical loop bandwidth to $< 1.2 MHz$, effectively suppressing discrete-time sampling effects and ensuring a critically damped transient settling without ringing.

---

## 📘 Detailed Mathematical Calculations & Sizing
> For in-depth theoretical calculations, transistor sizing parameters, VCDL gain extraction ($K_{vcdl}$), closed-loop bandwidth ($f_{bw}$) derivations, and divergence analysis, **please refer to the attached Project Report PDF (`ProjectDLL_SamarthWalse_251040092.pdf`) included in this repository.**

---

## Simulation Results (Virtuoso ADE L)
The DLL was rigorously simulated to ensure robust locking across the extreme PVT corners.

* **Tracking Bandwidth**: Achieved a closed-loop bandwidth of 1.107 MHz in the Fast corner and 0.936 MHz in the Slow corner, successfully suppressing PFD sampling ripple.
* **Static Phase Offset (SPO)**: The custom TSPC PFD and regulated Charge Pump minimized dynamic non-idealities, resulting in an SPO of just 3.09 ps (Fast corner) and 6.69 ps (Slow corner)—well below the 25 ps requirement.
* **Steady-State Power**: Consumes 5.01 mW (Fast corner) and 13.67 mW (Slow corner). *(Note: The SS corner consumes more power because it requires a significantly higher control voltage to force the inherently slow delay elements to meet the strict 2.0 ns target).*
* **Transient Stability**: Both minimum and maximum frequency bounds demonstrated critically damped, ringing-free settling to the locked state.

---

## Result Imagery

* **VCDL Single Stage & Full Architecture Schematic:**
<img width="899" height="941" alt="vcdu_schematic" src="https://github.com/user-attachments/assets/e18fa4d5-6896-4645-a717-8bf06a8a3ea9" />
<img width="2370" height="596" alt="vcdl_schematic" src="https://github.com/user-attachments/assets/180b01cd-b3d4-43bd-92cc-52acbb5b82a0" />
&nbsp;

* **TSPC PFD Architecture:**
<img width="2312" height="992" alt="dff_TSPC_schematic" src="https://github.com/user-attachments/assets/c50f3d83-5e2e-4f14-bcbe-fbf90aa4cdf1" />
<img width="2328" height="971" alt="pfd_TSPC_schematic" src="https://github.com/user-attachments/assets/3d30c722-353d-4bb8-a187-13072eedef74" />
&nbsp;

* **Source-Switched Charge Pump:**
<img width="1719" height="981" alt="qpump_srcSwitch_schematic" src="https://github.com/user-attachments/assets/36ba7bc1-bca2-414f-a3ce-e2cea6c8bb26" />
&nbsp;

* **VCDL Delay, Gain, and Power Characteristics:**
<img width="1910" height="654" alt="TpLH vs Vc for PVT Corners" src="https://github.com/user-attachments/assets/6c71bdbb-3c7e-442b-a34d-cdb2eeb4435f" />
<img width="1910" height="653" alt="Kdl vs Vc for PVT Corners" src="https://github.com/user-attachments/assets/713cf01f-14d1-4d7c-9516-369e7f564743" />
<img width="1910" height="654" alt="Pdl vs Vc for PVT Corners" src="https://github.com/user-attachments/assets/91b49e24-3953-49c2-8975-2fc6ddee0aa7" />
&nbsp;

* **Fast Corner Locking Transient (450 MHz, 0°C, 1.89V):**
<img width="1910" height="654" alt="Transient Analysis Settling State FF" src="https://github.com/user-attachments/assets/cf97b004-6a63-41b7-9ae1-d8756e754ba3" />
&nbsp;

* **Fast Corner Steady-State SPO:**
<img width="1910" height="654" alt="Transient Analysis Steady State FF" src="https://github.com/user-attachments/assets/3591cd39-95f7-4723-9a50-71e5b692c90d" />
&nbsp;

* **Slow Corner Locking Transient (500 MHz, 70°C, 1.71V):**
<img width="1910" height="654" alt="Transient Analysis Settling State SS" src="https://github.com/user-attachments/assets/cee45839-41b3-46f5-8a3d-4573ffe259f4" />
&nbsp;

* **Slow Corner Steady-State SPO:**
<img width="1910" height="654" alt="Transient Analysis Steady State SS" src="https://github.com/user-attachments/assets/f1280531-6c4e-4a17-8ce8-d80ce85f71c8" />
&nbsp;
