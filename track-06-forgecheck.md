# Track 06 — Forge Check

## Status: DRAFT v2 — expanded to match syllabus v2

---

## Part 1 — Recall Round

1. What is the main performance limitation of a single-cycle CPU design?
   ✅ Clock period must accommodate the slowest instruction's full
   datapath delay, wasting time on every faster instruction.

2. True or False: Design Space Exploration means picking the first
   reasonable microarchitecture idea and implementing it directly.
   ✅ False — DSE means comparing multiple real candidate designs using
   actual estimated data before committing to one.

3. Why do real engineering teams study existing open-source cores
   before designing their own?
   ✅ No design happens in a vacuum — learning from proven, real
   architectural decisions (and their tradeoffs) informs better
   decisions than starting from pure intuition.

4. What is a RAW hazard?
   ✅ Read-After-Write — an instruction needs a value that a preceding,
   still-in-flight instruction hasn't yet written back.

5. True or False: forwarding (bypassing) can resolve every data hazard
   in a classic 5-stage pipeline without ever stalling.
   ✅ False — the load-use hazard specifically requires a stall.

6. Why does branch misprediction cost increase directly with pipeline
   depth?
   ✅ Deeper pipelines fetch more instructions speculatively before a
   branch resolves, so a misprediction discards more in-flight work.

7. What does a 2-bit saturating counter branch predictor improve on
   compared to a simple 1-bit predictor?
   ✅ It avoids flipping its prediction after a single "wrong-way"
   outcome — requires two consecutive contrary outcomes to change
   prediction, reducing mispredictions from occasional anomalies in
   otherwise consistent branch behavior.

8. What does a Branch Target Buffer (BTB) cache, and why?
   ✅ Predicted target addresses for branches, so the core can start
   fetching from the predicted target immediately rather than waiting
   to compute it, even for correctly-predicted-taken branches.

9. True or False: a Return Address Stack (RAS) predicts function
   return targets better than a generic branch predictor would.
   ✅ True — returns go to varying addresses (the call site), which a
   generic pattern-based predictor handles poorly; a dedicated stack
   mirroring call/return behavior predicts these accurately.

10. What does a direct-mapped cache's "tag" field actually verify?
    ✅ That the cached data at a given index actually corresponds to
    the requested address, not just any data occupying that cache line.

11. True or False: write-back and write-through are the same policy
    with different names.
    ✅ False — write-through writes to both cache and memory
    immediately; write-back writes only to cache and defers writing to
    memory until the line is evicted.

12. What does "superscalar" mean, and what extra hardware complexity
    does it require beyond a simple pipeline?
    ✅ Issuing more than one instruction per cycle; requires multiple
    execution/memory ports and dependency checking across
    simultaneously issued instructions.

13. True or False: out-of-order execution requires register renaming
    to function correctly.
    ✅ True — renaming removes false (WAR/WAW) dependencies between
    instructions so they can be reordered safely.

14. Why is it acceptable (and honest industry practice) for this
    project's core to target in-order single-issue rather than
    superscalar/out-of-order?
    ✅ It's a deliberate, documented scope decision appropriate to the
    project's learning goals and complexity budget — understanding
    what's excluded and why is itself a real engineering skill, not a
    gap to hide.

15. Why does riscv-arch-test passing not prove a pipelined core has no
    bugs?
    ✅ It verifies instruction-level ISA compliance but doesn't
    specifically stress pipeline hazard/timing interactions that only
    show up in certain instruction sequences.

16. What does PPA stand for, and why is "best design" not simply "the
    fastest one"?
    ✅ Power, Performance, Area — real designs must balance all three
    against product requirements; a faster design that's too large or
    power-hungry may not actually be the right choice.

17. True or False: a shipped CPU is expected to have zero known bugs
    (no errata) at release.
    ✅ False — virtually all real commercial CPUs ship with a
    documented errata list of known issues and mitigations; this is
    standard, not a sign of failure.

18. What is the purpose of a Debug Module (DM) built into a core,
    beyond simply having JTAG pins?
    ✅ Provides structured halt/resume/single-step/register-access
    capability integrated with the core's own execution state, which
    external probing of pins alone cannot provide.

---

## Part 2 — Applied Round

1. Given the sequence:
   # Track 06 — Forge Check

