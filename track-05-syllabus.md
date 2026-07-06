# Track 05 — Functional Verification (Industry Standard)

## Status: DRAFT v1 — for team review
## Standard: Builds on Track 04. Industry depth throughout.

## Why this track exists
RTL that "looks right" and RTL that's been proven correct are
different things — verification is how a real chip team closes that
gap before silicon, when a bug fix costs a respin instead of a
recompile. This track is taught immediately after RTL design so every
design from Track 06 onward is verified the way it's built, not
checked as an afterthought.

---

## Module 1 — What Verification Actually Is, and Why It's a Separate Discipline
1.1 Design vs. verification as distinct engineering disciplines in
    real companies — why the person who wrote the RTL usually isn't
    the one who verifies it (avoiding shared blind spots)
1.2 The cost-of-bug curve: why a bug found in simulation is cheap and
    a bug found in silicon is catastrophically expensive — this is the
    actual business reason verification exists
1.3 "Verified" vs. "tested" — the real distinction between running
    a few directed tests and having actual confidence in correctness

## Module 2 — Verification Planning
2.1 What a verification plan (test plan) is, and why writing it before
    writing tests prevents "testing what's easy" instead of "testing
    what matters"
2.2 Deriving test scenarios directly from a design specification —
    practiced against a design from Track 04
2.3 Defining pass/fail criteria and coverage goals up front

## Module 3 — Testbench Architecture Fundamentals
3.1 The role of each testbench component: generator, driver, monitor,
    scoreboard, checker — what each does and why they're separated
3.2 DUT (Design Under Test) isolation: why the testbench should never
    need to know DUT internals to check correctness
