# Buck Converter — 10V → 2–8V @ 1A  
**Power Electronics Course Project · March – April 2026**

This project presents the complete design, simulation, and hardware validation of a PWM-controlled DC-DC Buck Converter capable of stepping down a 10V input supply to a regulated output voltage between 2V and 8V at load currents up to 1A.

The converter was implemented using the TL494 PWM controller, TC4428A gate driver, and IRFZ44N MOSFET, 1mH inductor and 220uF capacitor. Analytical derivations were performed to determine CCM/DCM boundary conditions, critical load resistance, and switching frequency requirements. Theoretical predictions were validated through MATLAB Simulink simulations and experimental hardware testing.

The converter achieved approximately 8 mV peak-to-peak output ripple at 20 kHz and a peak efficiency of 94% at rated load conditions.

---

## Key Specifications

| Parameter | Value |
|---|---|
| Input Voltage (Vᵢₙ) | 10 V |
| Output Voltage Range (Vₒᵤₜ) | 2 V – 8 V (variable duty cycle) |
| Maximum Output Current | 1 A |
| Duty Cycle Range (D) | 0.2 – 0.8 |
| Switching Frequencies Tested | 800 Hz (DCM) · 20 kHz (CCM) |
| Output Voltage Ripple (simulation) | 8 mV @ 20 kHz |
| Measured Efficiency | **94% @ 1A output** |
| Build Medium | Perfboard (through-hole) |

---

## Repository Structure

```
buck-converter/
│
├── README.md
│
├── docs/
│   ├── derivations/
│   │   ├── volt_second_balance.md      ← Steady-state duty cycle derivation
│   │   ├── ccm_dcm_boundary.md         ← Critical resistance & frequency analysis
│   │   └── ripple_calculations.md      ← ΔiL and ΔVo derivations
│   ├── hardware/
│   │   ├── component_selection.md      ← TL494, TC4428A, IRFZ44N rationale
│   │   └── gate_driver_design.md       ← TC4428A drive chain notes
│   └── images/
│       ├── schematics/                 ← Circuit schematic
│       ├── waveforms/                  ← Simulation & oscilloscope captures
│       └── hardware_photos/            ← Perfboard photos
│
├── simulations/
│   └── simulink/                       ← MATLAB Simulink model (.slx)
│
└── assets/
    └── datasheets/                     ← TL494, TC4428A, IRFZ44N datasheets
```

---

## Core Design Theory

### 1. Volt-Second Balance → Duty Cycle

In steady-state CCM, average inductor voltage = 0:

$$D = \frac{V_{out}}{V_{in}}$$

For this design's output range:

$$D_{min} = \frac{2}{10} = 0.2 \qquad D_{max} = \frac{8}{10} = 0.8$$

---

### 2. Inductor Current Ripple

$$\Delta i_L = \frac{V_{out}(1 - D)}{f_s \cdot L}$$

---

### 3. CCM/DCM Boundary — Critical Load Resistance

The converter enters DCM when the load draws less current than half the peak-to-peak inductor ripple. The **critical load resistance** $R_{crit}$ defines this boundary:

$$R_{crit}(D) = \frac{2L}{(1-D) \cdot T_s} = \frac{2Lf_s}{1-D}$$

- For $R_{load} < R_{crit}$: **CCM** (inductor current never reaches zero)
- For $R_{load} > R_{crit}$: **DCM** (inductor current touches zero each cycle)

This was derived analytically **across the full duty cycle range (D = 0.2 to 0.8)**, producing a $R_{crit}$ vs. $D$ curve that defines the operating mode boundary for any load condition.

---

### 4. Minimum Switching Frequency for DCM at a Given Load

For a fixed load resistance (e.g., $R = 8\ \Omega$), DCM is guaranteed when:

$$f_s < \frac{R(1-D)}{2L}$$

This sets the **maximum switching frequency** that still ensures DCM at 8 Ω — used to select 800 Hz as the DCM test frequency.

> *Insert plot: R_crit vs. Duty Cycle across the D = 0.2–0.8 range*

![CCM/DCM Boundary Plot](docs/images/waveforms/ccm_dcm_boundary.png)

---

## Component Selection

| Component | Part | Key Reason |
|---|---|---|
| PWM Controller | **TL494** | Adjustable frequency & duty cycle, built-in dead-time control, widely available |
| Gate Driver | **TC4428A** | Dual non-inverting 1.5A peak drive; bridges TL494's limited output current to MOSFET gate |
| Power MOSFET | **IRFZ44N** | $V_{DS}$ = 60V, $I_D$ = 49A, $R_{DS(on)}$ = 28 mΩ — well within ratings at 10V/1A |
| Build | Perfboard | Accessible, suitable for <20 kHz prototype validation |

---

## Simulation & Hardware Results

### MATLAB Simulink — 20 kHz (CCM)

> *Insert Simulink schematic screenshot*

![Simulink Model](docs/images/waveforms/simulink_model.png)

- Output ripple: **8 mV** at 20 kHz, 1A load (matches analytical prediction)
- Inductor current waveform confirms continuous conduction

> *Insert inductor current and output voltage waveforms*

![Simulation Waveforms](docs/images/waveforms/simulink_waveforms.png)

---

### Hardware — 800 Hz (DCM Validation)

> *Insert oscilloscope capture showing inductor current touching zero*

![DCM Waveform — 800 Hz](docs/images/waveforms/hardware_dcm_800hz.png)

- Inductor current ramps to zero each cycle ✓ — confirms DCM prediction

---

### Hardware — 20 kHz (CCM Validation)

> *Insert oscilloscope capture showing continuous inductor current*

![CCM Waveform — 20 kHz](docs/images/waveforms/hardware_ccm_20khz.png)

- Efficiency measured: **94% at 1A output**

---

## Hardware Build

> *Insert perfboard photos*

![Perfboard Top View](docs/images/hardware_photos/perfboard_top.jpg)

---

## References

- Erickson & Maksimovic, *Fundamentals of Power Electronics*, 3rd Ed. — Ch. 2 (CCM/DCM Analysis)
- Texas Instruments, *TL494 Datasheet* (SLVS074)
- Microchip, *TC4428A Datasheet*
- Vishay, *IRFZ44N Datasheet*

---

## 👤 Author

**[Your Name]**  
Power Electronics Course · [Your University] · 2026  
[LinkedIn](https://linkedin.com/in/yourprofile) · [Email](mailto:you@example.com)