## Status: DRAFT v2 — expanded to match syllabus v2

---

## Part 1 — Recall Round

1. What is the main performance limitation of a single-cycle CPU design?
   ✅ Clock period must accommodate the slowest instruction's full
   datapath delay, wasting time on every faster instruction.

2. True or False: Design Space Exploration means picking the first
   reasonable microarchitecture idea and implementing it directly.
   ✅ False — DSE means comparing multiple real candidate designs using
   actual estimated data before committing to one.

3. Why do real engineering teams study existing open-source cores
   before designing their own?
   ✅ No design happens in a vacuum — learning from proven, real
   architectural decisions (and their tradeoffs) informs better
   decisions than starting from pure intuition.

4. What is a RAW hazard?
   ✅ Read-After-Write — an instruction needs a value that a preceding,
   still-in-flight instruction hasn't yet written back.

5. True or False: forwarding (bypassing) can resolve every data hazard
   in a classic 5-stage pipeline without ever stalling.
   ✅ False — the load-use hazard specifically requires a stall.

6. Why does branch misprediction cost increase directly with pipeline
   depth?
   ✅ Deeper pipelines fetch more instructions speculatively before a
   branch resolves, so a misprediction discards more in-flight work.

7. What does a 2-bit saturating counter branch predictor improve on
   compared to a simple 1-bit predictor?
   ✅ It avoids flipping its prediction after a single "wrong-way"
   outcome — requires two consecutive contrary outcomes to change
   prediction, reducing mispredictions from occasional anomalies in
   otherwise consistent branch behavior.

8. What does a Branch Target Buffer (BTB) cache, and why?
   ✅ Predicted target addresses for branches, so the core can start
   fetching from the predicted target immediately rather than waiting
   to compute it, even for correctly-predicted-taken branches.

9. True or False: a Return Address Stack (RAS) predicts function
   return targets better than a generic branch predictor would.
   ✅ True — returns go to varying addresses (the call site), which a
   generic pattern-based predictor handles poorly; a dedicated stack
   mirroring call/return behavior predicts these accurately.

10. What does a direct-mapped cache's "tag" field actually verify?
    ✅ That the cached data at a given index actually corresponds to
    the requested address, not just any data occupying that cache line.

11. True or False: write-back and write-through are the same policy
    with different names.
    ✅ False — write-through writes to both cache and memory
    immediately; write-back writes only to cache and defers writing to
    memory until the line is evicted.

12. What does "superscalar" mean, and what extra hardware complexity
    does it require beyond a simple pipeline?
    ✅ Issuing more than one instruction per cycle; requires multiple
    execution/memory ports and dependency checking across
    simultaneously issued instructions.

13. True or False: out-of-order execution requires register renaming
    to function correctly.
    ✅ True — renaming removes false (WAR/WAW) dependencies between
    instructions so they can be reordered safely.

14. Why is it acceptable (and honest industry practice) for this
    project's core to target in-order single-issue rather than
    superscalar/out-of-order?
    ✅ It's a deliberate, documented scope decision appropriate to the
    project's learning goals and complexity budget — understanding
    what's excluded and why is itself a real engineering skill, not a
    gap to hide.

15. Why does riscv-arch-test passing not prove a pipelined core has no
    bugs?
    ✅ It verifies instruction-level ISA compliance but doesn't
    specifically stress pipeline hazard/timing interactions that only
    show up in certain instruction sequences.

16. What does PPA stand for, and why is "best design" not simply "the
    fastest one"?
    ✅ Power, Performance, Area — real designs must balance all three
    against product requirements; a faster design that's too large or
    power-hungry may not actually be the right choice.

17. True or False: a shipped CPU is expected to have zero known bugs
    (no errata) at release.
    ✅ False — virtually all real commercial CPUs ship with a
    documented errata list of known issues and mitigations; this is
    standard, not a sign of failure.

18. What is the purpose of a Debug Module (DM) built into a core,
    beyond simply having JTAG pins?
    ✅ Provides structured halt/resume/single-step/register-access
    capability integrated with the core's own execution state, which
    external probing of pins alone cannot provide.

---

## Part 2 — Applied Round

1. Given the sequence:
   add x1, x2, x3
