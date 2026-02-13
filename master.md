---
title: AI-Native Data Center Pod - 10 MW Reference Architecture (PoC)
author: Antreas Christofi
date: 2026-02-11
toc: true
toc-depth: 3
---
## Authority Model

- **BoD** is the single normative source of requirements and evidence gates.
- **Appendices** are the single source of derivations and worked examples.
- **Reference Architecture** instantiates a topology that satisfies the BoD by reference only.
- **Witness excerpt** is a pointer and reconciliation summary; the authoritative witness lives in Appendix F.

# Engineering Basis of Design

**Physics-First, Vendor-Agnostic**

---

## 0. Executive Snapshot

**Purpose:**  
Define a facility capable of sustaining ~10 MW IT load for large-scale AI training with liquid-cooled racks, while remaining safe, observable, and operable across its lifecycle.

**What this document is**

- A set of **engineering contracts** between IT and facility
- A definition of **envelopes** (power, heat, water, telemetry)
- A framework for **commissioning and evidence**

**What this document is not**

- A vendor selection
- A construction drawing pack
- A detailed controls implementation

**System Chain (intent)**  
```
Workload → Electrical Power → Heat Capture → Fluid Transport → Heat Rejection → Monitoring → Operations
```

**Key Envelopes**

| Domain | Target |
|---|---|
| IT load | 10 MW nominal |
| Rack cooling | Direct liquid to chip + CDU |
| $\Delta T$ liquid | 8–12 K initial assumption |
| Redundancy | N+1 at critical layers |
| Observability | Second-level telemetry |

---

## 1. Scope, Audience, Definitions

### 1.1 Audience

- Electrical & mechanical engineering
- Controls & BMS teams
- Safety/compliance
- Operations

### 1.2 Boundary Definitions

- **IT boundary:** rack manifolds and CDU interface
- **Facility boundary:** power delivery, fluid loops, heat rejection, controls
- **Responsibility split** defined at CDU inlet/outlet and rack PDU

### 1.3 Terminology

- FW – facility water loop
- TC – technical coolant to racks
- CDU – coolant distribution unit
- HIL – hardware-in-loop testing
- KPI – sustainability & performance metric

---

## 2. Architecture Contracts (Normative Core)

### A. Electrical Contract

The facility shall:

1. Deliver up to **10 MW IT power** with defined ramp limits
2. Support dynamic AI workloads with bounded inrush
3. Provide:
   - N+1 at MV/LV transformation
   - Selective coordination
   - Power quality monitoring

**Evidence**

- FAT/SAT under staged load banks
- Ramp tests 0→100% and 100→0%
- Harmonics and THD reports

---

### B. Thermal Contract

1. Capture ≥ 95% of IT heat via liquid path
2. Maintain rack inlet temperature within OEM envelope
3. Provide:
   - Dual-loop separation (FW / TC)
   - Leak containment and detection
   - Minimum $\Delta T$ target 8 K

**Evidence**

- Calibrated heat balance
- Flow vs load linearity
- CDU failover test

---

### C. Water Contract

1. Water quality per OEM specification
2. Treatment for corrosion/biological control
3. Leak detection at:
   - rack
   - CDU
   - header

**Evidence**

- Lab analysis
- Pressure decay tests
- Containment verification

---

### D. Observability Contract

1. All critical paths instrumented
2. Time-synchronized telemetry
3. Alarms classified:
   - Safety
   - Service
   - Advisory

**Evidence**

- Telemetry acceptance
- Alarm storm testing
- Loss-of-signal scenarios

---

### E. Sustainability Contract

1. Measure PUE, WUE, CUE boundaries
2. Reportable metering at:
   - utility entry
   - IT PDUs
   - cooling plant

**Evidence**

- KPI reconciliation
- Sensor calibration records

---

## 3. Reference Architecture Overview (Non-Normative Orientation)

### 3.1 Electrical

- Utility → MV → LV
- Static transfer where required
- Rack PDUs with branch monitoring
- Grounding and bonding strategy

### 3.2 Mechanical

- FW loop to dry coolers
- CDU layer with TC distribution
- Residual air path for ancillaries

### 3.3 Controls

- BMS + DCIM integration
- State machines for:
  - ramp
  - fault isolation
  - maintenance modes

### 3.4 Safety

- EPO logic
- Leak isolation
- Fire strategy compatible with liquid cooling

---

## 4. Architecture Tradeoffs and Design Decisions

This section records the key architectural decisions made within the admissible physical envelope established in Appendices A–F.

The appendices prove feasibility.  
This section explains selection.

Each decision represents a chosen operating point within a broader feasible region, balancing thermodynamic efficiency, reliability, operability, and deployment constraints.

---

### 4.1 Cooling Topology Selection

**Decision**  
Warm-water direct-to-chip liquid cooling with CDU-mediated separation between facility water (FW) and technical coolant (TC).

**Alternatives Considered**

- Air cooling
- Rear-door heat exchangers (RDHx)
- Full immersion cooling
- Hybrid air/liquid approaches

**Tradeoffs**

| Approach | Advantages | Disadvantages |
|---|---|---|
| Air cooling | Simpler infrastructure, mature operational model | Cannot sustain ≥100 kW/rack without excessive airflow, fan power, and acoustic burden |
| RDHx | Compatible with existing air-cooled hardware | Retains airflow dependency and heat rejection inefficiencies |
| Immersion | Excellent heat transfer and density potential | Operational complexity, hardware compatibility constraints, vendor coupling |
| Direct-to-chip (selected) | Highest heat capture efficiency, scalable to ≥150 kW/rack, vendor-neutral facility interface | Requires liquid infrastructure and leak containment |

**Rationale**

