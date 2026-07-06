# Track 04 — Forge Check

## Status: DRAFT v2 — expanded to match syllabus v2

---

## Part 1 — Recall Round

1. True or False: RTL that simulates correctly is guaranteed to
   synthesize correctly.
   ✅ False — simulation and synthesis are different domains; some
   constructs simulate fine but synthesize incorrectly or not at all.

2. In a combinational always block, what unwanted hardware results
   from an incomplete if/case statement (missing an else or default)?
   ✅ An unintended latch, since the output must "hold" its previous
   value when no branch matches.

3. True or False: non-blocking assignment (`<=`) should be used inside
   a purely combinational always_comb block.
   ✅ False — blocking assignment (`=`) is correct for combinational
   logic; non-blocking is for sequential (clocked) logic.

4. What's the key practical difference between a Moore machine and a
   Mealy machine?
   ✅ Moore machine outputs depend only on current state; Mealy machine
   outputs depend on current state and current inputs.

5. True or False: one-hot state encoding always uses less area than
   binary encoding.
   ✅ False — one-hot generally uses more flip-flops (one per state)
   but can simplify next-state/output logic and improve speed; it's a
   tradeoff, not a universal area win.

6. Why does an asynchronous reset crossing between clock domains need
   special handling (a reset synchronizer)?
   ✅ Reset assertion is instantaneous/async and safe, but reset
   de-assertion is itself an edge that can violate recovery/removal
   timing if it happens close to a clock edge in the receiving domain,
   creating the same metastability-class risk as CDC.

7. What are the three possible read-during-write behaviors for a
   memory, and why must one be deliberately chosen?
   ✅ Read old data, read new data, or undefined — different memory
   architectures/use cases require different guarantees; leaving it
   unspecified risks simulation/synthesis mismatch.

8. True or False: in a correct valid/ready handshake, `valid` is
   allowed to combinationally depend on the receiver's `ready` signal.
   ✅ False — this creates a combinational dependency loop between
   producer and consumer; valid must be generated independently of
   the current ready value.

9. What does "backpressure" mean in a valid/ready interface?
   ✅ The receiver signaling (via ready=0) that it cannot currently
   accept data, requiring the sender to hold/stall its output.

10. What problem does a two-flop synchronizer solve, and why doesn't
    it work for multi-bit buses?
    ✅ Solves metastability risk for single-bit CDC; for multi-bit
    buses, different bits could be sampled inconsistently across a
    transition, so handshaking or gray-coding is needed instead.

11. True or False: pipelining a design always improves both its
    latency and throughput.
    ✅ False — pipelining generally improves throughput (higher
    achievable clock frequency) but increases latency (more cycles
    for one operation to complete).

12. What does retiming mean, conceptually?
    ✅ Moving register boundaries within a design without changing its
    functional (input/output) behavior, typically to balance
    combinational delay and improve timing.

13. Why does DFT-friendly RTL avoid combinational feedback loops and
    unmanaged gated clocks?
    ✅ These patterns interfere with scan chain insertion/operation,
    making the design harder or impossible to properly test post-
    manufacturing.

14. True or False: declaring a false path in timing constraints is
    always a safe way to fix a timing violation.
    ✅ False — a false path exception should only be used when the
    path is genuinely never functionally exercised; misusing it can
    mask a real timing violation instead of fixing it.

15. What is a multi-cycle path, in one sentence?
    ✅ A timing path that is legitimately allowed more than one clock
    cycle to settle, because the design guarantees the receiving
    register won't sample it every single cycle.

16. True or False: assertions in design (SVA) code are only the
    verification team's responsibility, never the RTL designer's.
    ✅ False — design engineers commonly write basic assertions to
    capture their own design intent directly in the RTL.

17. What is the key conceptual difference between simulation and
    formal verification?
    ✅ Simulation tests specific scenarios/vectors; formal verification
    mathematically proves (or disproves) a property holds for all
    possible input sequences.

18. True or False: using `#` delays in RTL meant for synthesis is
    acceptable as long as simulation results look correct.
    ✅ False — delays are not synthesizable and have no meaning in real
    hardware; using them in design code (vs. testbenches) is a real bug
    source when simulation and hardware diverge.

19. What does "slack" mean in a timing report, at a basic level?
    ✅ The margin between the required time for a signal to arrive and
    the actual time it arrives; positive slack means timing is met,
    negative slack means a violation.

