# ASIC Synthesis, Timing Analysis and Physical Design

This repository demonstrates a complete RTL-to-GDSII ASIC design flow using Cadence Genus and Cadence Innovus. The experiments cover logic synthesis, timing constraint development, static timing analysis (STA), PVT corner analysis, floorplanning, placement, clock tree synthesis (CTS), routing, RC extraction, and physical implementation.

---

## Tools Used

- Cadence Genus
- Cadence Innovus
- Cadence Virtuoso
- Verilog HDL
- Liberty Libraries (.lib)
- LEF Files
- SDC Constraints

---

# ASIC Design Flow

```text
RTL Design
    ↓
Functional Verification
    ↓
Logic Synthesis
    ↓
Gate-Level Netlist Generation
    ↓
Static Timing Analysis
    ↓
Floorplanning
    ↓
Placement
    ↓
Clock Tree Synthesis (CTS)
    ↓
Routing
    ↓
RC Extraction
    ↓
Post-Route Timing Analysis
    ↓
Physical Verification
```

---

# Static Timing Constraints (SDC)

## Clock Definition

```tcl
create_clock -name clk -period 10 -waveform {0 5} [get_ports clk]
```

Defines a clock with:
- Clock Period = 10 ns
- Rising Edge = 0 ns
- Falling Edge = 5 ns

---

## Clock Transition

```tcl
set_clock_transition -rise 0.1 [get_clocks clk]
set_clock_transition -fall 0.1 [get_clocks clk]
```

Defines rise and fall transition times.

---

## Clock Uncertainty

```tcl
set_clock_uncertainty 0.1 [get_clocks clk]
```

Accounts for clock jitter and skew.

---

## Input Delay Constraints

```tcl
set_input_delay -max 1.0 [get_ports a] -clock [get_clocks clk]
set_input_delay -max 1.0 [get_ports b] -clock [get_clocks clk]
set_input_delay -max 1.0 [get_ports c] -clock [get_clocks clk]
```

Represents external source arrival times.

---

## Output Delay Constraints

```tcl
set_output_delay -max 1.0 [get_ports s] -clock [get_clocks clk]
set_output_delay -max 1.0 [get_ports cout] -clock [get_clocks clk]
```

Represents timing requirements at receiving registers.

---

# Logic Synthesis Flow (Cadence Genus)

## Read RTL

```tcl
read_hdl design.v
```

## Elaborate Design

```tcl
elaborate
```

## Read Timing Constraints

```tcl
read_sdc design.sdc
```

## Run Synthesis

```tcl
syn_generic
syn_map
syn_opt
```

### Synthesis Stages

| Stage | Purpose |
|---------|---------|
| syn_generic | Generic optimization |
| syn_map | Technology mapping |
| syn_opt | Timing/Area optimization |

---

## Generate Reports

### Timing Report

```tcl
report_timing
```

### Area Report

```tcl
report_area
```

### Power Report

```tcl
report_power
```

### Generate Netlist

```tcl
write_hdl > netlist.v
```

---

# Static Timing Analysis (STA)

## Timing Parameters

### Arrival Time

Time required for data to reach destination.

### Required Time

Maximum allowable time.

### Slack

```text
Slack = Required Time − Arrival Time
```

### Timing Status

| Slack Value | Result |
|-------------|---------|
| Positive | Timing Met |
| Zero | Critical Path |
| Negative | Timing Violation |

---

# PVT Corner Analysis

PVT = Process + Voltage + Temperature

## Slow Corner

Used for Setup Analysis

```text
slow.lib
```

Characteristics:

- Slow NMOS
- Slow PMOS
- Maximum delay

---

## Fast Corner

Used for Hold Analysis

```text
fast.lib
```

Characteristics:

- Fast NMOS
- Fast PMOS
- Minimum delay

---

## Typical Corner

Nominal operating condition.

```text
typical.lib
```

---

# Physical Design Flow (Cadence Innovus)

## Input Files

```text
Netlist (.v)
SDC File (.sdc)
LEF File (.lef)
slow.lib
fast.lib
```

---

## Launch Innovus

```bash
innovus
```

---

# MMMC Setup

## Library Sets

### Setup Analysis

