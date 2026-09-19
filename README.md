# Three-Phase Inverter

![Power Electronics](https://img.shields.io/badge/Domain-Power_Electronics-FF6F00?style=for-the-badge)
![Three-Phase Inverter](https://img.shields.io/badge/Topology-Three_Phase_Inverter-009999?style=for-the-badge)
![VSI](https://img.shields.io/badge/Topology-VSI_Voltage_Source-4B0082?style=for-the-badge)
![SPWM](https://img.shields.io/badge/Modulation-SPWM_%2F_Six_Step-00599C?style=for-the-badge)
![Hardware Verified](https://img.shields.io/badge/Status-Hardware_Verified-28A745?style=for-the-badge)
![License](https://img.shields.io/badge/License-MIT-blue?style=for-the-badge)

## Executive Overview
Heavy industrial machinery, from traction motors to conveyor systems, relies overwhelmingly on robust three-phase induction and synchronous motors. This project details the theoretical engineering and physical prototyping of a **Three-Phase Voltage Source Inverter (VSI)**. By synthesizing three alternating AC voltages displaced precisely by $120^\circ$ electrical degrees from a single DC power source, this inverter topology forms the foundational core of Variable Frequency Drives (VFDs) and grid-tied renewable energy systems.

> [!CAUTION]
> **High Voltage DC-Link & Shoot-Through Hazard**
> Inverter topologies utilize a massive DC-Link bus backed by high-capacity, low-ESR capacitors. These capacitors hold a lethal electrical charge long after the power is disconnected; standard discharge protocols (bleeder resistors) must be followed. Furthermore, the 6-switch bridge is highly susceptible to **Shoot-Through (Cross-Conduction)**. If a high-side and low-side transistor on the same phase leg activate simultaneously due to improper dead-time programming or driver failure, it will create a dead short across the DC-Link, instantly destroying the silicon and potentially vaporizing the copper traces.

## System Highlights
- **Three-Phase AC Voltage Synthesis**: Generates Phase U, V, and W waveforms precisely displaced by $120^\circ$ from a singular DC input.
- **6-Switch Bridge Topology**: Employs a classical three-leg transistor matrix (MOSFETs or IGBTs) for four-quadrant bidirectional power flow.
- **Floating Gate Driver Stages**: Utilizes specialized half-bridge bootstrap driver ICs to successfully trigger the high-side N-channel transistors floating above ground potential.
- **Shoot-Through Interlocks**: Implements strict hardware-level dead-time delays to prevent concurrent high/low leg conduction.

## System Architecture Diagram

mermaid
flowchart TD
    DC["DC Power Source / DC-Link Bus V_dc"] --> CAP["Bulk DC-Link Decoupling Capacitors"]
    
    subgraph Three-Phase VSI 6-Switch Bridge
        CAP --> LEGA["Leg A \nHigh/Low Switches"]
        CAP --> LEGB["Leg B \nHigh/Low Switches"]
        CAP --> LEGC["Leg C \nHigh/Low Switches"]
    end
    
    CTRL["Gate Drive & Dead-Time Logic"] -.-> LEGA
    CTRL -.-> LEGB
    CTRL -.-> LEGC
    
    LEGA --> U["Phase U Terminal"]
    LEGB --> V["Phase V Terminal"]
    LEGC --> W["Phase W Terminal"]
    
    U --> MOTOR["3-Phase Inductive Motor / R-L Load"]
    V --> MOTOR
    W --> MOTOR


## Theoretical & Mathematical Models

### 1. Six-Step ($180^\circ$ Conduction) RMS Voltages
In fundamental six-step mode, each switch conducts for $180^\circ$. The resulting fundamental RMS output voltages relative to the DC-link voltage ($V_{"dc"}$) are:
**Line-to-Line RMS**:
$$V_{"LL,rms"} = \sqrt{"\frac{2"}{3}} V_{"dc"} \approx 0.8165 V_{"dc"}$$
**Phase-to-Neutral RMS** (assuming a balanced Y-connected load):
$$V_{"phase,rms"} = \frac{"\sqrt{2"}}{3} V_{"dc"} \approx 0.4714 V_{"dc"}$$

### 2. Sinusoidal Pulse-Width Modulation (SPWM)
By comparing a low-frequency sinusoidal reference ($\hat{"V"}_{"control"}$) with a high-frequency triangular carrier ($\hat{"V"}_{"tri"}$), the inverter can synthesize a true sine wave fundamental. In the linear modulation region ($m_a \le 1$):
$$V_{"LL,1(rms)"} = \frac{"\sqrt{3"}}{2\sqrt{"2"}} m_a V_{"dc"} \approx 0.612 m_a V_{"dc"}$$
*(Where $m_a = \frac{"\hat{V"}_{"control"}}{\hat{"V"}_{"tri"}}$ is the amplitude modulation index).*

### 3. Dead-Time Timing Inequality (Shoot-Through Prevention)
To ensure the low-side switch is completely off before the high-side switch turns on (and vice-versa), the programmed dead-time ($t_{"dead"}$) must satisfy:
$$t_{"dead"} \ge t_{"off(max)"} - t_{"on(min)"} + t_{"prop\_skew"}$$

### 4. Three-Phase Instantaneous Balanced Power
Unlike single-phase systems where power pulsates at $2\omega$, a balanced three-phase inverter delivers constant instantaneous power to the motor, resulting in zero torque ripple at the fundamental frequency:
$$p(t) = v_a(t)i_a(t) + v_b(t)i_b(t) + v_c(t)i_c(t) = 3 V_{"ph"} I_{"ph"} \cos(\phi) = \text{"Constant"}$$

## Hardware Bill of Materials (BOM)
| Component | Function |
| :--- | :--- |
| **Power Switching Transistors** | 6x Power MOSFETs or IGBTs forming the three-phase bridge |
| **Half-Bridge Gate Drivers** | 3x driver ICs (e.g., IR2104 or IR2110) for high/low side logic translation |
| **Bootstrap Circuitry** | 3x Ultra-fast recovery diodes and bootstrap capacitors for floating high-side gate drive |
| **DC-Link Capacitors** | Low-ESR electrolytic capacitors to decouple the DC bus and source high transient ripple currents |
| **Snubber Networks** | RC networks across transistors to suppress inductive turn-off voltage spikes ($dv/dt$) |

## Six-Step Switching Logic State Matrix
In classical $180^\circ$ conduction, there are 6 distinct active states (excluding the 2 zero-vectors). Each state lasts for $60^\circ$ electrical.
| State | Conducting Switches (H=High, L=Low) | Phase A | Phase B | Phase C |
| :---: | :--- | :---: | :---: | :---: |
| **1** | A(H), B(L), C(L) | $+2/3 V_{"dc"}$ | $-1/3 V_{"dc"}$ | $-1/3 V_{"dc"}$ |
| **2** | A(H), B(H), C(L) | $+1/3 V_{"dc"}$ | $+1/3 V_{"dc"}$ | $-2/3 V_{"dc"}$ |
| **3** | A(L), B(H), C(L) | $-1/3 V_{"dc"}$ | $+2/3 V_{"dc"}$ | $-1/3 V_{"dc"}$ |
| **4** | A(L), B(H), C(H) | $-2/3 V_{"dc"}$ | $+1/3 V_{"dc"}$ | $+1/3 V_{"dc"}$ |
| **5** | A(L), B(L), C(H) | $-1/3 V_{"dc"}$ | $-1/3 V_{"dc"}$ | $+2/3 V_{"dc"}$ |
| **6** | A(H), B(L), C(H) | $+1/3 V_{"dc"}$ | $-2/3 V_{"dc"}$ | $+1/3 V_{"dc"}$ |

## Authentic Artifacts Catalog
- **Engineering Reports**: [`docs/Model of 3 phase Inverter _ حسن مقبل .pdf`](docs/)
- **Simulation Waveforms & Prototype Photos**: Located in ["`docs/images/`"](docs/images/) as **[ORIGINAL SCHEMATIC & HARDWARE ARTIFACTS]**.

## Engineering Audit & Tradeoffs
- **Six-Step vs. SPWM/SVPWM**: Six-step inversion requires very slow switching frequencies (e.g., $50\text{"Hz"}$), resulting in negligible switching losses ($P_{"sw"}$). However, it generates massive low-order harmonic distortion ($5^{th}$ and $7^{th}$ harmonics), which causes severe acoustic noise and torque pulsation in motors. Transitioning to SPWM or Space Vector PWM (SVPWM) pushes harmonics to the high-frequency switching carrier (e.g., $10\text{"kHz"}$), easily filtered by the motor inductance, but significantly increases silicon heating due to dynamic switching losses.
- **Filter Requirements**: When driving sensitive loads or complying with grid-tie EMI standards, a bulky and expensive 3-phase LC low-pass filter must be cascaded at the inverter output to reconstruct a pure sinusoidal waveform.

---

**Hassan Moqbel Morshed Ghaleb**
Mechatronics Engineer | Mechanical Design & CAD (SolidWorks & AutoCAD) | Preventive Maintenance & Electromechanical Systems | Industrial Automation, Control Systems, Robotics & Intelligent Machines | CAD/FEA, Embedded Systems, Python & C++
[GitHub](https://github.com/Hassan-Moqbel) · [Facebook](https://www.facebook.com/share/1BqxAgVjHi/) · [LinkedIn](https://www.linkedin.com/in/hassan-moqbel)

## License
This project is licensed under the ["MIT License"](LICENSE).
