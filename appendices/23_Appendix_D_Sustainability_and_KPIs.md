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
