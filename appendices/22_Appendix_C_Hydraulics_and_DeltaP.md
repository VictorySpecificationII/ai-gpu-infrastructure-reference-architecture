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

2. Pressure envelope:

$$
\Delta P_{zone} \le 100\ \text{kPa}
$$

3. Velocity audit:

Confirm

$$
v \le 2\ \text{m/s}
$$

in primary headers.

4. Transient test:

Staged valve closure without excursions beyond the defined $\Delta P$ envelope.

5. Leak isolation:

Single-zone containment proven.