Direct-to-chip liquid cooling maximizes primary heat capture efficiency while preserving hardware and operational flexibility.  
It aligns with the ≥95% liquid heat capture contract and enables rack densities beyond the practical limits of air cooling.

The CDU separation preserves facility independence from IT coolant chemistry and hardware refresh cycles.

**Sensitivity**

If rack densities remained below approximately 40 kW/rack, air or hybrid cooling approaches could remain viable.  
If immersion becomes operationally standardized across vendors, immersion may become favorable in future revisions.

---

### 4.2 Thermal Rise ($\Delta T$) Selection

**Decision**  
Design liquid temperature rise of approximately 12 °C (envelope 8–15 °C).

**Alternatives Considered**

- Low $\Delta T$ (5–8 °C)
- High $\Delta T$ (15–20 °C)

**Tradeoffs**

| $\Delta T$ | Advantages | Disadvantages |
|---|---|---|
| Lower | Lower component temperature gradients, simpler control | Higher required flow rates, larger pipes, higher pumping power |
| Higher | Lower flow rates, smaller pipes, reduced pumping power | Reduced thermal control margin, potential component temperature risk |
| 12 °C (selected) | Balanced flow rate, pumping power, and thermal margin | Requires coordinated control |

**Rationale**

The selected $\Delta T$ minimizes hydraulic infrastructure size while maintaining sufficient thermal margin for control stability and transient absorption.

This choice reduces pipe diameter, pump sizing, and parasitic energy without compromising component safety.

**Sensitivity**

Lower $\Delta T$ may be required for hardware with tighter thermal tolerances.  
Higher $\Delta T$ may be feasible with validated component envelopes and control authority.

---

### 4.3 Mechanical Fault Domain Size

**Decision**  
Maximum mechanical fault domain size of approximately 1 MW.

**Alternatives Considered**

- Smaller zones (250–500 kW)
- Larger zones (2 MW or greater)

**Tradeoffs**

| Zone Size | Advantages | Disadvantages |
|---|---|---|
| Smaller | Greater fault containment | Increased infrastructure complexity and cost |
| Larger | Reduced infrastructure overhead | Larger thermal impact during failures |
| 1 MW (selected) | Balanced containment, manageable infrastructure complexity | Moderate additional infrastructure overhead |

**Rationale**

A 1 MW zone size ensures that hydraulic or cooling failures remain locally recoverable without propagating to the full facility.

This aligns mechanical fault domains with operational recoverability and commissioning isolation.

**Sensitivity**

Higher availability targets may justify smaller zones.  
Lower capital cost priorities may justify larger zones.

---

### 4.4 Electrical Fault Domain Size

**Decision**  
Electrical distribution blocks of approximately 2 MW.

**Alternatives Considered**

- Smaller blocks (≤1 MW)
- Larger blocks (≥5 MW)

**Tradeoffs**

| Block Size | Advantages | Disadvantages |
|---|---|---|
| Smaller | Better fault isolation | Increased electrical infrastructure complexity |
| Larger | Reduced infrastructure overhead | Larger blast radius during faults |
| 2 MW (selected) | Balanced coordination, scalability, and fault containment | Moderate infrastructure overhead |

**Rationale**

2 MW electrical blocks align with mechanical zoning while maintaining manageable switchgear and protection complexity.

This structure supports selective coordination and staged energization during commissioning.

**Sensitivity**

Higher grid instability environments may favor smaller blocks.  
Highly stable utility environments may support larger blocks.

---

### 4.5 Heat Rejection Strategy

**Decision**  
Primary reliance on dry cooling, with optional heat reuse as a secondary path.

**Alternatives Considered**

- Mandatory heat reuse
- Evaporative cooling primary systems

**Tradeoffs**

| Strategy | Advantages | Disadvantages |
|---|---|---|
| Mandatory reuse | Maximum energy recovery | Introduces dependency on external heat consumers |
| Evaporative primary | High efficiency | High water consumption |
| Dry primary, reuse secondary (selected) | Independence, water conservation, operational robustness | Slightly lower peak thermal efficiency |

**Rationale**

The facility must remain fully operable independent of external heat consumers.

Heat reuse is supported but must not introduce operational dependency.

**Sensitivity**

Sites with guaranteed heat offtake may increase reuse integration.

---

### 4.6 Digital Twin Authority Model

**Decision**  
Digital twin operates in bounded supervisory mode, without authority over safety interlocks.

**Alternatives Considered**

- Full autonomous control
- Monitoring-only model

**Tradeoffs**

| Mode | Advantages | Disadvantages |
|---|---|---|
| Autonomous | Maximum automation potential | Increased safety and validation risk |
| Monitoring only | Maximum safety | Limited operational optimization |
| Supervisory bounded (selected) | Balanced safety and optimization | Requires defined authority boundaries |

**Rationale**

The twin provides operational optimization while preserving deterministic safety behavior.

Safety-critical interlocks remain hardware- or PLC-enforced.

**Sensitivity**

Future validated models may expand operational authority.

---

### 4.7 Cooling Topology Independence from Hardware Vendors

**Decision**  
Facility cooling architecture remains vendor-agnostic at the CDU boundary.

**Alternatives Considered**

- Hardware-specific cooling integration

**Tradeoffs**

| Approach | Advantages | Disadvantages |
|---|---|---|
| Vendor-specific | Maximum optimization for specific hardware | Reduces flexibility, increases lock-in |
| Vendor-agnostic (selected) | Maximum flexibility, supports hardware evolution | Requires interface abstraction |

**Rationale**

Facility lifetime exceeds IT hardware lifetime.

Vendor-neutral interfaces preserve long-term adaptability.

---

### 4.8 Summary

The selected architecture represents a balanced operating point within the physically admissible region defined by Appendices A–F.

The chosen decisions prioritize:

