# Track 13 — ASIC Flow Overview: RTL to GDSII

## Status: DRAFT v2 — for team review
## Standard: Builds on Tracks 00–12. Industry depth throughout.
## Practical anchor: OpenLane + SkyWater 130nm open PDK — a real,
## free, silicon-proven flow. This track is hands-on, not conceptual.

## Why this track exists
Every earlier track converges here. Your Track 06 core, verified in
Track 05, protocol-integrated in Track 07/08, running real firmware in
Track 11, timing-closed on FPGA in Track 10, and DFT-reviewed in Track
12 — this is where it becomes an actual physical chip layout. This
track is deliberately built around a real, free, open-source flow so
"ASIC design" stops being theory and becomes something your team
genuinely does, not just reads about.

---

## Module 1 — The ASIC Flow, End to End (Orientation Before Depth)
1.1 Full flow map: RTL → synthesis → floorplanning → placement →
    clock tree synthesis → routing → sign-off → GDSII — what each
    stage's real input and output actually is
1.2 Why ASIC synthesis optimization goals genuinely differ from FPGA
    synthesis (Track 10 Module 4.1) — standard cell libraries vs. fixed
    LUT fabric, and why this changes what "good RTL" even means at
    this stage
1.3 Introducing OpenLane as this track's real working environment:
    what it automates, and why using a real open-source flow (rather
    than only reading about a commercial one your team can't access)
    is the deliberate design of this entire track

## Module 2 — Installing & Verifying the OpenLane + SKY130 Environment
2.1 System requirements: disk space (the PDK and tool images are
    substantial, plan for real free space, not a token amount), RAM,
    and why this environment is genuinely heavier than anything run in
    earlier tracks
2.2 Why OpenLane runs containerized via Docker rather than being
    installed natively — reproducibility across every team member's
    machine, avoiding "works on my machine" tool-version drift (direct
    callback to Track 00 Module 11.4's container introduction)
2.3 Installing Docker itself (if not already set up from Track 00),
    verifying it runs correctly with a basic sanity-check container
    before touching OpenLane at all
2.4 Cloning the OpenLane repository and running its setup process step
    by step
2.5 Downloading and correctly configuring the SKY130 PDK: setting
    PDK_ROOT, selecting the correct PDK variant, and verifying the
    download completed without corruption
2.6 Running OpenLane's built-in example design (the standard "spm"
    test design included with the tool) as a smoke test — the
    mandatory gate before ever pointing the flow at your own RTL:
    if the example design doesn't run cleanly, nothing built on top
    of it can be trusted
2.7 Troubleshooting common installation failures, as a real diagnostic
    checklist rather than a hope-it-works step: Docker permission
    errors, PDK path misconfiguration, insufficient disk space, and how
    to read OpenLane's own error output to identify which of these it
    actually is
2.8 Documenting the working install (tool version, PDK version, host
    OS) in the team's repo — direct application of Track 09/10's
    reproducibility practices, since "it worked when I set it up" is
    not good enough for a 6-person team

## Module 3 — The PDK: What You're Actually Designing For
3.1 What a PDK (Process Design Kit) actually contains: standard cell
    library, device models, design rules — the physical manufacturing
    reality your design must conform to
3.2 SkyWater SKY130: why this specific open PDK exists, what real
    fabrication capability stands behind it, and what its process node
    characteristics mean practically (not just "130nm" as a number)
3.3 Standard cell libraries in depth: what a cell library actually is
    (pre-characterized logic gates, timing/power data per cell), and
    why synthesis tools choose cells from this fixed set rather than
    generating arbitrary transistor-level logic

## Module 4 — ASIC Synthesis, Properly
4.1 Logic synthesis targeting a standard cell library: technology
    mapping explained concretely — your Track 04-style RTL becomes a
    netlist of actual named standard cells, not abstract gates
4.2 Synthesis constraints for ASIC specifically: SDC files, clock
    definitions, and why constraint quality directly determines
    synthesis quality (garbage constraints produce a garbage netlist,
    even from good RTL)
4.3 Running your team's actual Track 06 core through Yosys (OpenLane's
    synthesis engine) and reading the real resulting synthesis report:
    cell count, area estimate, initial timing estimate
4.4 Comparing this ASIC synthesis report against your Track 10 FPGA
    synthesis report for the same RTL — a genuinely instructive,
    concrete comparison of the two target technologies

## Module 5 — Floorplanning
5.1 What floorplanning actually decides: die size, core area, I/O pin
    placement, macro (memory/large block) placement — the physical
    "blueprint" before detailed placement begins
5.2 Core utilization and aspect ratio: real tradeoffs between die size,
    routability, and timing — why cramming cells too densely causes
    real downstream routing failures
5.3 Power planning: power ring/grid structure — how power actually
    reaches every standard cell in the design, introduced here as a
    genuine physical concern, not an afterthought
5.4 Running floorplanning on your own design in OpenLane and reviewing
    the resulting floorplan visually

## Module 6 — Placement
6.1 Global placement vs. detailed placement — what each optimizes for
    and why they're separate stages
6.2 What a placer is actually trying to minimize: wirelength and
    congestion, and why these two goals sometimes conflict
6.3 Placement's real effect on timing: why moving a cell closer to
    what it drives can fix a timing path, connecting directly back to
    Track 04/10's timing concepts now at the physical placement level
6.4 Running placement on your design and visually reviewing cell density

