# Track 13 — Forge Check

## Status: DRAFT v2 — updated to match syllabus v2 (install module added)

---

## Part 1 — Recall Round

1. Why is OpenLane run inside Docker rather than installed natively?
   ✅ Ensures reproducibility across every team member's machine,
   avoiding tool-version drift and "works on my machine" problems —
   the same container reasoning introduced in Track 00.

2. True or False: it's safe to skip running OpenLane's built-in
   example design and go straight to your own RTL, as long as the
   install commands completed without error.
   ✅ False — the built-in smoke-test design is the real verification
   that the entire toolchain (Docker, OpenLane, PDK) actually works
   correctly; install commands completing doesn't guarantee a working
   flow.

3. What does PDK_ROOT need to point to, and why does misconfiguring it
   cause real failures later?
   ✅ The location of the downloaded/configured PDK files; if
   misconfigured, later flow stages (synthesis, DRC, etc.) will fail
   or silently use wrong/missing process data.

4. What are the main stages of the RTL-to-GDSII flow, in order?
   ✅ Synthesis → floorplanning → placement → clock tree synthesis →
   routing → sign-off (DRC/LVS/STA) → GDSII generation.

5. What does a PDK actually contain?
   ✅ Standard cell library, device models, and manufacturing design
   rules for a specific fabrication process.

6. True or False: ASIC synthesis and FPGA synthesis optimize toward
   the same target with no real difference.
   ✅ False — ASIC synthesis maps to a standard cell library for a
   specific process; FPGA synthesis maps to fixed LUT/flip-flop/DSP
   fabric — genuinely different optimization targets.

7. Why can bad SDC constraints produce a poor netlist even from good RTL?
   ✅ Synthesis quality depends heavily on accurate constraints (clock
   definitions, timing intent); without correct constraints, the tool
   has no accurate target to optimize against.

8. What does floorplanning actually decide, at a high level?
   ✅ Die/core area, I/O pin placement, and macro placement — the
   physical layout blueprint before detailed placement.

9. True or False: a placer's only goal is minimizing wirelength, with
   no consideration of congestion.
   ✅ False — placers balance wirelength and congestion, since purely
   minimizing wirelength can create routing congestion elsewhere.

10. What is clock skew, and why does it matter more than clock latency
    for timing correctness?
    ✅ Clock skew is the difference in clock arrival time at different
    flip-flops; it directly affects setup/hold margins between
    communicating flops, whereas uniform latency (same delay
    everywhere) doesn't threaten timing the same way.

11. True or False: routing congestion can make an already-placed
    design genuinely unroutable.
    ✅ True — congestion can prevent successful detailed routing even
    after placement succeeded, sometimes requiring a placement or
    floorplan change to fix.

12. Why do post-layout STA numbers often differ from pre-layout timing
    estimates?
    ✅ Post-layout timing includes real physical parasitic delays
    (actual wire lengths/capacitance from placement and routing) rather
    than estimates used earlier in the flow.

13. What does DRC check, and why is a DRC violation a hard
    manufacturing problem, not just a style issue?
    ✅ Checks that the physical layout obeys the PDK's manufacturing
    design rules (minimum spacing/width, etc.); violating these means
    the fab may not be able to correctly manufacture that part of the
    chip at all.

14. True or False: LVS checks that the layout meets timing
    requirements.
    ✅ False — LVS (Layout vs. Schematic) checks that the physical
    layout actually implements the same circuit as the netlist/
    schematic, catching a different class of error (correctness of
    implementation, not timing).

15. What is IR drop, and why does it matter for a real chip?
    ✅ Voltage drop across the power distribution network under load;
    if too severe, cells may not receive adequate voltage to switch
    correctly, causing functional or timing failures.

16. What does a GDSII file actually contain?
    ✅ Physical shape/layer data — the literal geometric manufacturing
    instructions sent to a fab to produce photomasks.

17. True or False: a real tapeout sign-off checklist only needs timing
    closure, ignoring DFT and power.
    ✅ False — a genuine sign-off checklist includes timing closure,
    DRC/LVS cleanliness, power budget, and DFT review together.

---

## Part 2 — Applied Round

1. A teammate's OpenLane install completes with no visible errors, but
   they skip running the built-in example design and immediately run
   your team's core, which then fails partway through floorplanning.
   What's the correct first diagnostic step, and why?
   ✅ Go back and run the built-in smoke-test example design first —
   if it also fails, the problem is environment/install-level (PDK
   misconfiguration, Docker issue), not a bug in your team's RTL;
   skipping this step wastes time debugging the wrong layer.