- Fault containment
- Operational independence
- Thermal and hydraulic efficiency
- Commissionability
- Long-term adaptability

These selections establish a reference architecture that is physically grounded, operationally robust, and adaptable to evolving AI workloads.


## 5. Sizing Envelopes (Outputs)

### 5.1 Thermal

| Item | Value (initial) |
|---|---|
| Heat to liquid | 9.5–10 MW |
| $\Delta T$ design | 10 K |
| Estimated flow | derived in Appendix B |
| CDU capacity | N+1 |

### 5.2 Electrical

| Item | Value |
|---|---|
| IT power | 10 MW |
| Ancillary | ~8–12% |
| Diversity | workload dependent |

### 5.3 Water

- Header sizing per hydraulic model
- Treatment plant sized for volume turnover

### 5.4 Observability

- 1 s resolution for critical paths
- 10 s for secondary
- Retention ≥ 24 months

---

## 6. Commissioning & Evidence Plan

For each contract:

1. **Method**
   - FAT
   - SAT
   - IST
   - HIL
2. **Instrumentation**
   - Calibrated meters
   - Flow/temperature pairs
   - Power analyzers
3. **Acceptance**
   - Numeric criteria
   - Stability windows
   - Negative tests
4. **Artifacts**
   - Reports
   - Traces
   - As-built configs

---

## 7. Operational Model

- Change governance
- Maintenance schedules
- Alarm philosophy
- Training requirements
- Failure drills

---

### Document Status

- Version: Draft 1.0
- Nature: Engineering Basis of Design
- Ownership: Multidisciplinary

**Appendix Index (authoritative derivations and evidence mapping)**  
- Appendix A — Workload → Boundary Projection  
- Appendix B — Thermal Physics and Digital Twin Basis  
- Appendix C — Hydraulics, Piping, and $\Delta P$ Budgets  
- Appendix D — Sustainability Model and KPI Closure  
- Appendix E — Cross-Domain FMEA and Commissioning Mapping  
- Appendix F — CausalCompute Alignment (Witness Workload Closure)
# Appendix A — Workload → Boundary Projection

## A.1 Purpose

This appendix translates **arbitrary AI training objectives** into the **electrical and thermal quantities visible at the facility boundary**.

The facility is intentionally **workload-agnostic**.  
Therefore the mathematics is used to generate:

- admissibility envelopes, not a single model instance
- power transients, not GPU counts
- boundary quantities ($P_{IT}$, $\Delta P$, $dP/dt$)

---

## A.2 Notation

| Symbol | Meaning | Units |
|---|---|---|
| $P$ | model parameters | params |
| $Tok$ | total training tokens | tokens |
| $T$ | wall-clock budget | s |
| $c$ | FLOPs per param-token (dense ≈ 6) | FLOPs/(param·token) |
| $\eta$ | sustained training efficiency | – |
| $\varepsilon_{comp}$ | compute efficiency at IT boundary | FLOPs/s/W |
| $P_{IT}$ | IT electrical power | W |
| $\Delta P$ | step change in power | W |
| $dP/dt$ | ramp rate | W/s |

---

## A.3 Compute Demand

Total algorithmic work:

$$
F_{total} = c \cdot P \cdot Tok
$$

Required sustained throughput:

$$
F_{req} = \frac{F_{total}}{T}
$$

Mapping to electrical boundary:

$$
P_{IT,derived} = \frac{F_{req}}{\eta \cdot \varepsilon_{comp}}
$$

**Admissibility condition**

$$
P_{IT,derived} \le P_{IT,envelope}
$$

For this pod:

$$
P_{IT,envelope} = 10\ \text{MW}
$$

This relation **does not size the facility to one model**;  
it checks whether any proposed workload lies inside the envelope.

---

## A.4 Transient Abstraction

AI training creates power transients through:

- job start/stop
- collective synchronizations
- checkpoint I/O
- failure recovery

Detailed behavior is reduced to enforceable limits:

$$
|\Delta P| \le \Delta P_{max}
$$

$$
\left|\frac{dP}{dt}\right| \le (dP/dt)_{max}
$$

### PoC Envelope (per 2 MW block)

$$
\Delta P_{block} \le 0.2\ \text{MW in 1 s}
$$

$$
\left|\frac{dP_{block}}{dt}\right| \le 0.1\ \text{MW/s}
$$

Recovery objective:

$$
t_{rec,block} \le 10\text{–}30\ \text{s}
$$

### Per Rack (100 kW baseline)

$$
\Delta P_{rack} \le 10\ \text{kW in 1 s}
$$

$$
\left|\frac{dP_{rack}}{dt}\right| \le 5\ \text{kW/s}
$$

---

## A.5 Admissible Workload Set

The facility admits any workload whose boundary image lies in:

$$
\mathcal{A} =
\left\{
\begin{aligned}
P(t) &\le 10\ \text{MW} \\
|\Delta P| &\le 0.2\ \text{MW/block} \\
|dP/dt| &\le 0.1\ \text{MW/s} \\
T_{TC} &\in [T_{min},T_{max}] \\
\dot m &\ge \dot m_{min}
\end{aligned}
\right.
$$

Workload mathematics therefore act as the **projection operator** into this admissible set.

---

## A.6 Conceptual Shift

**Locked design**

$$
(P, Tok, T) \rightarrow \text{exact GPU count} \rightarrow \text{facility}
$$

**Agnostic design**

$$
(P, Tok, T) \rightarrow (P_{IT},\ \Delta P,\ dP/dt) \rightarrow \text{facility envelopes}
$$

---

## A.7 Scheduler ↔ Facility Interface

### Enforcement Layers

1. **Primary – Scheduler Policy**
   - staged energization
   - anti-herd starts
   - block-local ramp sequencing

2. **Secondary – Power Capping**
   - per-rack limits
   - collective smoothing

3. **Tertiary – BESS/UPS**
   - grid event absorption
   - residual transient buffer

---

## A.8 Verification Method

Acceptance requires replay of representative traces:

- voltage sag within limits
- UPS not overloaded
- CDU controls stable
- $T_{out}$ within envelope

---

## A.9 Definition

> In the agnostic formulation, workload mathematics serve to **map arbitrary ML objectives into boundary quantities** rather than prescribe a specific model instance.

---

## A.10 Outputs to Facility Design

This appendix produces:

- IT power envelope ($P_{IT}$)
- transient limits ($\Delta P$, $dP/dt$)
- inputs to thermal sizing (Appendix B)
- acceptance tests for commissioning

---

## A.11 Witness Reference

A single worked witness is maintained in Appendix F (CausalCompute alignment).  
All numeric witness values used by Appendices B–E are sourced from Appendix F.

**End of Appendix A**
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

## B.9 Discrete-Time Estimator (Online Digital Twin)

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
# Appendix C — Hydraulics, Piping, and $\Delta P$ Budgets

## C.1 Purpose

This appendix converts the thermal requirements from Appendix B into **buildable hydraulic quantities**:

- header diameters from the single witness flow ($\approx 95\ \text{L/s}$)
- per-zone distribution at $\approx 20\ \text{L/s}$
- velocity and $\Delta P$ budgets
- pumping power and sensitivity
- fault-domain containment rules

All calculations are SI; water at 25–30 °C is assumed.

---

## C.2 Continuity → Pipe Diameter

For design velocity $v$:

$$
A = \frac{\dot V}{v},
\qquad
d = \sqrt{\frac{4A}{\pi}}
$$

### C.2.2 Pod-level header (witness case)

From Appendix B:

$$
\dot V \approx 0.095\ \text{m}^3/\text{s}\ (95\ \text{L/s})
$$

Use $v = 2.0\ \text{m/s}$:

$$
A = \frac{0.095}{2.0}
= 0.0475\ \text{m}^2
$$

$$
d = \sqrt{\frac{4 \times 0.0475}{\pi}}
\approx 0.246\ \text{m}
$$

$$
\boxed{d \approx 0.25\ \text{m}}
$$

**Interpretation**

- Primary header class ≈ **DN250** (DN250–DN300 depending on allowances)
- The full 10 MW pod (200 L/s) would approach **DN350** class

---

## C.3 Zone Branch Sizing

Zone flow:

$$
\dot V_{zone} \approx 0.020\ \text{m}^3/\text{s}\ (20\ \text{L/s})
$$

Using $v=2.0\ \text{m/s}$:

$$
A_{zone} = \frac{0.020}{2.0}
= 0.010\ \text{m}^2
$$

$$
d_{zone} = \sqrt{\frac{4\times0.010}{\pi}}
\approx 0.113\ \text{m}
$$

$$
\boxed{d_{zone} \approx 110\ \text{mm}}
$$

Practical class: **DN100–DN125** per 1 MW zone.

---

## C.4 Rack Manifold Sanity

For 100 kW rack:

$$
\dot V_{rack} \approx 2\ \text{L/s} = 0.002\ \text{m}^3/\text{s}
$$

If internal manifold velocity target $v=1.5\ \text{m/s}$:

$$
A_{rack} = \frac{0.002}{1.5}
= 0.00133\ \text{m}^2
$$

$$
d_{rack} \approx 0.041\ \text{m}
$$

Approx: **DN40** class at rack interface.

---

## C.5 $\Delta P$ Budget Philosophy

$$
P_{pump} = \frac{\Delta P \cdot \dot V}{\eta}
$$

### C.5.1 PoC zone budget

| Segment | Target $\Delta P$ |
|---|---:|
| Rack cold plates & hoses | ≤ 40 kPa |
| CDU HEX + internals | ≤ 40 kPa |
| Distribution & valves | ≤ 20 kPa |
| **Total zone** | **≤ 100 kPa** |

### C.5.2 Zone pumping power (order-of-magnitude)

$$
\dot V_{zone} = 0.020\ \text{m}^3/\text{s},\quad
\Delta P = 100{,}000\ \text{Pa},\quad
\eta = 0.70
$$

$$
P_{pump,zone}
= \frac{100{,}000 \times 0.020}{0.70}
\approx 2.9\ \text{kW}
$$

Sensitivity:

$$
P_{pump} \propto \Delta P
$$

---

## C.6 Velocity Limits (Rationale)

| Location | Limit | Reason |
|---|---:|---|
| Primary headers | ≤ 2.0 m/s | erosion, noise, transients |
| Secondary runs | ≤ 1.5 m/s | controllability |
| Rack hoses | ≤ 1.2 m/s | connector wear |
| Drain lines | ≥ 0.6 m/s | sediment transport |

---

## C.7 Transient & Water Hammer Considerations

Rapid valve closure can generate:

$$
\Delta P_{wh} \approx \rho a \Delta v
$$

Mitigations:

- rate-limited actuators
- surge arrestors
- controlled pump ramps
- commissioning verification of worst-case closures

---

## C.8 Segmentation and Fault Domains

Containment rule:

A single failure shall not exceed **1 MW thermal radius**.

Therefore:

- isolation valves at each zone boundary
- no common headers without sectionalization
- wet components segregated from electrical rooms

Design spill volume per zone:

$$
V_{spill,max} = \dot V_{zone} \times t_{detect}
$$

With 20 L/s and 10 s detection:

$$
V_{spill,max} \approx 200\ \text{L}
$$

---

## C.9 Instrumentation Requirements

Minimum per zone:

- supply $T_{TC,in}$
- return $T_{TC,out}$
- flow $\dot m$ or $\Delta P$ proxy
- valve position
- leak detection (cable + point)

---

## C.10 Commissioning Evidence

Acceptance tests derived from this appendix:

1. Flow verification:
$$
\dot V_{zone} \ge 20\ \text{L/s}
$$

2. $\Delta P$ envelope:
$$
\Delta P_{zone} \le 100\ \text{kPa}
$$

3. Velocity audit: confirm $v \le 2\ \text{m/s}$ in primaries

4. Transient test: staged valve closure without excursions

5. Leak isolation: single-zone containment proven
# Appendix D — Sustainability Model and KPI Closure (Witness-Case Extended)

## D.1 Purpose

This appendix converts the single witness workload into auditable sustainability quantities:

- annual energy (IT and facility)
- heat export potential ($\text{MWh}_{th}$)
- water sensitivity under adiabatic/evaporative augmentation
- KPI boundaries and required metering
- a PoC measurement plan that makes reporting defensible

The intent is not “green claims,” but metered contracts.

---

## D.2 Inputs from the Witness Case

From Appendix F:

$$
P_{IT} \approx 4.77\ \text{MW}
$$

Design intent:

- $\text{PUE}_{design} = 1.15$
- utilization factor $U = 0.70$ (example)

Facility power:

$$
P_{fac} = P_{IT} \times \text{PUE}
$$

$$
P_{fac} \approx 4.77 \times 1.15 = 5.4855\ \text{MW}
$$

---

## D.3 Annual Energy ($\text{MWh/year}$)

### D.3.1 IT energy

$$
E_{IT,year} = P_{IT} \times 8760 \times U
$$

Compute:

$$
E_{IT,year} \approx 4.77 \times 8760 \times 0.70 \approx 29{,}300\ \text{MWh/yr}
$$

### D.3.2 Facility energy

$$
E_{fac,year} = P_{fac} \times 8760 \times U
$$

$$
E_{fac,year} \approx 5.4855 \times 8760 \times 0.70 \approx 33{,}600\ \text{MWh/yr}
$$

### D.3.3 Overhead energy (derived)

$$
E_{overhead,year} = E_{fac,year} - E_{IT,year} \approx 4{,}300\ \text{MWh/yr}
$$

---

## D.4 Heat Reuse Potential

Thermal energy available at IT boundary (first order):

$$
\dot Q \approx P_{IT}
$$

Annual thermal energy:

$$
E_{heat,year} \approx P_{IT} \times 8760 \times U \approx 29{,}300\ \text{MWh}_{th}\text{/yr}
$$

Heat pump relation (if needed):

If $COP = \frac{Q_{delivered}}{W_{hp}}$:

$$
W_{hp} = \frac{Q_{delivered}}{COP},
\qquad
Q_{source} = Q_{delivered}\left(1-\frac{1}{COP}\right)
$$

---

## D.5 Water Sensitivity (Adiabatic/Evaporative Augmentation)

Latent heat of vaporization:

$$
h_{fg} \approx 2.45\ \text{MJ/kg}
$$

Evaporation mass flow to reject heat $Q$:

$$
\dot m_{evap} \approx \frac{Q}{h_{fg}}
$$

For $Q = 4.77\ \text{MW} = 4.77\ \text{MJ/s}$:

$$
\dot m_{evap} \approx \frac{4.77}{2.45}\ \text{kg/s}
\approx 1.95\ \text{kg/s}
\approx 1.95\ \text{L/s}
$$

Daily water (if operating adiabatic continuously):

$$
V_{day} \approx 1.95\ \text{L/s} \times 86400\ \text{s/day}
\approx 168{,}000\ \text{L/day}
\approx 168\ \text{m}^3/\text{day}
$$

Annual at utilization $U=0.70$:

$$
V_{year} \approx 168\ \text{m}^3/\text{day}\times 365 \times 0.70
\approx 43{,}000\ \text{m}^3/\text{yr}
$$

---

## D.6 KPI Definitions (Time-Series, Not Scalars)

### D.6.1 $\text{PUE}(t)$

$$
\text{PUE}(t) = \frac{P_{fac}(t)}{P_{IT}(t)}
$$

Requirements:

- computed as a time series with time-aligned meters
- reported as distributions/percentiles, not a single number

### D.6.2 $\text{WUE}(t)$

Where make-up water exists:

$$
\text{WUE}(t) = \frac{\dot V_{makeup}(t)}{P_{IT}(t)}
$$

Interval form:

$$
\text{WUE}_{interval} = \frac{V_{makeup}}{E_{IT}}\quad [\text{L/kWh}]
$$

### D.6.3 Heat export metering

Exported thermal power:

$$
\dot Q_{export}(t) = \rho\,c_p\,\dot V_{export}(t)\,\Delta T_{export}(t)
$$

Exported energy:

$$
E_{export} = \int \dot Q_{export}(t)\,dt
$$

### D.6.4 Carbon (operational)

If grid intensity is a time series $I_{grid}(t)$ in kgCO$_2$e/kWh:

$$
CO2e = \int I_{grid}(t)\,P_{fac}(t)\,dt
$$

---

## D.7 Metering Contract (Minimum Required Meters)

Electrical:

- Utility import meter (true RMS, time-stamped)
- Plant distribution meters (cooling, UPS losses, BESS auxiliaries)
- IT boundary meters per block / PDU / RPP (depending on design)

Thermal / Water:

- Heat meter at export interface: $\dot V + T_{supply} + T_{return}$
- FW/TC loop meters per zone: $\dot V$, $T_{in}$, $T_{out}$, $\Delta P$
- Make-up water meter where adiabatic exists

Time synchronization:

- PTP/NTP such that meter timestamps are aligned and drift is monitored

---

## D.8 “Fails to Ambient” Export Philosophy (Reliability Constraint)

Heat export must not become a single point of failure.

Contractual rule:

- Export path must **fail open** to ambient rejection.
- Off-taker unavailability shall not cause derate of IT load.

This implies bypass and buffering at the export interface.

---

## D.9 Witness-Case Sustainability Summary

Using $P_{IT}=4.77\ \text{MW}$, $U=0.70$, $\text{PUE}=1.15$:

| Quantity | Result |
|---|---:|
| IT annual energy $E_{IT}$ | ~29,300 MWh/yr |
| Facility annual energy $E_{fac}$ | ~33,600 MWh/yr |
| Overhead energy | ~4,300 MWh/yr |
| Heat potential $E_{heat}$ | ~29,300 $\text{MWh}_{th}$/yr |
| Evaporation rate (adiabatic continuous) | ~1.95 L/s |
| Daily water (adiabatic continuous) | ~168 $\text{m}^3$/day |
| Annual water (adiabatic continuous, $U=0.70$) | ~43,000 $\text{m}^3$/yr |

Interpretation: even at ~5 MW class loads, adiabatic hours must be explicitly controlled and metered.

---

## D.10 PoC Verification Plan

To make the PoC defensible:

1. Verify $P_{IT}(t)$, $P_{fac}(t)$ and compute $\text{PUE}(t)$
2. Verify heat balance closure:
$$
P_{IT} \approx \rho c_p \dot V \Delta T + \text{loss terms}
$$
3. If export exists, verify heat meter accuracy and availability
4. If adiabatic exists, verify make-up meter and compute $\text{WUE}(t)$
5. Time-sync audit: confirm timestamp alignment across power + flow + temperature

Acceptance intent: all reported KPIs must be reproducible from raw time-series data with documented calibrations.
# Appendix E — Cross-Domain FMEA and Commissioning Mapping (Witness-Case)

## E.1 Purpose

This appendix applies a structured Failure Modes and Effects Analysis (FMEA) to the  
10 MW PoC architecture while explicitly referencing the **single witness workload** (Appendix F):

- $P_{IT} \approx 4.77\ \text{MW}$
- $\dot V \approx 95\ \text{L/s}$
- $\dot V_{zone} \approx 20\ \text{L/s}$
- $\tau \approx 250\ \text{s}$ (illustrative)

The objective is to:

1. Identify failure modes that could violate the envelopes derived in Appendices A–D
2. Quantify risk using the common O/S/D scale
3. Map each high-RPN item to a commissioning evidence gate

---

## E.2 Scoring Method (recap)

Risk Priority Number:

$$
RPN = O \times S \times D
$$

| Range | Action |
|---|---|
| ≥300 | Architectural redesign |
| 200–299 | Mandatory mitigation + tests |
| 120–199 | Mitigation required |
| 60–119 | Manage with monitoring |
| <60 | Accept / monitor |

---

## E.3 Electrical Domain (Witness-Linked)

| Failure Mode | O | S | D | RPN | Witness Impact | Mitigation | Commissioning Evidence |
|---|---:|---:|---:|---:|---|---|---|
| Block ramp exceeds envelope | 5 | 8 | 4 | 160 | Violates $dP/dt$ limits | Scheduler staging + capping | HIL replay of ramps |
| Protection mis-coordination | 3 | 9 | 6 | 162 | Upstream trip near block limits | Settings governance | Selectivity test |
| UPS bypass failure | 2 | 9 | 6 | 108 | Loss of power quality | Periodic transfer tests | Bypass SAT |
| Harmonics at PCC | 4 | 5 | 5 | 100 | PQ breach | Filters + monitoring | PQ report |
| Powered but uncooled | 3 | 10 | 6 | 180 | Thermal runaway | Hard interlocks | Interlock FAT |

---

## E.4 Mechanical Domain

| Failure Mode | O | S | D | RPN | Witness Link | Mitigation | Evidence |
|---|---:|---:|---:|---:|---|---|---|
| Loss of flow in 1 MW zone | 4 | 9 | 5 | 180 | $20\ \text{L/s}$ drop | Dual CDU + alarms | Flow fail test |
| Valve hunting | 5 | 6 | 6 | 180 | Violates $\tau$ model | Rate limits | Step response |
| Filter fouling | 6 | 5 | 4 | 120 | $\Delta P > 100\ \text{kPa}$ | DP trending | PM records |
| Leak at rack | 4 | 8 | 4 | 128 | ~200 L spill | Trays + isolation | Leak drill |
| Air ingress | 4 | 7 | 6 | 168 | Oscillatory $\Delta P$ | Degassing | Stability test |

---

## E.5 Digital Twin & Controls

| Failure Mode | O | S | D | RPN | Witness Link | Mitigation | Evidence |
|---|---:|---:|---:|---:|---|---|---|
| Sensor drift | 6 | 7 | 6 | 252 | Bias in $T_{out}$ | Redundancy | Calibration |
| Model obsolete | 5 | 7 | 7 | 245 | Wrong $\tau$ | BIM versioning | Re-fit gate |
| Time sync loss | 5 | 6 | 7 | 210 | KPI corruption | PTP/NTP | Audit |
| Unsafe actuation | 3 | 10 | 6 | 180 | Flow violation | Guardrails | Negative test |

---

## E.6 Sustainability Risks

| Failure Mode | O | S | D | RPN | Witness Link | Mitigation | Evidence |
|---|---:|---:|---:|---:|---|---|---|
| Unmetered PUE | 6 | 6 | 7 | 252 | KPI invalid | Meter plan | Data audit |
| Stranded heat | 6 | 6 | 6 | 216 | Export not utilized | Fail-open | Contract |
| Water cap breach | 4 | 8 | 6 | 192 | Adiabatic overuse | Dry baseline | WUE logs |

---

## E.7 Commissioning Matrix

### E.7.1 Electrical Tests

- Replay ramp to confirm:
$$
\left|\frac{dP}{dt}\right| \le 0.1\ \text{MW/s}
$$
- Verify per-block metering and coordination
- Bypass and transfer tests

### E.7.2 Mechanical Tests

- Prove:
$$
\dot V_{zone} \ge 20\ \text{L/s}
$$
- Confirm:
$$
\Delta P_{zone} \le 100\ \text{kPa}
$$
- Leak isolation within single zone

### E.7.3 Twin Validation

- RMSE:
$$
\le 0.5^\circ\text{C}
$$
- Fault injection for drift and flow loss

### E.7.4 Sustainability

- Reproduce $\text{PUE}(t)$ from raw meters
- Verify heat meter closure

---

## E.8 Top Residual Risks

1. Sensor integrity for twin ($RPN=252$)
2. KPI auditability ($RPN=252$)
3. Model validity after changes ($RPN=245$)

Mitigation requires process + engineering, not hardware alone.

---

## E.9 Conclusion

The witness workload demonstrates:

- envelopes can be met
- failure modes are observable
- each risk maps to a testable gate

No $RPN \ge 300$ remains after mitigations.
# Appendix F — CausalCompute Alignment (Witness Workload Closure)

## F.1 Purpose

This appendix demonstrates that the envelopes defined in the Basis of Design can host a representative AI training workload using an external first-principles sizing engine (CausalCompute, Steps 0–2).

This appendix is:

- a traceability bridge between ML intent and facility physics,
- an evidence layer showing that the BoD envelopes are internally consistent,
- a generator of measurable PoC acceptance criteria.

This appendix is not:

- a redesign of the facility,
- a procurement specification,
- a network topology design,
- a replacement for the thermo-hydraulic derivations in Appendices A–C.

---

## F.2 Lifecycle Status

### F.2.1 Status of the Basis of Design

The BoD is a pre-production engineering contract, not an as-built system.  
It defines envelopes and evidence gates that detailed design must later satisfy.

### F.2.2 Status of CausalCompute

CausalCompute is used here as a PoC witness engine.

Allowed conclusions:

- envelope admissibility at the facility boundary (MW, L/s, kW/rack)
- sensitivity to explicit assumptions ($\eta$, $\Delta T$, checkpoint policy)

Disallowed conclusions:

- procurement GPU counts or guarantees
- network topology or congestion claims
- ramp compliance verification
- reliability closure (N+1) or operational readiness

---

## F.3 Methodological Duality — Bottom-Up vs Top-Down

```
BoD — Bottom-Up (Facility-First)
Power → Heat → Fluid → Space → Operations
		 |________________________________
										  |
Workload → FLOPs → Time → GPUs → Power → Heat
CausalCompute — Top-Down (Workload-First)
```


Convergence: heat is the handoff point.

The tool delivers an implied heat rate:

$$
\dot Q_{\text{tool}} \approx P_{IT}
$$

The BoD accepts or rejects that heat rate using its $\Delta T$, flow, zoning, and rejection limits.

---

## F.4 Scope and Cross-References

| Domain | Primary Reference |
|---|---|
| Workload → boundary mapping | Appendix A |
| Thermal closure | Appendix B |
| Hydraulics & $\Delta P$ | Appendix C |
| Sustainability accounting | Appendix D |
| FMEA & commissioning | Appendix E |

---

## F.5 Inputs to the Witness Calculation

### F.5.1 Workload

| Item | Value |
|---|---|
| Parameters | $4.05\times10^{11}$ |
| Tokens | $3\times10^{12}$ |
| Deadline | 60 days |
| FLOPs/param-token | 6 |

### F.5.2 Platform Abstractions

| Item | Value |
|---|---|
| Device sustained | 0.875 PFLOP/s |
| Device memory | 80 GB |
| $\eta_{\text{compute}}$ | 0.30 |
| $\eta_{\text{fabric}}$ | 0.80 |

### F.5.3 Facility Envelopes (BoD)

| Envelope | Value |
|---|---|
| IT power | ≤ 10 MW |
| $\Delta T$ liquid | 12 °C (8–15) |
| Rack cap | 100 kW |
| Zone cap | 1 MW |
| Flow rule | ~20 L/s per MW |

---

## F.6 Step-0 — Topology-Agnostic Invariants

| Quantity | Result |
|---|---|
| Required throughput | 1.41 EFLOP/s |
| Token rate | $5.79\times10^5$ tok/s |
| Checkpoint size | 810 GB |
| Max step time | 69 s |

---

## F.7 Step-1 — Minimal Feasible Cluster (Witness)

| Item | Result |
|---|---|
| GPUs | 6,208 |
| Nodes | 776 (8 GPU/node) |
| Parallelism | DP/TP/PP = 97/16/4 |
| Step time | 68.9 s ≤ 69 s |
| Memory/device | 52 GB ≤ 80 GB |

---

## F.8 Step-2 → BoD Boundary Projection

### F.8.1 Power Alignment

| Quantity | Result | Envelope | Status |
|---|--:|--:|:-:|
| IT power | 4.77 MW | ≤ 10 MW | Aligned |
| Facility power | 5.49 MW | — | — |
| PUE | 1.15 | BoD target | aligned |

### F.8.2 Thermal Alignment

| Quantity | Result (SI) | Practical Equivalent | Rule | Status |
|---|---|---|---|:-:|
| Heat | 4.77 MW | — | 9.5–10 MW cap | Aligned |
| Coolant flow | 0.095 m$^3$/s | 95 L/s (5,700 L/min) | 20 L/s per MW | Aligned |
| Per-zone load | ~0.95 MW | — | ≤ 1 MW | Aligned |

Using Appendix B:

$$
\dot m = \frac{\dot Q}{c_p \, \Delta T}
$$

### F.8.3 Rack Density

| Item        | Result (SI) | Practical Equivalent | Limit  | Status |
| ----------- | ----------- | -------------------- | ------ | :----: |
| Racks       | 49          | —                    | —      |   —    |
| IT per rack | 97 kW       | (≈ 331,000 BTU/h)    | 100 kW |   Aligned    |

### F.8.4 Hydraulics

- Flow per zone ≈ 20 L/s (≈ 1,200 L/min)
- Header class consistent with DN200–DN250
- Pumping power remains kW-scale

---

## F.9 Alignment Statement

> The witness workload lies inside all BoD envelopes for electrical, thermal, hydraulic, and rack constraints.

---

## F.10 Sensitivities

| Change | Effect |
|---|---|
| $\eta_{\text{compute}}\downarrow$ | GPUs ↑ → IT power ↑ |
| $\Delta T\downarrow$ to 8 K | Flow ↑ ~50% |
| Shorter checkpoint | Storage BW ↑ |
| $\eta_{\text{fabric}}\downarrow$ | Step time ↑ → GPUs ↑ |

---

## F.11 PoC Evidence Gates

1. Power → heat closure:
$$
\dot Q \approx P_{IT}
$$

2. Flow law:
$$
\dot m = \frac{\dot Q}{c_p \, \Delta T}
$$

3. Ramp compliance: per Appendix A

4. Digital twin accuracy: per Appendix B

---



**End of Appendix F**
# Design Brief — Reference Architecture (PoC)

This document instantiates the Engineering Basis of Design (BoD) into a coherent 10 MW reference topology.

This document contains no original requirements and no original derivations.  
Requirements are defined in sections above.

---

## Executive Summary

This document defines a 10 MW AI-native data-center pod derived from physics rather than historical templates:

Workload → FLOP → Watts → Heat → Water → Space → Operations

Key principles:

1. Bounded fault domains — 2 MW electrical blocks aligned to 1 MW mechanical zones prevent local failures from becoming site events.
2. Explicit transients — sized against verified ramp envelopes ($\Delta P$, $dP/dt$), not steady-state MW alone. (See Appendix A.)
3. Closed-loop operations — a physics-based digital twin supervises within hard interlocks and auditable safety limits. (See Appendix B.)

The architecture is intentionally vendor-neutral and deployable across European jurisdictions.

---

## 1. Why This Is Needed

AI workloads have broken classical datacenter assumptions:

- Power density: 5–15 kW/rack → 30–150+ kW/rack
- Heat path: air → liquid-dominated
- Utilization: workload-bound, not facility-bound
- Deadlines: training schedules compete with time-to-power

Therefore infrastructure must be derived from workload physics, not legacy layouts.

---

## 2. Boundary Conditions

### Fixed by Reality

- Grid capacity and ramp constraints
- European limits on water, noise, and heat rejection
- Reliability targets (N+1 / optional 2N)
- Mixed customer hardware maintainability

### Design Freedoms

- Cooling topology (D2C/RDHx/hybrids)
- Electrical architecture (UPS/BESS placement)
- Spatial modularization
- Digital-twin control strategy

---

## 3. Inputs from the AI Domain

- Model size, tokens, deadline
- Implied sustained FLOP/s
- Availability & jitter tolerance
- Hardware class envelope (TDP, $\Delta T$ limits)

Workload-to-boundary mapping is defined in Appendix A.  
Witness closure via CausalCompute is defined in Appendix F.

---

## 4. Reference Topology (Instantiation)

- IT Load: 10 MW envelope (BoD)
- Rack baseline: 100 kW class
- Cooling: warm-water D2C with CDU layer
- Electrical blocks: 2 MW fault/coordination domains
- Mechanical zones: 1 MW maximum fault domains

Derived flow rules, $\Delta T$ envelopes, header classes, and $\Delta P$ budgets are defined in Appendices B and C.

---

## 5. Electrical Architecture (Instantiation)

- Utility → MV → LV distribution aligned to 2 MW blocks
- Per-block metering at the IT boundary (BoD observability contract)
- Selective coordination implemented per BoD electrical contract
- Interlock: rack enable requires cooling availability (BoD thermal contract)

Transient contract is defined in Appendix A and verified per Appendix E.

---

## 6. Mechanical Architecture (Instantiation)

- FW loop to dry coolers (baseline)
- CDU layer separating FW/TC
- Zone isolation at 1 MW boundaries
- Residual air path for ancillaries

Thermal closure and twin model basis are defined in Appendix B.  
Hydraulics, pipe classes, and $\Delta P$ budgets are defined in Appendix C.

---

## 7. Sustainability Architecture (Instantiation)

- Metering topology and KPI computation per Appendix D
- Export interface is fail-open to ambient rejection per Appendix D
- KPI reporting is time-series, not scalar summaries

---

## 8. Digital Twin Concept (Instantiation)

The twin uses the first-order energy balance defined in Appendix B, operating in modes:

Observe → Recommend → Bounded Actuate.

Authority boundaries: cannot override hard interlocks (BoD safety/thermal contracts).

---

## 9. Commissioning Philosophy (Instantiation)

- Progressive energization
- Negative testing
- Twin validation before authority
- Evidence-based acceptance

Test matrix and FMEA mapping are defined in Appendix E.

---

## 10. Deliverables to Detailed Design

- Electrical studies (load-flow, protection, harmonics)
- Hydraulic model and transient analysis
- Controls I/O and interlock matrix
- Twin data schema
- BIM constraints

---

## Core Principle

Infrastructure decisions are derivations from physics and workload, not brand templates.
