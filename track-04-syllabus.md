# Track 04 — RTL Design (Industry Standard)

## Status: DRAFT v2 — for team review
## Standard: Builds on Tracks 00–03. Industry depth throughout.

## Why this track exists
Writing Verilog that simulates correctly and writing Verilog that
synthesizes correctly, meets timing, survives DFT insertion, and passes
a real design review are very different skills. This track teaches RTL
the way a chip company expects it written — before Track 06 uses these
habits to build the actual core.

---

## Module 1 — HDL Fundamentals & Language Choice
1.1 What RTL (Register-Transfer Level) actually describes — data
    movement and transformation between registers, not "code that runs"
1.2 Verilog vs. SystemVerilog vs. VHDL — practical industry landscape,
    why this curriculum uses SystemVerilog for design (and full
    SystemVerilog later for verification in Track 05)
1.3 Simulation vs. synthesis: the critical mental model that some
    constructs behave differently (or aren't even synthesizable) in
    each domain — this distinction underlies almost every RTL bug class

## Module 2 — Combinational Logic, Done Correctly
2.1 assign statements and continuous assignment semantics
2.2 always_comb (and always @*) — correct sensitivity, avoiding
    accidental latches
2.3 Blocking assignment in combinational blocks — why it's required here
2.4 Common combinational mistakes: incomplete if/case branches creating
    unintended latches, explained with a real synthesized-schematic example

## Module 3 — Sequential Logic, Done Correctly
3.1 always_ff and clocked processes — what actually happens at each
    clock edge
3.2 Non-blocking assignment in sequential blocks — why it's required,
    and what breaks if blocking is used instead (a genuine, common bug)
3.3 Flip-flops vs. latches: what infers which, and why latches are
    almost always unintentional and unwanted in synchronous design
3.4 Reset strategies: synchronous vs. asynchronous reset — real
    tradeoffs (recovery/removal timing, reset tree cost, portability)
3.5 Reset polarity and naming conventions used industry-wide
3.6 Reset Domain Crossing (RDC): why asynchronous resets crossing
    between clock domains create the same class of danger as CDC, and
    the standard reset synchronizer circuit used to fix it

## Module 4 — Finite State Machines, Properly
4.1 Moore vs. Mealy machines — the real behavioral difference and when
    each is the right choice
4.2 The three-always-block (or two-always-block) FSM coding style used
    in industry, and why this specific structure is preferred for
    clarity and tool inference
4.3 State encoding strategies: binary, one-hot, gray — tradeoffs in
    area, speed, and glitch behavior
4.4 Safe FSM design: handling illegal/unreachable states explicitly
    rather than leaving them to chance

## Module 5 — Datapath Design: Building Real Functional Blocks
5.1 Multiplexers, decoders, priority encoders — RTL patterns for each
5.2 Adders: ripple-carry vs. carry-lookahead conceptually, and why
    synthesis tools usually make this choice for you (letting the
    tool infer arithmetic rather than hand-instantiating gates)
5.3 Comparators, shifters (logical/arithmetic/barrel shifter concept)
5.4 Building a simple ALU — directly foreshadowing the Track 06 core's ALU

## Module 6 — Pipelining & Retiming
6.1 Why combinational logic depth directly limits clock frequency —
    connecting straight back to Track 04 Module 12's timing concepts
6.2 Inserting pipeline registers to break long combinational chains,
    and the resulting latency-vs-throughput tradeoff
6.3 Pipeline hazards at the RTL-block level (conceptual introduction —
    full hazard/forwarding treatment is Track 06's core-specific job)
6.4 Retiming as a concept: moving register boundaries without changing
    functional behavior, and why synthesis tools can sometimes do this
    automatically (and when they can't)

## Module 7 — Memories in RTL
7.1 Modeling RAM/ROM behaviorally in RTL, and why the coding style
    matters for whether a tool infers a hardware memory macro
7.2 Single-port vs. dual-port vs. simple-dual-port memory — differences
    and use cases
7.3 Read-during-write behavior: the three real hazard scenarios (old
    data, new data, undefined) and why this must be deliberately chosen
7.4 Register files specifically: a hands-on connection to the RV32I
    register file needed in Track 06

## Module 8 — Parameterization & Reusable RTL
8.1 parameter and localparam — writing width/depth-configurable modules
    instead of hardcoding values
8.2 generate blocks: for-generate and if-generate for structurally
    repeated or conditional hardware
8.3 Why parameterized, reusable RTL is an industry expectation, not a
    nice-to-have — connects directly to writing genuinely reusable
    peripheral IPs in Track 08

## Module 9 — Interfaces & Module Composition
9.1 Module instantiation, port connection styles (positional vs. named
    — why named is the industry-preferred practice)
9.2 SystemVerilog interfaces: bundling related signals, and modports
    for direction control — reducing port-list sprawl in larger designs
9.3 Hierarchical design principles: partitioning a design into blocks
    with clean, well-defined boundaries

## Module 10 — The Valid/Ready Handshake (Industry-Standard Interface Pattern)
10.1 Why almost every real on-chip interface (and Track 07's bus
     protocols) is built on some form of valid/ready or valid/ack
     handshaking, rather than fixed-timing assumptions
10.2 Designing a correct valid/ready producer and consumer: the rule
     that valid must not depend combinationally on ready (a very common
     real bug that creates a combinational loop)
10.3 Backpressure: what it means for a downstream block to not be
     ready, and how upstream logic must correctly stall
10.4 Building a simple handshaking FIFO interface as a concrete example
     — direct groundwork for Track 07/08 peripheral interfaces

## Module 11 — Clock Domain Crossing (CDC) Fundamentals
11.1 Why CDC exists as a problem at all: metastability when a signal
     crosses from one clock domain to another
11.2 Two-flop synchronizers for single-bit signals — the standard
     solution, and why it doesn't work for multi-bit buses
11.3 Handshake-based and gray-code-based techniques for safely crossing
     multi-bit data (async FIFO concept introduced here)
11.4 Why CDC bugs are notoriously hard to catch in simulation and
     require dedicated static tools in real flows

## Module 12 — Low-Power Design Intent (Awareness Level)
12.1 Why power matters even in a learning-focused core project
12.2 Clock gating: the concept, why it saves power, and the
     metastability/glitch risks of doing it wrong by hand vs. using
     tool-inferred clock gating
12.3 Power domains and UPF (Unified Power Format) — conceptual
     awareness of what these are and where they fit in a real flow

## Module 13 — Design-for-Test (DFT) Awareness at the RTL Level
13.1 Why RTL coding style directly affects testability later (full DFT
     depth is Track 12 — this module is "write RTL that doesn't fight
     the DFT team")
13.2 Scan-friendly coding: avoiding combinational feedback loops,
     gated clocks without proper scan-mux insertion points, and
     asynchronous set/reset on every flop without a plan
13.3 Why every flip-flop in a real design typically needs to be
     scan-reachable, and what that constrains about reset/clock structure

## Module 14 — Synthesizable Coding Style (the Industry Contract)
14.1 The concept of a "synthesizable subset" — not everything legal in
     simulation can become hardware, and why that boundary exists
14.2 Constructs to avoid entirely in RTL meant for synthesis: delays
     (#), initial blocks (for design, not testbenches), certain loop
     constructs that don't map to hardware
14.3 Writing RTL that behaves identically in simulation and after
     synthesis — the single most important discipline this module teaches
14.4 Linting: running a lint tool against your own RTL and treating
     its output as a real gate, not a suggestion

## Module 15 — Introduction to Static Timing Concepts
15.1 Setup and hold time explained properly with a timing diagram
15.2 What a timing report shows at a conceptual level: slack, critical
     path — enough to read one, full STA methodology deepened in
     Track 10/13 context
15.3 Multi-cycle paths and false paths: what they are, why they exist,
     and why incorrectly declaring one is a real, dangerous bug class
     (masking a real timing violation rather than fixing it)
15.4 Why RTL coding style directly affects whether timing closure is
     even achievable later

## Module 16 — Assertions in Design Code (SVA Basics)
16.1 Why design engineers write basic assertions too, not just
     verification engineers — capturing design intent directly in RTL
16.2 Simple immediate and concurrent assertions: asserting a signal
     never goes X, a one-hot condition holds, a handshake protocol rule
     is never violated
16.3 How this connects forward to Track 05's much deeper
     verification-focused assertion methodology

## Module 17 — Formal Verification Awareness (Introductory)
17.1 What formal verification is, conceptually, as distinct from
     simulation — proving properties exhaustively rather than testing
     specific scenarios
17.2 Where lightweight formal tools fit into an RTL designer's own
     workflow today (e.g. proving a simple FSM has no unreachable
     deadlock state) — not full formal methodology, that's out of
     this project's immediate scope but genuinely useful to know exists

## Module 18 — Naming Conventions & Design Documentation
18.1 Industry-standard signal naming conventions (clk, rst_n, active-low
     suffixes, _i/_o/_reg suffixes) and why consistency matters at scale
18.2 Module and port documentation practices — header comments,
     interface documentation usable by someone who didn't write the code
18.3 Code review expectations specific to RTL: what a reviewer checks
     for beyond "does it simulate correctly"

## Module 19 — File Organization & Version Control for RTL Projects
19.1 Directory structure conventions for real RTL projects (rtl/, tb/,
     scripts/, docs/ separation) — connects back to Track 00 Module 11
19.2 Managing generated files (synthesis outputs, simulation artifacts)
     properly in .gitignore for a hardware repo specifically
19.3 Coding style consistency across a multi-person team (directly
     relevant with your 5 collaborators) — the case for a shared style
     guide and why it prevents real review friction

## Module 20 — Simulation & Basic Testbenches for RTL Designers
20.1 Writing a minimal self-checking testbench for a single module
     (full testbench architecture is Track 05's domain — this is the
     RTL designer's own sanity-check level)
20.2 Using a waveform viewer (GTKWave or equivalent) to visually debug
     signal behavior — a core daily-use skill
20.3 $display, $monitor, and basic assertions for quick self-verification

## Capstone Checkpoint for Track 04
A learner should be able to, unaided:
- Write correct, synthesizable combinational and sequential logic with
  no accidental latches
- Design a Moore or Mealy FSM using the industry-standard coding style
  with explicit illegal-state handling
- Write a parameterized, reusable module using generate blocks
- Design a correct valid/ready handshake interface, including proper
  backpressure handling
- Correctly synchronize a signal across clock domains using a two-flop
  synchronizer, and explain reset domain crossing
- Identify non-synthesizable constructs and DFT-unfriendly patterns in
  a piece of given RTL
- Run a lint tool on their own code and correctly interpret and fix
  at least one real finding
- Read a basic timing report, explain slack/critical path, and explain
  what a multi-cycle/false path exception is and its risk if misused
- Write a simple assertion capturing a design intent rule
- Write a minimal self-checking testbench and debug it using a
  waveform viewer
