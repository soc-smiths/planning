# Track 10 — FPGA Bring-up & Hardware Verification

## Status: DRAFT v1 — for team review
## Standard: Builds on Tracks 00–09. Industry depth throughout.

## Why this track exists
Everything so far has lived in simulation. This is where your Track 06
core, Track 07 protocols, and Track 08 SoC become an actual physical
system running on real silicon — and where an entire class of bugs
that simulation cannot show you (timing closure, signal integrity,
real-world electrical behavior) first appears. This track also
introduces the physical lab instrumentation skills real hardware
engineers use daily.

---

## Module 1 — From RTL to Bitstream: The FPGA Flow
1.1 What an FPGA actually is: LUTs, flip-flops, block RAM, DSP slices —
    the physical building blocks your synthesized design maps onto
1.2 The full flow, stage by stage: synthesis → technology mapping →
    placement → routing → bitstream generation — what each stage
    actually does to your Track 04/06 RTL
1.3 Why simulation-clean RTL can still fail on FPGA: introducing the
    real causes (timing closure, uninitialized state, clock domain
    issues that simulation didn't stress) that this whole track exists
    to address

## Module 2 — Choosing & Understanding Your Target FPGA
2.1 Reading an FPGA datasheet properly: LUT count, block RAM amount,
    I/O standards supported, clock resources (PLLs/MMCMs) — translating
    this into "can my design actually fit and run here"
2.2 Vendor toolchains landscape (Xilinx/AMD Vivado, Intel/Altera
    Quartus, open-source Yosys+nextpnr) — practical differences and
    why this project may target an open-source-friendly FPGA specifically
2.3 Resource estimation: mapping your Track 06 core + Track 08
    peripherals to a rough LUT/BRAM budget before attempting synthesis

## Module 3 — Constraints: Timing & Physical
3.1 Why constraints exist at all: the tool doesn't know your design's
    timing intent unless you tell it — direct continuation of Track 04
    Module 15's timing concepts, now applied for real
3.2 Writing SDC/XDC timing constraints: clock definitions, input/output
    delays, false paths and multi-cycle paths declared correctly and
    honestly (direct callback to Track 04 Module 15.3's warning about
    misusing these)
3.3 Pin/physical constraints: assigning your design's ports to actual
    physical FPGA pins based on the board's schematic
3.4 I/O standards: LVCMOS, LVDS, and why mismatching a pin's electrical
    standard against what a peripheral expects causes real, sometimes
    silent, hardware faults

## Module 4 — Synthesis & Implementation for FPGA, in Depth
4.1 FPGA-specific synthesis: how the same RTL that mapped to gates in
    Track 04's conceptual discussion instead maps to LUTs/flip-flops/
    DSP blocks here — genuinely different optimization goals than ASIC
    synthesis (Track 13)
4.2 Reading FPGA-specific synthesis reports: resource utilization,
    critical path, DSP/BRAM inference confirmation (did your Track 06
    multiply/divide unit actually infer a hardware DSP block, or did
    it synthesize into slow LUT logic by accident?)
4.3 Place & route: what's actually happening, and why routing
    congestion becomes a real, visible failure mode at high utilization

## Module 5 — Timing Closure on Real Hardware
5.1 Reading a post-implementation timing report properly: worst
    negative slack (WNS), total negative slack (TNS), failing endpoints
5.2 Real timing closure techniques: pipeline restructuring (direct
    application of Track 04 Module 6), retiming, physical constraint
    adjustment — actually fixing violations, not just re-running the tool
5.3 Clock domain crossing verification on FPGA specifically: why your
    Track 04/07 CDC synchronizers must be correctly constrained (set_
    false_path or equivalent) or the tool may try to time-close an
    inherently asynchronous path incorrectly

## Module 6 — Board Bring-up
6.1 Reading a board schematic and reference manual: power sequencing,
    reset circuitry, clock sources, connector pinouts — a genuine
    hardware-literacy skill most software-focused courses skip entirely
6.2 Programming the FPGA: JTAG bitstream download vs. configuration
    from onboard flash — the practical difference and when each is used
6.3 First bring-up checklist: power rail verification, clock presence
    verification, basic LED/GPIO sanity test before attempting anything
    complex — the real, disciplined order operations happen in industry

## Module 7 — Lab Instrumentation Skills
7.1 Multimeter fundamentals: continuity, voltage, and current
    measurement — verifying power rails and basic connectivity on a
    real board before assuming a "no bitstream" failure is a design bug
7.2 Oscilloscope fundamentals: reading a waveform display, setting
    trigger conditions, measuring signal timing/voltage on a real clock
    or GPIO signal — connecting an abstract "clock signal" concept to
    an actual physical waveform for the first time
7.3 Logic analyzer fundamentals: capturing multi-signal digital
    behavior (e.g. a real SPI or UART transaction) and comparing it
    against the protocol timing diagrams studied in Track 07 — closing
    the loop between "I designed this protocol controller" and "I can
    prove it behaves correctly on real wires"
7.4 When to reach for which tool: a genuine diagnostic decision
    process, not just tool trivia

## Module 8 — Running Your Own SoC on Real Hardware
8.1 Flashing your Track 08 SoC bitstream and connecting your Track 03
    GDB/OpenOCD flow to real silicon instead of a simulator — the exact
    moment every earlier track converges onto physical hardware
8.2 Running your Track 08 firmware test program on real hardware and
    observing real UART output, real GPIO toggling on a real LED
8.3 Diagnosing "works in simulation, fails on hardware" systematically:
    a genuine checklist connecting back to CDC (Module 5.3), timing
    closure (Module 5), constraint correctness (Module 3), and board
    bring-up (Module 6) — not guessing

## Module 9 — Debugging With On-Chip Instrumentation
9.1 Why external probing alone isn't enough for internal FPGA signals —
    introducing embedded logic analyzer IP (e.g. Integrated Logic
    Analyzer / SignalTap-class tools)
9.2 Inserting debug probes into your design without modifying its
    actual logic, and capturing internal signals (e.g. your Track 06
    core's internal pipeline registers) triggered on a real hardware
    condition
9.3 Correlating an on-chip capture with your Track 04/05 simulation
    waveforms — using both together as real engineers do, not treating
    them as separate skills

## Module 10 — Signal Integrity Awareness
10.1 Why a correctly-timed, correctly-routed design can still fail due
     to physical electrical effects: reflection, crosstalk, ground
     bounce — introductory literacy, not full SI engineering depth
10.2 Practical symptoms of SI issues at this project's scale (glitches
     visible on scope that don't match logical design intent) and when
     to suspect SI vs. a logic/timing bug

## Module 11 — Reproducibility & Bring-up Documentation
11.1 Writing a board bring-up log: what was tested, in what order,
     with what result — the same documentation discipline as Track 06's
     errata practice, applied to hardware bring-up specifically
11.2 Version-controlling constraint files and bitstream build scripts
     (direct application of Track 09's automation content to the FPGA
     flow specifically) so hardware results are reproducible by teammates
11.3 A hardware bring-up report as a real deliverable: what a new
     contributor needs to reproduce your team's working hardware setup

## Capstone Checkpoint for Track 10
A learner/team should be able to, unaided:
- Estimate FPGA resource requirements for their SoC before attempting
  synthesis, and read a post-synthesis utilization report correctly
- Write correct timing and physical constraints for their design
  targeting a real board
- Read a post-implementation timing report and apply a real fix
  (not just re-running the tool) to close a timing violation
- Perform a disciplined board bring-up sequence using a multimeter,
  oscilloscope, and logic analyzer appropriately
- Flash and run their actual SoC and firmware on real hardware,
  observing correct behavior via real UART/GPIO output
- Systematically diagnose a "works in sim, fails on hardware"
  scenario using a real checklist, not guesswork
- Use on-chip debug instrumentation to capture and correlate internal
  signals against simulation waveforms
- Produce a reproducible, version-controlled bring-up report
