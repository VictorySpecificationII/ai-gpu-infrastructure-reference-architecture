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
