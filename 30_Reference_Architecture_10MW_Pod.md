# Design Brief — Reference Architecture (PoC)

This document instantiates the Engineering Basis of Design (BoD) into a coherent 10 MW reference topology.

This document contains no original requirements and no original derivations.  
Requirements are defined in `10_BOD_Engineering_Basis_of_Design.md`.  
Derivations and worked examples are defined in `20_Appendix_A_...` through `25_Appendix_F_...`.

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
