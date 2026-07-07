# Track 10 — Forge Check

## Status: DRAFT v1

---

## Part 1 — Recall Round

1. What are the four main resource types an FPGA design maps onto?
   ✅ LUTs, flip-flops, block RAM, and DSP slices.

2. True or False: RTL that passes simulation is guaranteed to close
   timing on a real FPGA at the target clock frequency.
   ✅ False — simulation checks functional correctness only; timing
   closure depends on physical implementation (placement, routing,
   real gate/wire delays) not modeled by RTL simulation.

3. Why must timing constraints (clock definitions, I/O delays) be
   explicitly written rather than inferred by the tool?
   ✅ The tool has no inherent knowledge of the design's intended clock
   frequencies or external timing requirements unless explicitly told
   via constraints.

4. True or False: declaring a false path constraint is always safe as
   long as it makes a timing violation disappear.
   ✅ False — a false path must genuinely never be functionally
   exercised; misusing it to hide a real violation is a dangerous bug
   (same warning as Track 04 Module 15.3, now with real hardware
   consequences).

5. What does WNS stand for in a timing report, and what does a
   negative value indicate?
   ✅ Worst Negative Slack — the largest timing violation across all
   paths; negative means the design's slowest path fails to meet its
   timing requirement by that amount.

6. True or False: a CDC synchronizer's inherently asynchronous path
   should be timed normally by the FPGA timing tool like any other path.
   ✅ False — it should typically be constrained as a false path (or
   equivalent) since strict timing analysis doesn't meaningfully apply
   across an intentionally asynchronous boundary; the synchronizer
   itself, not timing closure, is what makes it safe.

7. Why would you check power rails with a multimeter before assuming a
   "board doesn't respond" issue is a design/bitstream bug?
   ✅ A basic hardware fault (bad power connection, wrong voltage) can
   look identical to a design failure from the outside; verifying
   fundamentals first avoids wasted debugging time chasing a
   non-existent logic bug.

8. What is the practical difference between what an oscilloscope and a
   logic analyzer are each best suited for?
   ✅ Oscilloscope: precise analog voltage/timing detail on a small
   number of signals (signal integrity, exact timing). Logic analyzer:
   capturing many digital signals simultaneously to observe protocol-
   level behavior over time.

9. True or False: if a multiply operation in your core's RTL doesn't
   explicitly get written to infer a DSP block, an FPGA synthesis tool
   might implement it in slow LUT logic instead.
   ✅ True — DSP inference depends on coding style matching patterns
   the synthesis tool recognizes; failing to do so can silently
   produce a much slower, LUT-based implementation.

10. Why is an Integrated Logic Analyzer (on-chip debug IP) needed for
    debugging internal FPGA signals, when external probes exist?
    ✅ Internal signals aren't physically accessible on external pins;
    on-chip debug IP captures internal state and can output/store it
    for inspection without requiring physical probe access.

11. True or False: signal integrity issues like ground bounce and
    crosstalk can cause hardware failures even when the design is
    logically and timing-correct.
    ✅ True — these are physical/electrical effects independent of
    logical correctness or timing closure.

12. Why does a hardware bring-up log matter for a team project
    specifically?
    ✅ Documents what was tested and in what order/result, making
    hardware results reproducible and debuggable by teammates rather
    than relying on one person's undocumented memory of what worked.

---

## Part 2 — Applied Round

1. Your design passes simulation and synthesizes cleanly, but after
   implementation, the timing report shows -1.2ns WNS on a path inside
   your Track 06 pipeline's forwarding logic. What are two realistic
   fixes, and why might simply constraining it as a false path be the
   wrong move?
   ✅ Realistic fixes: add a pipeline register to break up the
   combinational path (Track 04 Module 6 retiming/pipelining), or
   reduce logic depth in the forwarding mux chain; declaring it a false
   path would be wrong because this path is genuinely functionally
   exercised in real operation — hiding the violation would mean
   incorrect data could actually be latched on real hardware.

