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
\begin{cases}
P(t) \le 10\ \text{MW} \\
|\Delta P| \le 0.2\ \text{MW/block} \\
\left|\frac{dP}{dt}\right| \le 0.1\ \text{MW/s} \\
T_{TC} \in [T_{min},T_{max}] \\
\dot m \ge \dot m_{min}
\end{cases}
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
