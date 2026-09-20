# Three-Phase DC-AC Voltage Source Inverter (VSI) Simulation & Harmonic Analysis

[![Domain: Power Electronics](https://img.shields.io/badge/Domain-Power%20Electronics%20%7C%20Inverters-darkblue.svg)](#theoretical--mathematical-models)
[![Simulation: MATLAB Simulink](https://img.shields.io/badge/Simulation-MATLAB%20%7C%20Simulink%20Simscape-red.svg)](#simulation-environment--control-architecture)
[![Modulation: SPWM & Six-Step](https://img.shields.io/badge/Modulation-SPWM%20%26%20180%C2%B0%20Conduction-orange.svg)](#switching-topologies--modulation-strategies)
[![Academic: Sana'a University](https://img.shields.io/badge/Origin-Sana'a%20University%20Mechatronics-green.svg)](#academic-provenance--project-context)
[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)

A rigorous power electronics modeling and harmonic analysis study of a two-level, three-phase Voltage Source Inverter (VSI) feeding balanced dynamic loads. Designed, modeled, and evaluated in MATLAB/Simulink, this project investigates semiconductor switching profiles, conduction topologies (180° vs 120° vs SPWM), line and phase voltage Fourier decompositions, triplen harmonic cancellations, and total harmonic distortion (THD) characteristics.

---

## Academic Provenance & Project Context

This research and simulation project was formally developed within the **Department of Mechatronics Engineering, Faculty of Engineering at Sana'a University**:

- **Academic Institution:** Sana'a University — Faculty of Engineering
- **Engineering Discipline:** Mechatronics Engineering & Industrial Automation
- **Course Focus:** Power Electronics & Motor Drive Systems
- **Academic Mentorship:** Supervised by **Dr. Radwan Al-Budhaji** & **Eng. Amjed Al-Shagthah**
- **Lead Engineering Contributor:** Hassan Moqbel Morshed Ghaleb

---

## Executive Overview & Power Conversion KPIs

Three-phase DC-AC inverters serve as the vital conversion backbone for variable-frequency drives (VFDs), grid-tied renewable energy systems (photovoltaic and wind generation), and uninterrupted power supplies (UPS). Achieving optimal power conversion requires balancing power factor, switching losses, semiconductor thermal boundaries, and harmonic content.

---

## System Architecture & Three-Phase Bridge Topology

The converter relies on a classical six-switch, three-phase bridge structure utilizing insulated-gate bipolar transistors (IGBTs) or power MOSFETs, each paralleled with an anti-parallel ultrafast freewheeling diode (FWD) to provide inductive energy recirculation paths.

```mermaid
flowchart TD
    subgraph DC_Stage["DC Energy Bus"]
        VDC["DC Voltage Source (V_dc)"]
        CAP["DC-Link Bulk Decoupling Capacitor"]
    end

    subgraph Bridge["Three-Phase Two-Level Inverter Bridge"]
        subgraph LegA["Phase Leg A"]
            S1["Upper Switch S1 (0°)"]
            S4["Lower Switch S4 (180°)"]
        end
        subgraph LegB["Phase Leg B"]
            S3["Upper Switch S3 (120°)"]
            S6["Lower Switch S6 (300°)"]
        end
        subgraph LegC["Phase Leg C"]
            S5["Upper Switch S5 (240°)"]
            S2["Lower Switch S2 (60°)"]
        end
    end

    subgraph Load["Balanced Three-Phase Load"]
        LOAD_A["Phase Load A (R-L)"]
        LOAD_B["Phase Load B (R-L)"]
        LOAD_C["Phase Load C (R-L)"]
        NEUTRAL["Fictitious Star Neutral (n)"]
    end

    VDC --> CAP
    CAP --> LegA
    CAP --> LegB
    CAP --> LegC
    S1 --- S4
    S3 --- S6
    S5 --- S2
    LegA -->|"Phase Out A"| LOAD_A
    LegB -->|"Phase Out B"| LOAD_B
    LegC -->|"Phase Out C"| LOAD_C
    LOAD_A --- NEUTRAL
    LOAD_B --- NEUTRAL
    LOAD_C --- NEUTRAL
```

---

## Theoretical & Mathematical Models

### 1. Six-Step (180° Conduction) Phase and Line Voltages
In a perfectly balanced 180° conduction star (Y) connected load, the relationship between the fundamental Line-to-Line ($V_{LL}$) and Line-to-Neutral ($V_{LN}$) voltages relies strictly on the DC-link voltage ($V_{dc}$):

$$ V_{LL} = \sqrt{3} \times V_{LN} $$

### 2. Three-Phase Instantaneous Balanced Power
Using the classical equations for a balanced three-phase system, we can derive the Real, Reactive, and Apparent Power metrics required for thermal and load engineering.

- **Active Power (Watts):** The true real power doing physical work.
  $$ P = \sqrt{3} \times V_{LL} \times I_L \times \cos(\phi) $$

- **Reactive Power (VARs):** The oscillatory magnetic field power.
  $$ Q = \sqrt{3} \times V_{LL} \times I_L \times \sin(\phi) $$

- **Apparent Power (VA):** The total geometric vector power supplied.
  $$ S = \sqrt{3} \times V_{LL} \times I_L $$

- **Power Factor (pf):** The efficiency ratio of the delivery pipeline.
  $$ p_f = \cos(\phi) = \frac{P}{S} $$

*(Where $I_L$ represents the Line Current and $\phi$ represents the phase angle differential between current and voltage).*

---

## Engineering Documents & Simulation Resources
The full academic and engineering analysis, complete with harmonic Fourier breakdowns, is documented in the central repository:
* 📄 **[Three Phase Inverter Engineering Report (PDF)](docs/Three_Phase_Inverter_Engineering_Report.pdf)**
* 📄 **[Three Phase Inverter Engineering Report (DOCX)](docs/Three_Phase_Inverter_Engineering_Report.docx)**

---

**Hassan Moqbel Morshed Ghaleb**  
Mechatronics Engineer | Mechanical Design & CAD (SolidWorks & AutoCAD) | Preventive Maintenance & Electromechanical Systems | Industrial Automation, Control Systems, Robotics & Intelligent Machines | CAD/FEA, Embedded Systems, Python & C++  
[GitHub](https://github.com/Hassan-Moqbel) · [Facebook](https://www.facebook.com/share/1BqxAgVjHi/) · [LinkedIn](https://www.linkedin.com/in/hassan-moqbel)

## License
This project is licensed under the [MIT License](LICENSE).
