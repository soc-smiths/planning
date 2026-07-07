# Track 06 — Designing Your Own RISC-V Core

## Status: DRAFT v2 — for team review
## Standard: Builds on Tracks 00–05. Industry depth throughout.

## Why this track exists
This is the centerpiece: everything in Tracks 01–05 converges here
into an actual, working RISC-V core — designed the way a real core
team designs one, meaning decisions are backed by data (PPA numbers,
case studies of real cores, verification results), not intuition.

---

## Module 1 — Core Architecture Planning & Design Space Exploration
1.1 Defining the target: which ISA variant and why — a real
    architectural decision, not a default choice
1.2 Single-cycle vs. multi-cycle vs. pipelined — fundamental tradeoffs
    before writing any RTL
1.3 Design Space Exploration (DSE) as real industry teams practice it:
    identifying 2–3 real candidate microarchitectures, and deciding
    between them using actual estimated data (cycle count, rough area/
    frequency estimate) rather than picking the first idea
1.4 Writing a microarchitecture specification document before coding,
    including a documented rationale for the rejected alternatives —
    industry practice: decisions are reviewed and recorded, not just made

## Module 2 — Case Studies: Reading Real Open-Source Cores
2.1 Why studying existing silicon-proven cores before building your own
    is standard practice, not "cheating" — no real engineer designs in
    a vacuum
2.2 Structured walkthrough of a minimal real core (e.g. PicoRV32 or
    similar small open RV32I core): architecture, coding style, how it
    handles what this track will build
2.3 Structured walkthrough of a more advanced real core (e.g. a
    pipelined design like VexRiscv or the Rocket core at a conceptual
    level): what changes at higher performance targets, and why
2.4 Extracting concrete design lessons from both — feeding directly
    into your team's own microarchitecture decisions in Module 1

## Module 3 — Single-Cycle Core: The Baseline
3.1 Datapath: PC, instruction memory, register file, ALU, data memory
3.2 Control unit: decoding opcode/funct3/funct7 into control signals
3.3 Implementing R-type, I-type, and load/store instructions end to end
3.4 Implementing branches and jumps: PC update logic, branch comparison
3.5 Why single-cycle is a poor final choice but an essential reference
    model — used later to cross-check the pipelined design's correctness

## Module 4 — Multi-Cycle Core
4.1 Breaking instruction execution into separate cycles — motivation
    and datapath changes
4.2 Adding the control FSM (application of Track 04 Module 4)
4.3 Instruction-dependent cycle counts and their real performance effect

## Module 5 — The Classic 5-Stage Pipeline
5.1 IF/ID/EX/MEM/WB stages defined precisely, with pipeline registers
5.2 Structural hazards and how this design avoids or handles them
5.3 Data hazards (RAW) with a concrete instruction sequence
5.4 Forwarding (bypassing): datapath additions, designed and justified
    stage by stage
5.5 Load-use hazard and required stall logic
5.6 Control hazards: branches/jumps and pipeline flush
5.7 Hazard detection unit: centralizing hazard logic cleanly

## Module 6 — Branch Prediction, In Depth
6.1 Why branch misprediction cost grows directly with pipeline depth —
    the real motivation for prediction, quantified
6.2 Static prediction schemes: always-not-taken, always-taken,
    backward-taken/forward-not-taken (BTFN) heuristic
6.3 Dynamic prediction: 1-bit and 2-bit saturating counter predictors,
    Branch History Table (BHT) design and indexing
6.4 Branch Target Buffer (BTB): caching predicted target addresses to
    avoid even the taken-branch penalty
6.5 Return Address Stack (RAS): a specialized predictor for function
    returns, and why generic branch prediction handles this poorly
6.6 Measuring real misprediction rate on your own core using Module
    13's performance instrumentation, and using that data to decide if
    a more advanced predictor is actually worth the added area

## Module 7 — Implementing the M Extension in Hardware
7.1 Multi-cycle multiply/divide implementation options (iterative
    shift-add vs. dedicated multiplier) and area/latency tradeoffs
7.2 Integrating a multi-cycle multiply/divide unit into the pipeline
    without breaking timing assumptions

## Module 8 — Exception & Interrupt Handling in the Pipeline
8.1 Precise exceptions: what "precise" means and why it matters
8.2 Handling an exception mid-pipeline: flushing younger instructions,
    saving correct PC/cause into CSRs
8.3 Implementing CSR read/write instructions and the minimum CSR set
    needed
8.4 Interrupt entry point: connecting an external interrupt input
    (foreshadowing PLIC/CLINT integration in Track 08)

## Module 9 — Memory Interface Design
9.1 Interfacing the core to instruction and data memory using the
    valid/ready handshake pattern
