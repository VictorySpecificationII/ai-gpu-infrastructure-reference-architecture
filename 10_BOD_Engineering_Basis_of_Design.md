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
Workload → Electrical Power → Heat Capture → Fluid Transport → Heat Rejection → Monitoring → Operations

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

## 4. Sizing Envelopes (Outputs)

### 4.1 Thermal

| Item | Value (initial) |
|---|---|
| Heat to liquid | 9.5–10 MW |
| $\Delta T$ design | 10 K |
| Estimated flow | derived in Appendix B |
| CDU capacity | N+1 |

### 4.2 Electrical

| Item | Value |
|---|---|
| IT power | 10 MW |
| Ancillary | ~8–12% |
| Diversity | workload dependent |

### 4.3 Water

- Header sizing per hydraulic model
- Treatment plant sized for volume turnover

### 4.4 Observability

- 1 s resolution for critical paths
- 10 s for secondary
- Retention ≥ 24 months

---

## 5. Commissioning & Evidence Plan

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

## 6. Operational Model

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