20. True or False: a lint tool's findings on your RTL should generally
    be treated as optional style suggestions.
    ✅ False — many lint findings (e.g. unintentional latch inference,
    incomplete sensitivity) indicate real functional bugs, not just style.

21. Why does a multi-person RTL team (like a 5–6 person student team)
    specifically benefit from a shared coding style guide?
    ✅ Reduces review friction and misunderstandings, makes code from
    different authors consistently readable, and avoids style-based
    debate replacing substantive review of correctness.

---

## Part 2 — Applied Round

1. Given this snippet:
   ```systemverilog
   always_comb begin
     if (sel == 2'b00) y = a;
     else if (sel == 2'b01) y = b;
   end
  What hardware problem does this create, and how would you fix it?
✅ Missing cases (sel == 2'b10/2'b11) mean y isn't assigned in every
branch, inferring an unintended latch; fix by adding an else branch
or default case assigning y a defined value.
A design works correctly in simulation but fails intermittently on
real hardware, and the failing signal crosses from a 50MHz domain
to a 100MHz domain with no special handling. What's the most likely
root cause?
✅ Missing clock domain crossing synchronization — metastability is
probabilistic and often doesn't show up in simulation but causes
real intermittent hardware failures.
A teammate writes an FSM using blocking assignment (=) inside an
always_ff block. Explain what could go wrong.
✅ Blocking assignment in sequential logic can cause signals to be
evaluated using already-updated values within the same clock edge
(rather than the pre-edge values), leading to simulation/synthesis
mismatches and incorrect next-state behavior in certain patterns.
Given a timing report showing -0.3ns slack on a specific path,
explain what this means and give one RTL-level reason this might
have happened.
✅ The path is violating its timing requirement by 0.3ns (arriving
too late); an RTL-level cause could be an overly long combinational
logic chain between two flip-flops with no pipelining.
A producer module sets valid = 1 and connects it directly from
ready & some_condition. Explain why this is a bug, not just bad
style.
✅ It makes valid combinationally dependent on ready, which (if the
consumer's ready also depends on the producer's valid) creates a
combinational loop — a real, often hard-to-diagnose bug in
handshake-based interfaces.
A synthesis tool reports a timing violation on a path the designer
knows is only ever sampled once every 4 clock cycles by design.
What timing constraint mechanism addresses this correctly, and what
is the risk of using it carelessly?
✅ A multi-cycle path constraint; risk is applying it to a path that
isn't actually guaranteed to be multi-cycle in all cases, which
would mask a genuine timing failure.
Why would a DFT engineer reject RTL containing a manually
instantiated gated clock with no scan bypass mux?
✅ Gated clocks can prevent scan chains from being clocked properly
during test mode unless a bypass/test-mode mux is provided,
breaking the ability to shift test patterns through the chain.

---

### Part 3 — Practical Proof

Task 1 — Fix the latch
Given a provided combinational always block with an incomplete
case statement, identify the inferred latch and rewrite it correctly.
Task 2 — FSM design
Design a Moore FSM (using the three-always-block style) for a provided
simple sequence-detector specification, with explicit illegal-state
handling.
Task 3 — Parameterized module
Write a parameterized N-bit wide multiplexer using a generate block,
and instantiate it at two different widths to demonstrate reuse.
Task 4 — Valid/ready handshake
Design a simple producer-consumer pair using valid/ready handshaking
with correct backpressure behavior, and write a testbench scenario
where the consumer deasserts ready mid-transfer to prove your producer
holds data correctly.
Task 5 — CDC synchronizer
Write a correct two-flop synchronizer for a single-bit signal crossing
between two clock domains, and explain in comments why each flop is
necessary.
Task 6 — Lint your own work
Run a provided lint tool against your Task 1–4 solutions, report every
finding, and fix any real issues found.
Task 7 — Waveform debugging
Given a provided testbench and a buggy RTL module (in
/quizzes/track-04-waveform), use a waveform viewer to identify the
clock cycle at which the design first produces incorrect output, and
explain what you observed.
Task 8 — Write an assertion
Given a provided handshake interface module, write a concurrent SVA
assertion that fires if valid is ever asserted while data is
unknown (X), and explain what design bug this would catch.


Completion state
Shown as: "Forge Check — Track 04: Cleared ✓" once all three parts
are attempted.