9.2 Handling variable-latency memory responses correctly
9.3 Byte/halfword/word access alignment handling

## Module 10 — Instruction & Data Cache Fundamentals
10.1 Why even a simple core benefits from a cache once memory latency
     is realistic (not the idealized single-cycle memory used so far)
10.2 Direct-mapped cache design: tag/index/offset addressing, hit/miss
     logic, explained and built at introductory depth
10.3 Cache line fill on a miss, and the stall behavior this requires in
     the pipeline
10.4 Write policies: write-through vs. write-back, at a conceptual
     decision level appropriate for this project's scope
10.5 Why this project may choose to keep memory simple (no cache) for
     v1 and add this as a documented future enhancement — a legitimate,
     honestly-recorded scope decision, not a gap hidden from the team

## Module 11 — Register File Design, Revisited
11.1 Dual-read, single-write register file design
11.2 Handling same-cycle write-then-read hazard — forwarding within the
     register file vs. external forwarding

## Module 12 — Beyond Single-Issue: Superscalar & Out-of-Order (Awareness)
12.1 What "superscalar" means — issuing more than one instruction per
     cycle, and the hardware complexity this actually requires
     (multiple ports, dependency checking across issued instructions)
12.2 What out-of-order execution solves, conceptually — reservation
     stations, register renaming, reorder buffers, at a genuinely
     conceptual level
12.3 Honest scope statement: why this project targets an in-order
     single-issue pipeline, and what real commercial cores (e.g.
     application-class processors) add beyond it — so the team
     understands what's deliberately out of scope and why, not
     mistaking simplicity for ignorance

## Module 13 — Verifying Your Own Core (Applying Track 05 for Real)
13.1 Writing a verification plan specifically for this core
13.2 Using Spike as the golden reference model for a scoreboard
13.3 Running riscv-arch-test against your own core
13.4 Writing targeted directed tests for pipeline hazards specifically
13.5 Constrained-random instruction sequence generation, checked
     against Spike

## Module 14 — Performance Analysis & PPA-Aware Decision Making
14.1 CPI measurement — instrumenting the core to report real statistics
14.2 Identifying the actual performance bottleneck using collected data
14.3 Running your core through synthesis (foreshadowing Track 13) far
     enough to get a real area and maximum-frequency estimate — turning
     "this design is better" into an actual number
14.4 Power-Performance-Area (PPA) as the three-way tradeoff real teams
     optimize against — explaining why the "best" microarchitecture is
     the one that fits the target product, not the fastest one in isolation

## Module 15 — CPU Debug & Trace Infrastructure
15.1 Why a shipped processor needs built-in debug hardware, not just
     external JTAG probing of pins — the RISC-V External Debug
     Specification conceptually introduced
15.2 Debug Module (DM), halt/resume/single-step support built into the
     core itself, and how this connects to Track 03's GDB/OpenOCD flow
     from the hardware side this time
15.3 Instruction trace concepts: why post-silicon bring-up and
     performance debugging need a trace port, at an introductory level

## Module 16 — RTL Reviews & Errata Management, CPU-Specific
16.1 What a design review specifically checks for in CPU RTL beyond
     general Track 04 review practices (hazard completeness, exception
     precision, CSR correctness)
16.2 Errata: how real commercial CPUs (virtually all of them) ship with
     known, documented bugs and mitigations — introducing the practice
     of maintaining an errata list for your own core rather than
     assuming a "finished" design has zero known issues
16.3 Structuring your team's own design review process given multiple
     collaborators — checklist-driven review, not just "looks fine to me"

## Module 17 — Design Iteration & Documentation
17.1 Writing a microarchitecture document describing the finished core
17.2 Documenting known limitations and deliberately deferred features
     honestly (directly connects to Module 10.5 and Module 12.3's scope
     decisions, and Module 16.2's errata list)
17.3 Preparing the core for the next tracks: what interface contracts
     must stay stable for Track 07/08 to build on top of it

## Capstone Checkpoint for Track 06
A learner/team should be able to, having built this together:
- Justify their chosen microarchitecture using actual DSE data and
  lessons drawn from studying real open-source cores
- Explain every hazard class in their pipeline and point to the exact
  RTL that resolves each
- Explain their branch predictor choice with real measured
  misprediction data, and justify why (or why not) a more advanced
  predictor would be worth it
- Demonstrate their core passing riscv-arch-test compliance tests
- Demonstrate a constrained-random test suite checked against Spike
- Report real CPI and synthesis-based area/frequency data, and discuss
  the PPA tradeoff their design represents
- Explain what debug/trace infrastructure their core has (or explicitly
  lacks, with reasoning) and how it connects to Track 03's GDB flow
- Produce a microarchitecture document including an honest errata/
  known-limitations list, reviewed by the whole team