sub x4, x1, x5
Explain exactly which hazard exists, and how forwarding resolves it
without stalling.
✅ RAW hazard — `sub` needs x1's new value in its EX stage, but
`add` hasn't reached WB yet; forwarding routes the ALU result from
`add`'s EX/MEM pipeline register directly into `sub`'s EX stage
input, avoiding a stall.

2. Given:
   lw x1, 0(x2)
add x3, x1, x4
Explain why forwarding alone cannot resolve this, and what the
pipeline must do instead.
✅ The loaded value isn't available until the MEM stage completes,
but `add` needs it in its EX stage the very next cycle — there's no
completed value yet to forward; the pipeline must stall one cycle
before forwarding the load result.

3. Your team measures a 15% branch misprediction rate using a simple
always-not-taken predictor. Before implementing a 2-bit counter
predictor with a BTB, what data would you want to see to justify
the added area/complexity?
✅ Actual measured performance impact (CPI contribution) of that
misprediction rate on realistic workloads, and an estimate of how
much a better predictor would actually reduce it — justifying the
area cost against a real, quantified performance gain rather than
assumption.

4. A function is called recursively many times. Explain why a generic
2-bit branch predictor handles the return instruction poorly, and
why a RAS specifically fixes this.
✅ Returns jump to varying addresses depending on the call site, so
a pattern-based predictor (which predicts taken/not-taken or a
fixed target) can't track this; a RAS pushes the return address on
each call and pops it on each return, correctly predicting the
actual target each time.

5. Your core currently assumes single-cycle memory access. You're
asked to add a data cache. What pipeline-level change is required
to correctly handle a cache miss, and why?
✅ The pipeline must be able to stall on a miss until the cache line
fill completes, since memory access is no longer guaranteed to
complete in one cycle — requires stall/hazard logic extension
similar to the load-use stall mechanism.

6. Your team decides not to implement out-of-order execution for this
project. How would you justify this decision in a design review,
using PPA reasoning rather than just "it's too hard"?
✅ Out-of-order execution significantly increases area (reorder
buffer, renaming hardware, reservation stations) and design/
verification complexity for a performance gain that isn't
justified by this project's target use case and timeline — a
legitimate PPA/scope tradeoff, not an admission of inability.

7. Your core passes all functional tests but has no debug module and
no documented errata list at "completion." What's missing from an
industry-standard release, and why does it matter even for a
learning project?
✅ Missing structured debug/trace hardware (limiting real hardware
bring-up/debugging capability) and an honest errata list (standard
practice acknowledging known limitations) — both are expected
deliverables of a real core project, not optional polish.

---

## Part 3 — Practical Proof

**Task 1 — Case study analysis**
Read the source of a provided small open-source RV32I core (e.g.
PicoRV32), and write a short comparison: what design choices differ
from your team's planned microarchitecture, and why.

**Task 2 — Microarchitecture spec with DSE**
Write a microarchitecture specification document presenting at least
two candidate pipeline designs your team considered, with estimated
tradeoffs, and a documented decision with reasoning.

**Task 3 — Single-cycle datapath**
Implement a single-cycle datapath supporting at minimum R-type,
I-type, load/store, and branch instructions, and verify it executes a
provided small test program correctly.

**Task 4 — Pipeline hazard implementation**
Extend to a 5-stage pipeline implementing forwarding, load-use
stalling, and branch flush logic; demonstrate each with a targeted
directed test exercising exactly that hazard.

**Task 5 — Branch predictor**
Implement a 2-bit saturating counter branch predictor with a BTB, and
measure/report the misprediction rate on a provided benchmark compared
to a static always-not-taken baseline.

**Task 6 — Compliance testing**
Run riscv-arch-test against your core and report full results,
including any failures and root-cause analysis.

**Task 7 — Spike-checked random testing**
Build a constrained-random test flow that generates instruction
sequences, runs them on both your core and Spike, and reports any
mismatch with a minimal failing case isolated.

**Task 8 — PPA report**
Run your core through a provided synthesis flow far enough to obtain
area and estimated max frequency, and write a short PPA analysis
alongside your Task 6 CPI/performance data.

**Task 9 — Errata list**
Write an honest errata/known-limitations document for your team's
finished core, including at least one real known issue or deliberately
deferred feature and its practical impact.

---

## Completion state
Shown as: "Forge Check — Track 06: Cleared ✓" once all three parts
are attempted.
