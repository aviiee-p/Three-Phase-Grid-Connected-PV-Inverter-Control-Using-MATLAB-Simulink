# Three-Phase-Grid-Connected-PV-Inverter-Control-Using-MATLAB-Simulink

A MATLAB/Simulink model of a **108 kW two-stage grid-connected photovoltaic (PV) system** featuring:

- Perturb & Observe (P&O) Maximum Power Point Tracking (MPPT)
- DC-DC boost converter
- Three-phase Voltage Source Inverter (VSI)
- dq-frame current control
- Phase-Locked Loop (PLL) synchronization
- Sinusoidal Pulse Width Modulation (SPWM)
- LCL output filter
- Unity power factor operation
- Grid-current harmonic analysis

---

## 📌 Overview

This project presents the **modeling, simulation, and control of a 108 kW two-stage grid-connected photovoltaic system** developed in MATLAB/Simulink.

The system consists of a PV array connected to a **DC-DC boost converter**, which uses a **Perturb & Observe (P&O) MPPT algorithm** to extract maximum available power from the PV array. The boosted DC voltage is then supplied to a **three-phase Voltage Source Inverter (VSI)**, which converts the DC power into AC power suitable for grid injection.

The inverter is controlled using a **dq-reference-frame current controller**, while a **Phase-Locked Loop (PLL)** provides grid synchronization. An **LCL filter** is used at the inverter output to attenuate switching-frequency harmonics and improve the quality of the current injected into the grid.

The main objectives of the system are:

- Maximum power extraction from the PV array
- Stable DC-link voltage regulation
- Accurate active and reactive power control
- Unity power factor operation
- Low harmonic distortion
- Proper synchronization with the utility grid

---

# ⚙️ System Configuration

The overall system can be represented as:

**PV Array → DC-DC Boost Converter + P&O MPPT → DC Link → Three-Phase VSI → LCL Filter → Grid**

The major subsystems are described below.

---

## 1. PV Array Model

The PV array is modeled using a **single-diode equivalent circuit** to reproduce the electrical characteristics of the photovoltaic modules, including their I–V and P–V behavior under varying irradiance conditions.

### PV Array Specifications

| Parameter | Value |
|---|---:|
| PV Module | REC Solar REC215PE-BLK |
| Modules in Series | 11 |
| Modules in Parallel | 46 |
| Rated Power per Module | 215.08 W |
| Total Array Power | ≈ 108.8 kW |

The total installed PV power is calculated as:

$$
P_{PV} = 46 \times 11 \times 215.08
$$

$$
P_{PV} \approx 108.8\ \mathrm{kW}
$$

The array is subjected to varying irradiance conditions to evaluate the dynamic performance of the MPPT and grid-side control systems.

---

## 2. DC-DC Boost Converter and P&O MPPT

The DC-DC boost converter increases the PV-array voltage to the required DC-link voltage of approximately **600 V**.

For an ideal boost converter, the relationship between the input voltage, output voltage, and duty cycle is:

$$
V_{out} = \frac{V_{in}}{1-D}
$$

where:

- $V_{in}$ = input voltage
- $V_{out}$ = output voltage
- $D$ = converter duty cycle

### Perturb & Observe MPPT

The **Perturb & Observe (P&O) MPPT algorithm** continuously adjusts the converter operating point to track the maximum power point of the PV array.

The instantaneous PV power is calculated as:

$$
P(t) = V(t)I(t)
$$

The changes in power and voltage are calculated as:

$$
\Delta P = P(t) - P(t-1)
$$

$$
\Delta V = V(t) - V(t-1)
$$

The algorithm determines the direction of the next perturbation based on the signs of $\Delta P$ and $\Delta V$.

| Condition | Duty-Cycle Action |
|---|---|
| $\Delta P > 0$ and $\Delta V > 0$ | Increase duty cycle |
| $\Delta P > 0$ and $\Delta V < 0$ | Decrease duty cycle |
| $\Delta P < 0$ and $\Delta V > 0$ | Decrease duty cycle |
| $\Delta P < 0$ and $\Delta V < 0$ | Increase duty cycle |

This process is repeated continuously so that the PV operating point converges toward the **Maximum Power Point (MPP)**.

---

# 3. Three-Phase VSI with dq-Frame Control

The three-phase **Voltage Source Inverter (VSI)** converts the regulated DC-link voltage into three-phase AC power for grid injection.

The inverter current is controlled in the **synchronous dq reference frame**, allowing active and reactive power to be controlled independently.

### dq Current Control

With the reference frame synchronized to the grid voltage:

- **d-axis current ($i_d$)** primarily controls active power.
- **q-axis current ($i_q$)** primarily controls reactive power.

For unity power factor operation:

$$
i_q = 0
$$

The active-power reference determines the required d-axis current:

$$
i_d \propto P
$$

Thus, the controller adjusts $i_d$ according to the available PV power while maintaining $i_q$ close to zero.

This enables independent control of active and reactive power and allows the system to operate at approximately **unity power factor**.

---

## 4. Phase-Locked Loop (PLL)

A **Phase-Locked Loop (PLL)** is used to synchronize the inverter with the grid voltage.

The PLL tracks the grid voltage phase angle and provides the angular position required for the abc-to-dq and dq-to-abc transformations.

The estimated phase angle can be expressed as:

$$
\theta = \int \omega_{\mathrm{grid}}\,dt
$$

where:

- $\omega_{\mathrm{grid}}$ = grid angular frequency
- $\theta$ = estimated grid phase angle