2. Your synthesized netlist from Module 4 has a much larger area
   estimate than your Track 10 FPGA utilization report suggested for
   the "same" design. Explain why this comparison isn't actually
   apples-to-apples, referencing the real difference in target technology.
   ✅ FPGA resources (LUTs, fixed logic fabric) and ASIC standard cells
   are fundamentally different implementation technologies with
   different area/optimization characteristics; a raw numeric
   comparison between "FPGA utilization" and "ASIC cell area" doesn't
   translate directly — they're answering different questions about
   different physical substrates.

3. After placement, your STA report shows several new timing
   violations that weren't present in the pre-layout estimate from
   synthesis. What's the most likely explanation, and what physical
   stage would you investigate first?
   ✅ Real physical parasitics (wire delay from actual cell placement
   distances) weren't accounted for in the pre-layout estimate;
   investigate placement first — cells driving/driven by a violating
   path may simply be placed too far apart, and placement adjustments
   or a floorplan change may be needed.

4. Your design passes placement and CTS but fails detailed routing
   with significant congestion in one region. What are two realistic
   fixes, connecting back to earlier stages in this flow?
   ✅ e.g. Increase core area/reduce utilization density in the
   floorplan (Module 5) to give routing more room, or adjust placement
   constraints/re-run placement (Module 6) to spread congested cells
   more evenly — congestion fixes often require revisiting an earlier
   stage, not just re-running routing with different settings.

5. Your design passes DRC cleanly but fails LVS with several
   mismatches. Explain what this specifically indicates that a clean
   DRC result does not rule out.
   ✅ DRC only confirms the layout obeys manufacturing geometric rules;
   it says nothing about whether the layout actually implements the
   intended circuit. An LVS failure indicates the physical layout and
   the netlist have diverged (e.g. due to a flow error or manual
   layout edit), a real correctness bug DRC cannot catch.

6. Your power report shows significant IR drop in one region of the
   chip under heavy switching activity. What real physical design
   change would address this, and why?
   ✅ Strengthening the power grid in that region (e.g. adding
   additional power straps/wider power rails during floorplanning) to
   reduce resistance and voltage drop under load — an IR drop problem
   is fundamentally a power distribution network design issue, fixed
   at the floorplanning/power-planning stage, not by changing logic.

7. Your team is at the final tapeout sign-off meeting. One engineer
   says "timing is closed and DRC/LVS are clean, so we're ready."
   What's missing from a genuinely complete sign-off checklist per
   this track, and why does it matter?
   ✅ Power budget verification, DFT review (Track 12), and documented
   tool/environment versions (Module 2.8) are also required parts of a
   genuine sign-off checklist; timing/DRC/LVS alone don't confirm the
   chip is testable after manufacturing, that power delivery is
   adequate, or that the result is reproducible by the team.

---

## Part 3 — Practical Proof

**Task 1 — Environment install & smoke test**
Install Docker (if not already set up), install OpenLane, download and
configure the SKY130 PDK, and successfully run OpenLane's built-in
example design end to end. Document your tool/PDK versions and host OS
in the team repo.

**Task 2 — Real synthesis run**
Run your team's Track 06 core (or a suitable module) through Yosys via
OpenLane, and report the resulting cell count, area estimate, and
initial timing summary.

**Task 3 — Floorplanning & placement**
Run floorplanning and placement on your synthesized design in
OpenLane, and produce a written report of core utilization, aspect
ratio chosen, and a visual review of the resulting cell placement density.

**Task 4 — Clock tree synthesis**
Run CTS on your design and report the resulting clock skew numbers
from the CTS report.

**Task 5 — Routing**
Run detailed routing on your design, and report any congestion
warnings encountered — if congestion occurred, document what
floorplan/placement change (if any) resolved it.

**Task 6 — Post-layout STA**
Read your design's real post-route STA report and identify: worst
setup slack, worst hold slack, and the specific path(s) involved,
explaining what physical stage likely caused any violation found.

**Task 7 — DRC & LVS**
Run DRC and LVS on your completed layout using OpenLane's integrated
checks, and report full results — if any violations occur, document
your root-cause investigation and fix.

**Task 8 — Power report**
Generate and interpret a power report for your design, distinguishing
static and dynamic power contributions, and note any IR drop concerns.

**Task 9 — Generate real GDSII**
Complete the full flow and generate an actual GDSII file for your
team's design — the literal tangible deliverable of the entire curriculum.

**Task 10 — Final project report**
Write your team's complete final report: architecture summary
(Track 06), verification results (Track 05/06), FPGA bring-up results
(Track 10), firmware demo (Track 11), DFT review (Track 12), and final
ASIC implementation results (this track) — the single capstone
document proving every track connected into one real, working project.

---

## Completion state
Shown as: "Forge Check — Track 13: Cleared ✓" once all three parts
are attempted.
