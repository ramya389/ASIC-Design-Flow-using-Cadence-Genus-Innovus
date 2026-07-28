---

# Full Adder Physical Design using Cadence Innovus

## Project Overview

This project demonstrates the complete physical implementation of a Full Adder using Cadence Innovus. The flow begins with importing the synthesized netlist and constraints and proceeds through floorplanning, power planning, placement, clock tree synthesis (CTS), routing, RC extraction, and timing verification.

---

## Design Inputs

### Netlist

Generated from Cadence Genus after RTL synthesis.

### Constraint File

```tcl
create_clock -name clk -period 10 -waveform {0 5} [get_ports clk]

set_clock_transition -rise 0.1 [get_clocks clk]
set_clock_transition -fall 0.1 [get_clocks clk]

set_clock_uncertainty 0.1 [get_clocks clk]

set_input_delay -max 1.0 [get_ports a] -clock [get_clocks clk]
set_input_delay -max 1.0 [get_ports b] -clock [get_clocks clk]
set_input_delay -max 1.0 [get_ports c] -clock [get_clocks clk]

set_output_delay -max 1.0 [get_ports s] -clock [get_clocks clk]
set_output_delay -max 1.0 [get_ports cout] -clock [get_clocks clk]
```

---

## MMMC Setup

### Library Sets

```text
min_timing → fast.lib
max_timing → slow.lib
```

### Delay Corners

```text
min_delay
max_delay
```

### Analysis Views

```text
best_case
worst_case
```

Setup Analysis:

```text
worst_case
```

Hold Analysis:

```text
best_case
```

---

## Floorplanning

Parameters:

```text
Aspect Ratio = 1
Core-to-Die Boundary = 2.5
```

Objectives:

- Define core area
- Define die area
- Improve routability

---

## Power Planning

### Power Ring

```text
Nets = VDD VSS

Metal5 → Top/Bottom
Metal6 → Left/Right

Width = 0.7
Spacing = 0.2
Offset = 0.5
```

### Power Stripes

```text
Layer = Metal6
Direction = Vertical

Width = 0.7
Spacing = 0.2
```


---

## Placement

Performed standard-cell placement.

Objectives:

- Reduce congestion
- Minimize wirelength
- Improve timing

### Screenshot

Insert placement screenshot here.

---

## Pre-CTS Timing Analysis

Generated reports for:

- Setup Slack
- Hold Slack

Requirement:

```text
Setup Slack > 0
Hold Slack > 0
```

---

## Clock Tree Synthesis (CTS)

### CTS Commands

```tcl
create_route_type -name clkroute \
-non_default_rule 2w2s \
-bottom_preferred_layer Metal5 \
-top_preferred_layer Metal6

set_ccopt_property buffer_cells {CLKBUFX2 CLKBUFX4}

set_ccopt_property inverter_cells {CLKINVX2 CLKINVX4}

create_ccopt_clock_tree_spec -file ccopt.spec

source ccopt.spec

ccopt_design -cts
```

Objectives:

- Reduce clock skew
- Balance clock latency
- Improve timing


---

## Routing

Performed:

```text
Global Routing
Detailed Routing
```

Enabled:

```text
Optimize Wire
Optimize Via
```

---

## RC Extraction

Generated:

```text
SPF
SPEF
```

Purpose:

- Extract parasitic resistance
- Extract parasitic capacitance
- Improve timing accuracy


---

## Post-Route Timing Analysis

Verified:

- Setup Timing
- Hold Timing
- WNS
- TNS

Timing closure achieved after routing.

---

## Key Learning Outcomes

- Cadence Innovus Design Import
- MMMC Configuration
- Floorplanning
- Power Planning
- Placement
- Clock Tree Synthesis
- Routing
- RC Extraction
- Static Timing Analysis
- Timing Closure
