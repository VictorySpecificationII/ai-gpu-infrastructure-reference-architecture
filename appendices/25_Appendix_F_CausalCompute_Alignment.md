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
| IT power | 4.77 MW | ≤ 10 MW | ✅ |
| Facility power | 5.49 MW | — | — |
| PUE | 1.15 | BoD target | aligned |

### F.8.2 Thermal Alignment

| Quantity | Result (SI) | Practical Equivalent | Rule | Status |
|---|---|---|---|:-:|
| Heat | 4.77 MW | — | 9.5–10 MW cap | ✅ |
| Coolant flow | 0.095 m$^3$/s | 95 L/s (5,700 L/min) | 20 L/s per MW | ✅ |
| Per-zone load | ~0.95 MW | — | ≤ 1 MW | ✅ |

Using Appendix B:

$$
\dot m = \frac{\dot Q}{c_p \, \Delta T}
$$

### F.8.3 Rack Density

| Item | Result (SI) | Practical Equivalent | Limit | Status |
|---|---|---|---|:-:|
| Racks | 49 | — | — | — |
| IT per rack | 97 kW | (≈ 331,000 BTU/h) | 100 kW | ✅ |

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

## F.13 Exhibit F-1 — Witness Brief

`/exhibits/causalcompute_witness_405B.yaml`

**End of Appendix F**
