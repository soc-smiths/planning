# Track 05 — Forge Check

## Status: DRAFT v1

---

## Part 1 — Recall Round

1. Why do real companies usually have a different engineer verify RTL
   than the one who wrote it?
   ✅ To avoid shared blind spots — the RTL author's mental model of
   "correct" is exactly what needs independent checking.

2. True or False: a bug found during RTL simulation is generally far
   cheaper to fix than one found after tapeout.
   ✅ True — post-silicon bugs can require an expensive, slow chip
   respin; simulation bugs just need a code/test fix.

3. What is the purpose of writing a verification plan before writing
   tests?
   ✅ Ensures test scenarios are derived deliberately from the
   specification (what matters), rather than only testing what's easy
   or obvious to write.

4. True or False: a testbench's scoreboard should need to read the
   DUT's internal signals to determine correctness.
   ✅ False — a well-architected testbench checks correctness from the
   DUT's observable interface behavior, not internals, keeping it
   robust to internal implementation changes.

5. What does a `rand` variable with a constraint block actually
   guarantee?
   ✅ Random values are generated but restricted to only those
   satisfying the specified constraints — legal, meaningful stimulus
   rather than arbitrary random values.

6. True or False: functional coverage and code coverage measure the
   same thing.
   ✅ False — functional coverage measures whether meaningful design
   behaviors/scenarios were exercised; code coverage measures whether
   lines/branches of RTL code were executed.

7. What is a covergroup used for?
   ✅ Defining what functional scenarios/values (coverpoints) and
   combinations (cross coverage) should be tracked and sampled during
   simulation.

8. True or False: a concurrent SVA assertion can only check a
   single-cycle condition, never a multi-cycle sequence.
   ✅ False — concurrent assertions can express temporal sequences
   (e.g. "if A then B within N cycles") using sequence/property constructs.

9. What is a reference model in a testbench, and why is it useful?
   ✅ An independent, trusted (often simpler/behavioral) model of
   expected DUT behavior used to check actual DUT output against —
   catches bugs without needing to hand-predict every expected result.

10. Why was UVM created as an industry standard?
    ✅ To standardize testbench architecture and methodology across
    companies/tools so verification components and practices are
    reusable rather than reinvented per project.

11. True or False: in UVM, `uvm_component` and `uvm_object` are
    interchangeable base classes with no real distinction.
    ✅ False — uvm_component represents persistent testbench structure
    (driver, monitor, etc.); uvm_object represents transient data
    (transactions, sequence items).

12. What is a VIP (Verification IP), in one sentence?
    ✅ A reusable, pre-built (often pre-verified) testbench component
    for a standard interface or protocol.

13. Why is seed management important in constrained-random verification?
    ✅ It allows a specific random test failure to be exactly
    reproduced later for debugging, since the same seed regenerates
    the same random sequence.

14. True or False: achieving 100% code coverage means a design has
    been fully verified.
    ✅ False — code coverage only shows code was executed, not that
    its behavior was checked against correctness or that all
    meaningful scenarios were exercised.

15. What's the first triage question to ask when a test fails: is it
    necessarily a DUT bug?
    ✅ No — it could be a DUT bug, a testbench bug, or an incorrect
    expectation in the reference model; all three must be considered.

16. True or False: formal verification replaces simulation entirely in
    modern verification flows.
    ✅ False — formal complements simulation, excelling at certain
    exhaustive proofs (e.g. deadlock freedom) but not scaling well to
    large datapath-heavy designs where simulation remains essential.

17. What new class of bug can gate-level simulation catch that pure
    RTL simulation typically cannot?
    ✅ Post-synthesis mismatches and timing/X-propagation issues that
    only appear once real gate delays and synthesis transformations
    are present.

---

## Part 2 — Applied Round

1. A constrained-random test fails once in a regression of 500 runs
   and never fails again. What is the correct next step, and what
   information do you need to make it reproducible?
   ✅ Capture and reuse the exact random seed from the failing run to
   re-run that specific stimulus sequence deterministically, rather
   than treating it as a one-off/ignoring it.

2. A functional coverage report shows 100% coverage on a covergroup,
   but a real bug was later found in that exact scenario. What does
   this suggest about how the covergroup was written?
   ✅ The covergroup likely captured that a scenario occurred but not
   that the correct behavior/output for that scenario was actually
   checked — coverage confirms stimulus reached a case, not that a
   checker verified correctness there.

3. Explain why a scoreboard comparing DUT output against a reference
   model is generally more scalable than a testbench with individually
   hand-written expected values for each test.
   ✅ A reference model automatically computes expected results for
   any input the constrained-random generator produces, scaling to
   effectively unlimited test scenarios, whereas hand-written expected
   values only cover the specific cases manually anticipated.

4. You write an SVA property asserting "request must be followed by
   grant within 4 cycles," and it fails during a test. Walk through
   how you'd determine whether this is a real DUT bug or an incorrect
   assertion.
   ✅ Check the actual design specification for the real timing
   requirement, examine the waveform at the failure point to see
   actual behavior, and determine whether the DUT genuinely violated
   the spec'd timing or whether the assertion encoded an incorrect
   assumption about allowed timing.

5. Why would a team building a UVM agent for a bus protocol want to
   make it protocol-generic and reusable now, even though only one
   test currently needs it?
   ✅ The same agent can be reused unchanged across many future tests
   and even future projects/protocol variants, avoiding rebuilding
   verification infrastructure repeatedly — direct payoff once Track
   07/08 protocols need verifying.

---

## Part 3 — Practical Proof

**Task 1 — Verification plan**
Given a provided small DUT specification (e.g. a FIFO), write a
verification plan listing at least 8 concrete test scenarios derived
from the spec, including at least 2 corner cases.

**Task 2 — Constrained-random stimulus**
Write a SystemVerilog class with randomizable fields and constraints
generating legal stimulus for the Task 1 FIFO (e.g. valid write/read
patterns respecting full/empty conditions).

**Task 3 — Functional coverage**
Write a covergroup for the FIFO capturing at minimum: full condition
hit, empty condition hit, and simultaneous read+write, with cross
coverage between relevant signals.

**Task 4 — SVA protocol checker**
Write a concurrent assertion checking that the FIFO never asserts
both `full` and `empty` simultaneously, and one checking a
request-to-grant timing rule for a provided handshake interface.

**Task 5 — Self-checking testbench**
Build a self-checking testbench for the Task 1 FIFO with a reference
model-based scoreboard, and demonstrate it catching a deliberately
injected bug (provided in `/quizzes/track-05-buggy-fifo`).

**Task 6 — Minimal UVM testbench**
Convert your Task 5 testbench into a minimal UVM structure (driver,
sequencer, monitor, scoreboard, environment, one test) for the same FIFO.

**Task 7 — Reproduce a seed failure**
Given a provided regression log showing a failure with a specific
random seed, re-run that exact seed and confirm you reproduce the
identical failure.

---

## Completion state
Shown as: "Forge Check — Track 05: Cleared ✓" once all three parts
are attempted.