3.3 Directed testing vs. the limits that motivate everything else in
    this track (why directed-only testing doesn't scale to real designs)

## Module 4 — SystemVerilog for Verification (Language Deep Dive)
4.1 Classes, objects, and OOP in SystemVerilog — why verification code
    is written differently from design RTL
4.2 Randomization: rand/randc variables, constraint blocks
4.3 Mailboxes, semaphores, and events for testbench component
    communication and synchronization
4.4 Virtual interfaces: connecting class-based testbench code to
    signal-level DUT ports

## Module 5 — Constrained-Random Verification
5.1 Why pure randomization isn't enough — the need for constraints to
    generate legal, meaningful stimulus
5.2 Writing constraint blocks: basic constraints, weighted
    distributions, implication and conditional constraints
5.3 Randomization failures: what happens when constraints conflict,
    and how to debug an unsatisfiable constraint set
5.4 Balancing randomness with directed corner-case testing — real
    verification uses both, not one or the other

## Module 6 — Functional Coverage
6.1 What functional coverage measures, and how it's fundamentally
    different from code coverage (Module 12) — did we exercise the
    *behaviors that matter*, not just the lines of code
6.2 Covergroups, coverpoints, cross coverage — writing them against a
    real design's important scenarios
6.3 Reading a coverage report and identifying real coverage holes
    versus unreachable/don't-care cases
6.4 Closing the loop: using coverage holes to write new directed or
    constrained-random tests, not just reporting a percentage

## Module 7 — Assertion-Based Verification (SVA in Depth)
7.1 Immediate vs. concurrent assertions, revisited at full verification
    depth (builds on Track 04's introductory design-side assertions)
7.2 Sequences and properties: building temporal logic checks
    ("if A happens, B must happen within N cycles")
7.3 Assertions as both simulation checkers and formal-verifiable
    properties — the same assertion serving two purposes
7.4 Writing a protocol checker using assertions (directly reusable for
    Track 07's bus protocol verification)

## Module 8 — Building a Self-Checking Testbench, End to End
8.1 Scoreboards: comparing actual DUT output against expected output,
    architected properly (not just an if-statement pile)
8.2 Reference models: writing a simpler, trusted software model of the
    DUT's expected behavior to check against (directly connects to
    using Spike as a golden reference in Track 02)
8.3 Out-of-order and pipelined-design checking challenges — why
    scoreboards get harder as DUT complexity increases, and structural
    patterns for handling it

## Module 9 — UVM Fundamentals
9.1 Why UVM exists: standardizing testbench architecture across the
    entire industry so verification IP and methodology are reusable
9.2 UVM class hierarchy basics: uvm_component vs. uvm_object, and
    where each testbench piece fits
9.3 The standard UVM testbench structure: driver, sequencer, monitor,
    agent, environment, test — built up piece by piece
9.4 Sequences and sequence items: how stimulus is generated and
    delivered in UVM's structured way
9.5 The UVM factory and configuration database — why UVM testbenches
    are built to be overridden/reconfigured without editing base code
9.6 Phases: build_phase, connect_phase, run_phase — what runs when and why
9.7 Writing a complete, minimal UVM testbench for a simple DUT from
    Track 04, end to end

## Module 10 — Verification IP (VIP) Concepts
10.1 What a VIP is: a reusable, pre-verified testbench component for a
     standard interface/protocol
10.2 Why building protocol-agnostic, reusable agents now pays off
     directly in Track 07/08 when verifying real bus protocols
10.3 Structuring an agent (driver + monitor + sequencer) to be reused
     across multiple tests unchanged

## Module 11 — Regression & Verification Infrastructure
11.1 What a regression suite is and why running one test once is never
     considered "verified" in industry
11.2 Seed management for constrained-random tests — reproducing a
     failure from a random seed, a genuinely critical practical skill
11.3 Structuring test result logging so failures are diagnosable
     without re-running everything by hand
11.4 Introduction to running regressions via scripting (foreshadows
     Track 09's Python/TCL automation content)

## Module 12 — Code Coverage
12.1 Statement, branch, condition, toggle, and FSM coverage — what
     each actually measures at the RTL level
12.2 Why 100% code coverage does not mean a design is verified (the
     single most important lesson of this module) — and why functional
     coverage (Module 6) is what actually matters more
12.3 Using code coverage as a safety net for gaps functional coverage
     missed, not as the primary verification metric

## Module 13 — Debugging Verification Failures
13.1 Triaging a failing test: is it a DUT bug, a testbench bug, or a
     wrong expectation in the reference model? — a real, systematic
     process, not guessing
13.2 Waveform debugging at verification scale — navigating a complex
     testbench's signals, not just a single module (building on Track
     04 Module 20)
13.3 Using assertion failure messages effectively to jump straight to
     root cause instead of re-deriving it from waveforms alone

## Module 14 — Formal Verification, Applied
14.1 Revisiting Track 04's formal awareness with real application:
     using a formal tool to prove a property exhaustively rather than
     via simulation
14.2 Where formal genuinely outperforms simulation (deep corner cases,
     proving absence of deadlock) and where it doesn't scale (large
     datapath-heavy designs)
14.3 Writing a simple formal property for a small FSM or protocol
     checker and interpreting a proof/counterexample result

## Module 15 — Gate-Level Simulation Awareness
15.1 Why verification doesn't stop at RTL — a brief, honest look at
     gate-level simulation with timing, and what new bug classes it
     can catch that RTL simulation cannot (X-propagation issues,
     post-synthesis mismatches)
15.2 Where this fits in a real flow timeline relative to RTL
     verification (context-setting for Track 13, not implementation depth)

## Capstone Checkpoint for Track 05
A learner should be able to, unaided:
- Write a verification plan deriving test scenarios from a design spec
- Write constrained-random stimulus with meaningful, debuggable constraints
- Write functional coverage covergroups/coverpoints for a design's
  important scenarios and interpret a coverage report correctly
- Write concurrent SVA properties checking a protocol rule
- Build a self-checking testbench with a scoreboard and reference model
- Explain and construct the core pieces of a minimal UVM testbench
- Reproduce a random-seed test failure and triage whether it's a DUT
  bug, testbench bug, or wrong expectation
- Explain why 100% code coverage does not imply a verified design
- Interpret a basic formal verification proof/counterexample result
