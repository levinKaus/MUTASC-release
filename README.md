# MUTASC

**M**odular **U**rban **T**raffic **A**rchitecture and **S**imulation **C**ore (MUTASC, pronounced /ˈmuː.tæsk/).
A real-time urban traffic simulation built with Unreal Engine 5.8 and its Mass entity framework, developed as a
master's thesis artifact. This repository provides the packaged Windows build.

Vehicles use the Intelligent Driver Model (IDM) and MOBIL lane changes, pedestrians the Social
Force Model (SFM). Road networks are read from ASAM OpenDRIVE (.xodr) files. The simulation runs
at a fixed step of 1/60 s and is deterministic.

## Contributions

- **Authorable behavior:** vehicle behavior is a hierarchical state graph of condition and
  action primitives, edited at runtime in the in-game panel without code changes.
- **Negotiated conflict resolution:** conflicts between vehicles and between vehicles and
  pedestrians are derived from overlapping projected trajectories, without authored conflict
  points, and resolved by a joint right-of-way decision per group of interacting agents. This
  works for any intersection topology.

## Installation

Requirements: Windows 10 or 11 (64-bit), DirectX 12 GPU, about 1.1 GB disk space.

1. Download `MUTASC_Windows_<date>.zip` from the Releases page and extract it.
2. If the Visual C++ runtime is missing, run `Engine\Extras\Redist\en-us\vc_redist.x64.exe` once.
3. Start `MUTASC.exe`. Keep the folder structure; the executable needs the `MUTASC` and
   `Engine` folders next to it.

The executable is not signed. If SmartScreen blocks it, choose "More info", then "Run anyway".

## Usage

| Input | Action |
| --- | --- |
| W, A, S, D | Move camera |
| E or Space, Q or C | Up, down |
| Left or right mouse button and drag | Look around |
| `^` or backtick | Console |

The panel on the left has four tabs:

- **Scenario:** road network (generated networks from 10 to 2000 agents, single intersections,
  or an imported .xodr file), vehicles and pedestrians, behavior assignment, traffic lights,
  right-before-left, conflict evaluation, display options and frame rate limit.
- **Behaviors:** view the behavior graphs. Edit, Duplicate and New open a copy; Save writes it
  as JSON to `MUTASC\Saved\MUTASC\Behaviors`.
- **Stats:** the running configuration and live statistics.
- **Measurement:** recorded measurement runs.

The bottom bar holds Discard, Pause, "Apply & restart" (starts a new run with the settings)
and Exit. The cards at the top right explain the camera controls and the right-of-way markers
drawn above vehicles; both can be closed.

The default behavior is `BA_Urban`: car following, lane changes, negotiated right of way, and
the junction rules "do not enter a junction you cannot clear" and "yield to traffic with
junction priority". `BA_Example`, `BA_EntranceStop`, `BA_Multilane` and `BA_UrbanGiveWay` are
variants for comparison.

## Measurement

Measurement tab: set duration, warm-up and label, then "Start measurement". Each run restarts
the scenario. Options: a second run with conflict evaluation toggled, a window in simulated
instead of wall-clock seconds, and an engine CSV profile. The frame rate limit is lifted during
measurement and restored afterwards. Cancel with `mutasc.CancelMeasure` in the console.

Results are written to `MUTASC\Saved\MUTASC\Measurements` ("Open results folder" button):

| File | Content |
| --- | --- |
| `Summary.csv` | One row per run: network, agents, FPS (mean, min, 1% low), update time (mean, p99, max), conflict stage time, collisions, fallbacks, waiting, PET |
| `<run>_frames.csv` | Frame time and updates per frame |
| `<run>_updates.csv` | Stage times, candidate pairs and fallbacks per update |
| `<run>_pet.csv` | Post-encroachment time per resolved conflict |
| `<run>_trips.csv` | Completed trips on open networks |

## Command line and console

Example: `MUTASC.exe -MUTASCNetwork=Networks/City_500.xodr -MUTASCMeasure=60 -MUTASCWarmup=10`

| Option | Meaning |
| --- | --- |
| `-MUTASCNetwork=<file>` | Network, relative to the content folder or absolute |
| `-MUTASCVehicles=<n>` | Total agents including pedestrians (max 2000) |
| `-MUTASCPedestrians=<n>` | Pedestrians among them |
| `-MUTASCBehavior=/Game/Behaviors/<name>.<name>` | Behavior for all vehicles |
| `-MUTASCConflicts=0` | Conflict evaluation off |
| `-MUTASCSignals=1`, `-MUTASCRightBeforeLeft=1` | Traffic lights, right-before-left |
| `-MUTASCMeasure=<s>`, `-MUTASCWarmup=<s>`, `-MUTASCLabel=<text>` | One measured run, then exit |
| `-MUTASCSimulated=1` | Window in simulated seconds |
| `-MUTASCOut=<folder>` | Results folder |
| `-MUTASCParallel=0` | Sequential execution |
| `-MUTASCHashEvery=<n>` | Log a state hash every n updates |

Console commands: `mutasc.MeasureSeries <s> [warmup] [pair 0|1] [label] [simulated 0|1]`,
`mutasc.CancelMeasure`, `mutasc.DumpAgents`, `mutasc.UI 0|1`, `mutasc.DrawConflicts 0|1`,
`mutasc.DrawNetwork 0|1`, `mutasc.DrawBehavior 0|1`.

## Known limitations

- Right-hand traffic only, no pedestrian signals.
- Rare collisions when a vehicle changes lanes into a stopped queue.
- Spillback between neighboring intersections is not prevented; City_2000 shows long waits.
- Measured on the packaged build (Intel Core i9-10900, 10 cores / 20 threads, 32 GB RAM,
  Windows 11). Mean frame rate stays above 60 FPS through 1000 agents with no discarded
  simulated time; beyond the 500-agent design range it falls further - about 12 FPS at 1500
  agents and about 9 FPS at 2000 - and the simulation starts falling behind real time (about
  10.6 s discarded over a 60 s window at 1500 agents, about 23.6 s at 2000).

## Usage and source access

Permission is granted, free of charge, to download, install, copy, and run the packaged
MUTASC executable for any purpose, including research, education, and commercial use.

The source code is not publicly distributed. Researchers may request access to the full
MUTASC source code for research, reproducibility, or academic review by contacting
[support@levinkaus.de](mailto:support@levinkaus.de). Terms for source-code use, modification,
and redistribution will be provided with access.

Third-party components remain subject to their respective licenses.

© 2026 Levin Kaus. Rights not expressly granted are reserved.