```text
max_timing → slow.lib
```

### Hold Analysis

```text
min_timing → fast.lib
```

---

## Delay Corners

```text
min_delay
max_delay
```

---

## Analysis Views

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

# Floorplanning

Parameters Used:

```text
Aspect Ratio = 1
Core-to-Die Boundary = 2.5
```

Purpose:

- Define Core Area
- Define Die Area
- Reserve Routing Resources

---

# Power Planning

## Power Ring

```text
Nets : VDD VSS

Top Layer    : Metal5
Bottom Layer : Metal5

Left Layer   : Metal6
Right Layer  : Metal6

Width   : 0.7
Spacing : 0.2
Offset  : 0.5
```

---

## Power Stripes

```text
Layer     : Metal6
Direction : Vertical

Width     : 0.7
Spacing   : 0.2
```

---

# Special Routing

```text
Special Route
Nets = VDD VSS
Follow Pins Enabled
```

Purpose:

Connect power rings and stripes to standard cells.

---

# Placement

Enable:

```text
I/O Pins
```

Run:

```text
Place → Standard Cell
```

Objectives:

- Minimize wirelength
- Reduce congestion
- Improve timing

---

# Pre-CTS Timing Analysis

Generate Timing Reports:

```text
Timing → Report Timing
```

Checks:

- Setup Slack
- Hold Slack
- Critical Paths

Requirement:

```text
Setup Slack > 0
Hold Slack > 0
```

---

# Clock Tree Synthesis (CTS)

## Create Route Type

```tcl
create_route_type -name clkroute \
-non_default_rule 2w2s \
-bottom_preferred_layer Metal5 \
-top_preferred_layer Metal6
```

---

## Clock Buffers

```tcl
set_ccopt_property buffer_cells {CLKBUFX2 CLKBUFX4}
```

---

## Clock Inverters

```tcl
set_ccopt_property inverter_cells {CLKINVX2 CLKINVX4}
```

---

## Clock Gating Cells

```tcl
set_ccopt_property clock_gating_cells TLATNTSCA*
```

---

## Generate CTS Specification

```tcl
create_ccopt_clock_tree_spec -file ccopt.spec
```

---

## Source CTS File

```tcl
source ccopt.spec
```

---

## Run CTS

```tcl
ccopt_design -cts
```

---

## Save Design

```tcl
saveDesign DBS/cts.enc1
```

---

# Routing

Perform:

```text
Global Routing
Detailed Routing
```

Enable:

```text
Optimize Wire
Optimize Via
```

Goals:

- Complete all connections
- Reduce congestion
- Meet DRC rules

---

# Filler Cell Insertion

```text
Physical Cell → Add Fillers
```

Enable:

```text
Do DRC
Fit Gap
```

Purpose:

- Remove spacing violations
- Improve manufacturability

---

# RC Extraction

Generate:

```text
SPF
SPEF
```

Command Flow:

```text
Timing → Extract RC
```

Purpose:

- Extract parasitic resistance
- Extract parasitic capacitance
- Improve timing accuracy

---

# Post-Route STA

Analyze:

- Setup Violations
- Hold Violations
- Worst Negative Slack (WNS)
- Total Negative Slack (TNS)

Timing closure is achieved when:

```text
WNS ≥ 0
TNS = 0
```

---

# Skills Demonstrated

- Verilog RTL Design
- ASIC Logic Synthesis
- SDC Constraint Development
- Static Timing Analysis (STA)
- Setup/Hold Analysis
- PVT Corner Analysis
- MMMC Configuration
- Floorplanning
- Placement
- Clock Tree Synthesis (CTS)
- Routing
- RC Extraction
- Timing Closure
- Physical Design using Cadence Innovus

---

# Repository Structure

```text
01_RTL_Synthesis
02_SDC_Constraints
03_PVT_Corner_Analysis
04_Static_Timing_Analysis
05_Post_Synthesis_Verification
06_Floorplanning
07_Power_Planning
08_Placement
09_CTS
10_Routing
11_RC_Extraction
12_Post_Route_STA
```

---

## Author

M. Ramya Sree  
B.Tech VLSI Design  
VIT Chennai

GitHub: https://github.com/ramya389
