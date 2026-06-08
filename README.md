# Fluid Automation – Automatic Conveyor System for Slot Plate Processing

## Overview

This project implements an **automatic conveyor system** that processes still (metal) plates and presses them into slots, managing production count and basket capacity. The control logic is implemented **twice** — once using **SFC/Grafcet** and once using **PLC Ladder Logic** — allowing a direct comparison of both programming paradigms.


## System Description

The system operates in two main automatic cycles:

1. **Cycle 1 – Plate Production & Conveyor Transport**
   - The operator sets the number of plates to produce before starting.
   - Three plates move along the first conveyor simultaneously, driven by a **timing law** (pulse timers, 3-second intervals).
   - A proximity sensor at the end of the conveyor counts crossing slots.
   - The cycle stops automatically when the preset plate count is reached.

2. **Cycle 2 – Pressing & Basket Loading**
   - Each plate is pressed for 3 seconds by a pneumatic piston.
   - Pressed slots are placed into a basket that holds up to **6 slots**.
   - When the basket is full, an **alarm activates** and the system halts.
   - The operator changes the basket and restarts the cycle by pressing **Start**.


## Hardware Components

### Pneumatic
- 5× Double-acting cylinders
- Non-return throttle valves
- 5/2-way directional solenoid valves (×5, controlled by 10 solenoids)
- Bidirectional sensors

### Electrical / PLC I/O
- Push buttons: **Start**, **Change Basket** (auxiliary contacts, NO)
- Limit switches (NO) — one per cylinder end position (used to simulate proximity sensors)
- Solenoid outputs for all directional valves


## Control Logic Implementation

### PLC – Ladder Logic

- `PlateNumbers`: preset counter value set by operator
- Two counters for Cycle 1: one for plate count on conveyor, one for proximity sensor detections
- Three pulse timers (`X1`, `X2`, `X3`) to trigger conveyor pistons every 3 seconds
- `stop_plate` flag halts Cycle 1; `StopCycle` / `StartCycle` flags manage cycle transitions
- Basket counter triggers alarm at value = 6; reset via `ChangeBasket` button
- **Note:** Stop button was not implemented in the PLC version

### SFC / Grafcet

- Cycle 1 main branch: 3 timer-driven steps for plate movement
- Transition to Cycle 2 when `cycle_count == preset` AND last plate detected
- Cycle 2 main branch: press step (3 s delay) → product counter increment → basket slot activation
- Change Basket branch: alarm → wait for `ChangeBasket` → reset → wait for `Start`
- Conveyor activation/deactivation steps at the start and end of each cycle
- **Stop button** implemented via a `Run` coil added to all SFC transitions


## Repository Structure

```
/
├── Conveyor/            # Ladder logic project files
├── Grafcet/             # SFC/Grafcet project files
└── README.md
```
## How to Run / Simulate
 
> Requires **Automation Studio**.
 
1. Open the project file in Automation Studio.
2. Set `PlateNumbers` to the desired production count.
3. Run the simulation and press **Start** to begin Cycle 1.
4. Once the basket fills (6 slots), press **Change Basket** to reset and restart.
   
## Key Concepts Demonstrated

- SFC / Grafcet sequential function chart design
- PLC Ladder Logic programming
- Pneumatic circuit design and solenoid valve control
- Counter and timer usage in industrial control
- Dual-cycle automation with interlocking conditions
- Alarm and operator interaction handling

## Notes

- The conveyor is simulated using cylinders and pistons; limit switches serve as proximity sensors.
- A maximum of 3 plates are allowed on Conveyor 1 at any time.