The grid voltage is measured at the **Point of Common Coupling (PCC)**. The PLL uses this measurement to maintain synchronization between the inverter and the utility grid.

Proper synchronization is essential for accurate dq-frame control and unity power factor operation.

---

## 5. Current Loop Control

The inner current-control loop regulates the inverter output currents so that they accurately follow their respective reference values.

The controller generates the inverter voltage references required to minimize the current-tracking error.

The control objectives are:

- Track the reference d-axis current $i_d^*$
- Maintain the q-axis current $i_q^*$ near zero
- Regulate active power according to available PV power
- Minimize reactive power exchange with the grid
- Maintain synchronization with the grid voltage

The resulting control strategy enables stable power transfer from the PV array to the utility grid.

---

# 6. LCL Output Filter

An **LCL filter** is connected between the inverter and the grid to attenuate high-frequency switching harmonics produced by the PWM inverter.

The approximate resonance frequency of the filter is:

$$
f_{\mathrm{res}} =
\frac{1}{2\pi\sqrt{L_1L_2C_f}}
$$

where:

- $f_{\mathrm{res}}$ = LCL-filter resonance frequency
- $L_1$ = inverter-side inductance
- $L_2$ = grid-side inductance
- $C_f$ = filter capacitance

The LCL filter significantly reduces switching-frequency components in the injected grid current and improves overall power quality.

---

# 📊 Simulation Results

The system was evaluated under changing solar irradiance conditions to verify the performance of the MPPT, DC-link controller, PLL, dq current controller, and LCL filter.

---

## Irradiance Profile

![Irradiance Profile](Waveforms/Irradiation%20Profile.png)

A step-change irradiance profile is applied to evaluate the dynamic response of the complete system.

The irradiance changes from **1000 W/m² to 400 W/m²** and subsequently returns to **1000 W/m²**.

This allows the controller to be tested at multiple PV operating points.

---

## PV Array Power, Voltage, and Current

![PV Array Outputs](Waveforms/PV%20Array%20Outputs.png)

The PV array power, voltage, and current responses are shown for the applied irradiance profile.

The implemented **P&O MPPT algorithm successfully tracks the maximum power point** following changes in irradiance while maintaining stable operation with relatively small oscillations around the MPP.

As expected from the PV I–V and P–V characteristics, the array current changes significantly with irradiance, whereas the PV voltage remains comparatively stable.

---

## DC-Link Voltage

![DC-Link Voltage](Waveforms/DC%20Link%20Voltage.png)

The DC-link voltage is maintained close to its **600 V reference** despite significant changes in the available PV power.

The small voltage deviation during irradiance transitions demonstrates effective DC-link voltage regulation.

---

## Direct & Quadrature Axis Currents

![Direct and Quadrature Currents](Waveforms/Direct%20%26%20Quadrature%20Current%20of%20Injected%20Current.png)

The d-axis current $i_d$ follows the active-power demand, while the q-axis current $i_q$ is regulated close to zero.

This demonstrates effective decoupled active/reactive current control and confirms the system's ability to operate at approximately **unity power factor**.

---

## Three-Phase Grid Voltage and Current at the PCC

![Three-Phase Grid Voltage and Current](Waveforms/3%20Phase%20Grid%20Voltage%20%26%20Current%20at%20PCC.png)

The three-phase grid voltage and current waveforms are shown at the **Point of Common Coupling (PCC)**.

The grid voltage remains sinusoidal and essentially unchanged during irradiance variations, which is expected for a stiff-grid model.

The magnitude of the injected current changes according to the available PV power, demonstrating coordinated operation between:

- P&O MPPT
- DC-link voltage control
- dq-frame current control
- PLL synchronization

The PLL maintains synchronization with the grid voltage, enabling accurate active/reactive power control and unity power factor operation.

---

## Dynamic Phase Voltage & Current Response

![Phase Voltage and Current](Waveforms/Dynamic%20Response%20of%20Phase%20Current%20%26%20Voltage.png)

The phase current remains closely aligned with the corresponding grid voltage waveform.

The near-zero phase displacement between voltage and current demonstrates the **unity power factor operation** achieved by the control system.

---

## Total Harmonic Distortion of Grid Current

![Grid Current THD](Waveforms/THD%20of%20Grid%20Current%20at%20PCC.png)

The harmonic spectrum of the grid current results in a measured **THD of 2.34%**.

The obtained value is below the commonly referenced **5% current-distortion limit associated with IEEE 519**, indicating that the LCL filter and inverter control provide good current quality at the PCC.

---

# 🎯 Key Features

- **108.8 kW PV array**
- Two-stage grid-connected PV architecture
- P&O-based MPPT
- DC-DC boost converter
- 600 V DC-link
- Three-phase VSI
- dq-frame current control
- Grid-voltage PLL synchronization
- Unity power factor operation
- LCL output filter
- Dynamic irradiance testing
- Grid-current THD analysis
- **2.34% measured grid-current THD**

---

# 📌 Conclusion

The MATLAB/Simulink model demonstrates the complete control and grid integration of a **108.8 kW photovoltaic power system**.

The simulation results show that the proposed control architecture can track the PV maximum power point, maintain the DC-link voltage, regulate active and reactive current components, synchronize the inverter with the grid, and inject high-quality current with low harmonic distortion.

The obtained **2.34% grid-current THD** and near-unity power factor demonstrate the effectiveness of the combined **MPPT, dq-frame current control, PLL synchronization, and LCL filtering** approach.

---
