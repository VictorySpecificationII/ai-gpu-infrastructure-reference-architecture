# Appendix B — Thermal Physics and Digital Twin Basis (with Full Derivations)

## B.1 Purpose

This appendix closes the thermodynamic and control loop for the **single witness workload** defined in Appendix F:

- $P_{IT} \approx 4.77\ \text{MW}$
- $\dot Q \approx P_{IT}$ (all IT power becomes heat, first order)
- Design liquid rise: $\Delta T = 12^\circ\text{C}$ (envelope 8–15 °C)
- Mechanical zoning: $1\ \text{MW}$ maximum fault domain per zone

It produces:

1. Required coolant flow $\dot m,\ \dot V$ at the pod boundary
2. Per-zone flow targets
3. Pumping power order-of-magnitude from $\Delta P$ budgets
4. A control-oriented thermal state model and time constants
5. Commissioning identification and validation gates for the twin

---

## B.2 Notation and Constants

| Symbol | Meaning | Units |
|---|---|---|
| $\dot Q$ | heat rate | W |
| $P_{IT}$ | IT electrical power | W |
| $\dot m$ | mass flow rate | kg/s |
| $\dot V$ | volumetric flow rate | m$^3$/s |
| $\rho$ | fluid density (water, 25–30 °C) | kg/m$^3$ |
| $c_p$ | specific heat (water) | J/(kg·K) |
| $\Delta T$ | temperature rise | K or °C |
| $\Delta P$ | pressure differential | Pa |
| $\eta_{pump}$ | pump + motor + VFD efficiency | – |
| $C$ | effective thermal capacitance | J/K |
| $\tau$ | thermal time constant | s |

**Water properties (PoC design basis)**  
- $\rho \approx 997\ \text{kg/m}^3$  
- $c_p \approx 4180\ \text{J/(kg·K)}$

---

## B.3 Thermodynamic Closure (Heat → Flow)

### B.3.1 Fundamental single-phase relation

For an incompressible single-phase coolant loop:

$$
\dot Q = \dot m\, c_p\, \Delta T
$$

Rearranging gives required mass flow:

$$
\dot m = \frac{\dot Q}{c_p\,\Delta T}
$$

Using density to convert to volumetric flow:

$$
\dot V = \frac{\dot m}{\rho}
$$

### B.3.2 Witness workload heat rate

$$
\dot Q \approx P_{IT} \approx 4.77\times10^6\ \text{W}
$$

### B.3.3 Pod-level mass flow

With $\Delta T = 12^\circ\text{C}$:

$$
\dot m
= \frac{4.77\times10^6}{4180 \times 12}
$$

Compute denominator:

$$
4180\times 12 = 50{,}160
$$

So:

$$
\dot m \approx \frac{4.77\times10^6}{50{,}160}
\approx 95.1\ \text{kg/s}
$$

### B.3.4 Pod-level volumetric flow

$$
\dot V = \frac{95.1}{997}
\approx 0.0954\ \text{m}^3/\text{s}
\approx 95\ \text{L/s}
$$

**Pod-level result (witness case)**

$$
\boxed{\dot m \approx 95.1\ \text{kg/s}},\qquad
\boxed{\dot V \approx 0.095\ \text{m}^3/\text{s}\approx 95\ \text{L/s}}
$$

---

## B.4 Zone-Level Distribution (1 MW Fault Domains)

### B.4.1 Per-zone flow at 1 MW

For a zone cap of $\dot Q_{zone} = 1\ \text{MW}$:

$$
\dot m_{zone} = \frac{1.0\times10^6}{4180 \times 12}
\approx 19.9\ \text{kg/s}
$$

$$
\dot V_{zone} = \frac{19.9}{997}
\approx 0.0200\ \text{m}^3/\text{s}
\approx 20.0\ \text{L/s}
$$

**Per-zone result**

$$
\boxed{\dot V_{zone} \approx 20\ \text{L/s}\ \text{per}\ 1\ \text{MW zone at}\ \Delta T=12^\circ\text{C}}
$$

### B.4.2 Mapping the witness workload to zones

Witness load is served by approximately five 1 MW zones in aggregate:

$$
\frac{4.77\ \text{MW}}{1\ \text{MW/zone}} \approx 4.77\ \text{zones}
$$

Nominal flow if distributed across 5 zones:

$$
5 \times 20\ \text{L/s} = 100\ \text{L/s}
$$

This matches the pod-level 95 L/s within rounding and distribution overheads.

---

## B.5 Rack-Level Sanity (100 kW Baseline)

For a 100 kW rack:

$$
\dot m_{rack} = \frac{100{,}000}{4180\times 12}
\approx 1.99\ \text{kg/s}
$$

$$
\dot V_{rack} \approx \frac{1.99}{997}
\approx 0.0020\ \text{m}^3/\text{s}
\approx 2.0\ \text{L/s}
$$

---

## B.6 Pumping Power ($\Delta P$ Budget → Watts)

$$
P_{pump} \approx \frac{\Delta P \cdot \dot V}{\eta_{pump}}
$$

### B.6.1 Zone pumping order-of-magnitude

Assume a PoC zone budget:

- $\Delta P_{zone} = 100\ \text{kPa} = 100{,}000\ \text{Pa}$
- $\dot V_{zone} \approx 0.020\ \text{m}^3/\text{s}$
- $\eta_{pump} = 0.70$

$$
P_{pump,zone}
\approx \frac{100{,}000 \times 0.020}{0.70}
\approx 2{,}857\ \text{W}
\approx 2.9\ \text{kW}
$$

If the witness workload uses ~5 zones:

$$
P_{pump,total} \approx 5 \times 2.9\ \text{kW} \approx 14.5\ \text{kW}
$$

---

## B.7 Thermal State Model (Digital Twin Anchor)

### B.7.1 First-order energy balance

$$
C\frac{dT_{out}}{dt} = \dot Q(t) - \dot m(t)\,c_p\,\big(T_{out}(t) - T_{in}(t)\big)
$$

This is a **control-oriented abstraction** sufficient for estimation, anomaly detection, and supervisory setpoint control.

### B.7.2 Steady-state check

At steady state:

$$
\dot Q \approx \dot m c_p (T_{out}-T_{in})
\Rightarrow
\Delta T \approx \frac{\dot Q}{\dot m c_p}
$$

---

## B.8 Time Constant and Transient Behavior

Dominant time constant:

$$
\tau = \frac{C}{\dot m c_p}
$$

Illustrative inventory:

- $V_{eq} \approx 5\ \text{m}^3$
- $m_{eq} \approx 5000\ \text{kg}$

$$
C \approx m_{eq} c_p \approx 5000 \times 4180 \approx 2.09\times 10^7\ \text{J/K}
$$

With $\dot m \approx 20\ \text{kg/s}$:

$$
\dot m c_p \approx 20\times 4180 = 83{,}600\ \text{W/K}
$$

$$
\tau \approx \frac{2.09\times10^7}{83{,}600} \approx 250\ \text{s}
$$

---

## B.9 Discrete-Time Estimator (Online Twin)

For sampling interval $\Delta t$:

$$
\hat T_{out}[k+1] =
\hat T_{out}[k]
+
\frac{\Delta t}{\hat C}
\left(
\dot Q[k]
-
\dot m[k]\,c_p\,(\hat T_{out}[k]-T_{in}[k])
\right)
$$

Telemetry sources:

- $\dot Q[k]$: rack/block power metering
- $\dot m[k]$: flow meter
- $T_{in}[k]$: supply temperature
- $T_{out}[k]$: return temperature probe

---

## B.10 Commissioning Identification of $C$ (Evidence Gate)

Procedure:

1. Hold $T_{in}$ approximately constant
2. Hold $\dot m$ constant
3. Apply a bounded step or ramp in $\dot Q$
4. Measure $T_{out}(t)$
5. Fit $\hat C$ by minimizing prediction error

Acceptance (PoC intent):

$$
\text{RMSE}(T_{out}) \le 0.5^\circ\text{C}
$$

---

## B.12 Outputs Exported (Witness Case)

- Pod-level flow:
$$
\dot V \approx 0.095\ \text{m}^3/\text{s} \approx 95\ \text{L/s}
$$

- Zone-level flow:
$$
\dot V_{zone} \approx 0.020\ \text{m}^3/\text{s} \approx 20\ \text{L/s}
$$

- Rack flow:
$$
\dot V_{rack} \approx 2\ \text{L/s}
$$

- Zone time constant (illustrative):
$$
\tau \approx 250\ \text{s}
$$
