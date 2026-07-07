# Track 12 — Design-for-Test (DFT)

## Status: DRAFT v1 — for team review
## Standard: Builds on Tracks 00–11. Industry depth throughout.

## Why this track exists
A chip that works perfectly in simulation and on FPGA can still be
untestable in silicon manufacturing — DFT is how real ASIC teams
guarantee every manufactured chip actually works, not just the design.
This is rarely taught in student courses at all, despite being a hard
industry requirement for any real tapeout (Track 13).

---

## Module 1 — Why DFT Exists: The Manufacturing Test Problem
1.1 The fundamental problem DFT solves: silicon defects happen during
    manufacturing (not design bugs), and every single manufactured
    chip must be tested before shipping — functional simulation from
    Track 05 proves the design is correct, DFT proves each physical
    part is defect-free
1.2 Fault models: stuck-at faults as the primary abstraction used to
    reason about manufacturing defects, and why this simplified model
    is still industry-standard despite real defects being more complex
1.3 Controllability and observability: the two properties that make a
    circuit node testable at all, and why deeply-buried sequential
    logic (like your Track 06 core's internal pipeline registers) is
    inherently hard to control/observe from chip pins alone

## Module 2 — Scan Design Fundamentals
2.1 What scan insertion actually does: converting normal flip-flops
    into scan flip-flops with an added test mux, forming a shift
    register (scan chain) through the whole design
2.2 Scan chain operation: shift mode vs. capture mode, and the actual
    test sequence (shift in a pattern, capture one functional cycle,
    shift out the result) — connecting directly back to Track 04
    Module 13's DFT-awareness warnings about scan-unfriendly coding
2.3 Why the RTL coding rules from Track 04 Module 13.2 (no
    uncontrolled combinational feedback, no unmanaged gated clocks)
    exist specifically to make this module's scan insertion possible

## Module 3 — Automatic Test Pattern Generation (ATPG)
3.1 What ATPG tools actually do: automatically generating input
    patterns that will detect specific stuck-at faults via the scan
    chains from Module 2
3.2 Fault coverage: what percentage of possible faults your test
    patterns actually detect, and why 100% is rarely achievable or
    required — a real, honest industry number, not a pass/fail absolute
3.3 Reading an ATPG report: fault coverage percentage, pattern count,
    undetected fault categories and why each category exists (e.g.
    genuinely untestable redundant logic)

## Module 4 — Memory BIST (Built-In Self-Test)
4.1 Why scan chains alone don't adequately test embedded memories
    (your Track 04 Module 7 register files, any Track 10 block RAM
    usage) — memories need dedicated test algorithms, not just
    stuck-at pattern testing
4.2 March algorithms: the standard memory fault testing approach,
    conceptually explained (writing/reading specific patterns to catch
    memory-specific fault types)
4.3 MBIST controller architecture: a small hardware block that
    autonomously tests memory on-chip, and why this is preferred over
    external testing for embedded memories

## Module 5 — Boundary Scan (JTAG / IEEE 1149.1)
5.1 The board-level testing problem boundary scan solves: verifying
    chip-to-chip and chip-to-board connections after assembly, distinct
    from the internal logic testing scan chains (Module 2) provide
5.2 The JTAG TAP (Test Access Port) controller: TMS/TCK/TDI/TDO
    signals and the state machine that governs them — the same
    physical interface your team has been using for debug (Track 03/10)
    now understood from the test-infrastructure side
5.3 Boundary scan cells at every I/O pin, and how they enable testing
    board-level connectivity without any functional operation of the chip
5.4 Why JTAG serves double duty in real chips: debug access (Track 03)
    and manufacturing/board test (this module) sharing the same
    physical infrastructure — a genuinely elegant real design choice

## Module 6 — DFT Integration with the Design Flow
6.1 Where DFT insertion actually happens in the real flow: RTL →
    scan insertion → synthesis-with-scan → ATPG, and how this
    interacts with the synthesis flow from Track 04/13
6.2 The RTL-to-DFT handoff: what a design team must confirm before DFT
    insertion (clock/reset structure, absence of the module 2.3 coding
    violations) — a genuine gate/checklist in real tapeout schedules
6.3 Area and timing cost of DFT: scan flip-flops and test logic aren't
    free — a real, quantifiable PPA cost (direct connection to Track
    06 Module 14's PPA framework) that must be budgeted for, not
    treated as free insurance

## Module 7 — DFT Verification
7.1 Why DFT logic itself must be verified — a broken scan chain or
    MBIST controller means chips can't be tested at all, a
    manufacturing-blocking failure, not a minor bug
7.2 Simulating scan shift and capture operations to confirm scan
    chains are correctly connected before tapeout
7.3 Applying Track 05's verification methodology specifically to DFT
    logic: writing directed tests confirming scan chain integrity and
    MBIST controller correctness

## Module 8 — Applying DFT Concepts to Your Team's Core
8.1 Identifying scan-readiness issues in your team's actual Track 06
    core RTL, using the Module 2.3 checklist against real code (not a
    hypothetical example)
8.2 A documented, honest scope decision: full DFT insertion typically
    requires specific commercial EDA tooling not accessible to a
    student project — this module's realistic deliverable is a
    scan-readiness review and conceptual test plan for your core,
    following the same honest-scope-documentation precedent set in
    Track 06/07/10
8.3 What would change in your team's design if DFT insertion were
    performed, documented as a forward-looking analysis

## Capstone Checkpoint for Track 12
A learner/team should be able to, unaided:
- Explain the manufacturing test problem DFT solves, distinct from
  functional verification
- Explain scan chain operation (shift/capture) and why specific RTL
  coding rules exist to support it
- Read and interpret an ATPG fault coverage report
- Explain why embedded memories need MBIST rather than relying on scan
  chains alone
- Explain boundary scan's role and why JTAG serves both debug and test
  purposes in real chips
- Review their own team's core RTL for scan-readiness issues and
  produce an honest, documented scan-readiness assessment
- Explain DFT's real area/timing cost within a PPA framework