## Module 7 — Clock Tree Synthesis (CTS)
7.1 The clock distribution problem: why a single clock signal can't
    just be wired directly to every flip-flop without careful design
    at scale — clock skew and its real consequences
7.2 Clock skew vs. clock latency — the critical distinction, and why
    skew (not latency) is what actually threatens setup/hold timing
    (direct callback to Track 04 Module 15's setup/hold fundamentals,
    now at real physical scale)
7.3 Clock tree structures: H-tree and buffer-inserted distribution,
    conceptually explained
7.4 Running CTS on your design and reviewing the resulting clock tree
    report for skew

## Module 8 — Routing
8.1 Global routing vs. detailed routing — the same coarse-then-fine
    pattern as placement, applied to wires
8.2 Routing layers and metal stack basics: why real chips route
    signals across multiple physical metal layers, and how vias
    connect between them
8.3 Routing congestion: what causes it, why it can make a design
    genuinely unroutable even after successful placement, and what
    floorplan/placement changes fix it (closing the loop back to
    Module 5/6's decisions)
8.4 Running detailed routing on your design in OpenLane and reviewing
    the routed layout

## Module 9 — Static Timing Analysis, at Full Post-Layout Depth
9.1 Revisiting Track 04/10's timing concepts now with real, physical
    parasitic-aware delays instead of estimates — why post-layout
    timing numbers differ from pre-layout estimates, sometimes significantly
9.2 Reading a real post-route STA report from your own design: setup
    and hold analysis, worst slack paths, and what physical stage
    (placement, routing, CTS) is implicated by different failure signatures
9.3 Timing signoff: what "closing timing" actually means as a real,
    final gate before a design is considered tapeout-ready

## Module 10 — Physical Verification: DRC & LVS
10.1 Design Rule Check (DRC): verifying your physical layout obeys the
     PDK's manufacturing design rules (minimum spacing, width, etc.) —
     why violating these means the fab literally cannot manufacture the
     chip correctly, not just a quality preference
10.2 Layout vs. Schematic (LVS): verifying the physical layout actually
     implements the same circuit as your netlist — catching a real,
     dangerous class of tool/flow error where layout and logic diverge
10.3 Running DRC and LVS on your own design's output using OpenLane's
     integrated checks (via Magic/Netgen) and interpreting real
     violation reports, not just "it passed"

## Module 11 — Power Analysis
11.1 Static (leakage) vs. dynamic power — what each depends on, and
     why a physical layout enables real, more accurate power estimation
     than RTL-level guesses
11.2 IR drop: why voltage actually drops across a real power grid under
     load, and why this matters for whether cells receive adequate
     voltage to switch correctly
11.3 Reviewing a real power report from your own design's implementation

## Module 12 — GDSII Generation & What It Actually Contains
12.1 GDSII as a file format: what it actually encodes (physical shapes
     per layer) — the literal manufacturing instruction set sent to a fab
12.2 Generating your team's actual GDSII file from the completed
     OpenLane flow — the tangible, final artifact of this entire
     14-track curriculum
12.3 What happens after GDSII in a real commercial flow: mask
     generation, wafer fabrication, packaging — brief, honest overview
     of what's genuinely beyond this project's scope (real fabrication
     cost/logistics), while noting that shuttle programs (e.g.
     efabless-style open shuttles) are the real-world path from this
     exact GDSII to actual manufactured silicon

## Module 13 — Sign-off Checklist & Tapeout Readiness
13.1 A genuine tapeout sign-off checklist: environment/tool versions
     documented (Module 2.8), timing closed, DRC clean, LVS clean,
     power within budget, DFT reviewed (Track 12) — pulling every
     earlier track's deliverable into one final gate
13.2 Why real tapeout decisions are a team consensus moment, not one
     engineer's call — connecting to this project's actual 6-person
     team structure

## Module 14 — Putting It All Together: Your Team's Complete Chip
14.1 Running your team's full Track 06 core (or Track 08's integrated
     SoC, if scope allows) through the complete OpenLane flow end to end
14.2 Producing the complete project deliverable: RTL → verified design
     → synthesized netlist → floorplanned, placed, clocked, and routed
     layout → DRC/LVS-clean GDSII — the literal, tangible proof that
     every one of the 14 tracks connected correctly
14.3 Writing the final project report: architecture, verification
     results, FPGA bring-up results, and final ASIC implementation
     results together — the capstone artifact for the whole curriculum

## Capstone Checkpoint for Track 13
A learner/team should be able to, unaided:
- Install and verify a working OpenLane + SKY130 environment from
  scratch, including passing the built-in smoke-test design, and
  troubleshoot a real installation failure using tool error output
- Run their own RTL through the real, free, open-source ASIC flow
  from synthesis through GDSII
- Explain what a PDK and standard cell library actually are and why
  synthesis is constrained by them
- Read and act on real floorplanning, placement, CTS, and routing
  reports from their own design
- Read a real post-layout STA report and distinguish which physical
  stage a given timing failure likely originates from
- Run and interpret real DRC and LVS checks, understanding why each
  is a hard manufacturing/correctness gate, not a formality
- Read a real power report and explain static vs. dynamic power and
  IR drop
- Produce an actual, real GDSII file for their own design
- Compile a complete tapeout sign-off checklist referencing every
  relevant earlier track (verification, FPGA bring-up, DFT)