2. Your UART peripheral works correctly in RTL simulation but produces
   garbled output on real hardware. Walk through your systematic
   diagnostic order, referencing specific earlier-track concepts, rather
   than randomly changing things.
   ✅ Check: (1) board bring-up fundamentals — power/clock present via
   multimeter (Module 6/7); (2) constraint correctness — is the UART
   clock actually the frequency the baud generator assumes (Module 3);
   (3) pin/IO standard assignment correct for the physical UART lines;
   (4) capture the actual TX line with a logic analyzer and compare
   against the Track 07 UART timing diagram to see exactly where it
   diverges from expected framing.

3. A design uses a two-flop CDC synchronizer (Track 04 Module 11), but
   the FPGA timing tool reports a timing violation on the path feeding
   into the first synchronizer flop from the other clock domain. Is
   this violation meaningful, and what should actually be done?
   ✅ A raw cross-domain path is inherently asynchronous and strict
   setup/hold timing analysis doesn't meaningfully apply to it in the
   traditional sense; the correct fix is to properly constrain this
   specific path (e.g. as a false path or with an appropriate CDC-
   specific constraint) rather than trying to "time-close" it
   conventionally — but this must be done deliberately and only for
   genuinely asynchronous paths, not as a blanket excuse.

4. You capture your core's internal pipeline register values using an
   on-chip logic analyzer during a real hardware run, and the values
   don't match your Track 05/06 simulation waveform for the same
   input sequence. List two categories of things you'd check before
   concluding the RTL itself is wrong.
   ✅ e.g. Whether the actual input stimulus on hardware genuinely
   matches the simulated scenario (same instruction sequence, same
   timing), and whether the on-chip capture's trigger condition/timing
   offset is correctly aligned with what's being compared — a capture
   or comparison setup error can look identical to an RTL bug.

5. Your team observes an intermittent, seemingly random glitch on a
   high-speed signal captured on an oscilloscope, but simulation shows
   no logical reason for it. What category of issue should you now
   seriously consider, and why wouldn't simulation ever catch this?
   ✅ Signal integrity issues (crosstalk, reflection, ground bounce) —
   these are physical/electrical phenomena on the real board that
   logical RTL simulation has no model for at all, since simulation
   only represents logical/timing behavior, not real electrical effects.

---

## Part 3 — Practical Proof

**Task 1 — Resource estimation**
Given your Track 06 core and Track 08 peripheral set, estimate LUT and
BRAM usage before synthesis, then run FPGA synthesis and compare your
estimate against the actual utilization report.

**Task 2 — Constraints file**
Write a complete timing and pin constraints file for your SoC design
targeting a specific real or provided reference FPGA board.

**Task 3 — Timing closure**
Given a provided design with an intentional timing violation, identify
the failing path from the timing report, apply a real RTL-level fix
(not a false path), and confirm timing closure in a re-run.

**Task 4 — Board bring-up checklist**
Following a provided board bring-up checklist template, perform and
document power rail verification (multimeter), clock presence
verification (oscilloscope), and a basic LED/GPIO sanity test on real
or provided reference hardware.

**Task 5 — Logic analyzer capture**
Capture a real SPI or UART transaction from your Track 07/08 peripheral
running on hardware using a logic analyzer, and compare the captured
timing against the protocol's reference timing diagram, noting any
discrepancy.

**Task 6 — Real firmware bring-up**
Flash your full Track 08 SoC and Track 03-toolchain-compiled firmware
onto real hardware, connect via GDB/OpenOCD, and demonstrate real UART
output and GPIO toggling matching your simulation-based expectations.

**Task 7 — On-chip debug capture**
Insert an on-chip logic analyzer probe into your core's internal
pipeline signals, trigger a capture on a specific real hardware
condition, and correlate the captured values against a corresponding
simulation waveform for the same scenario.

**Task 8 — Bring-up report**
Write a complete, version-controlled hardware bring-up report
documenting your board, toolchain versions, constraint files, and
test results in enough detail for a teammate to reproduce your setup
from scratch.

---

## Completion state
Shown as: "Forge Check — Track 10: Cleared ✓" once all three parts
are attempted.
