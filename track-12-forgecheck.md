# Track 12 — Forge Check

## Status: DRAFT v1

---

## Part 1 — Recall Round

1. What real-world problem does DFT solve that functional verification
   (Track 05) does not?
   ✅ Functional verification proves the design is logically correct;
   DFT proves each individually manufactured physical chip is free of
   manufacturing defects — a different problem entirely.

2. What is a stuck-at fault, in one sentence?
   ✅ A simplified fault model representing a circuit node permanently
   stuck at logic 0 or 1, used as the standard abstraction for
   reasoning about manufacturing defects.

3. True or False: controllability and observability both matter for a
   node to be testable.
   ✅ True — you must be able to set a node to a known value
   (controllability) and observe its resulting effect (observability)
   to test it.

4. What does scan insertion actually change about a normal flip-flop?
   ✅ Adds a test mux, converting it into a scan flip-flop that can be
   chained with others into a shift register for test purposes.

5. True or False: during scan shift mode, the chain operates as normal
   functional logic.
   ✅ False — shift mode moves test pattern bits through the scan
   chain; capture mode captures one functional clock cycle's result;
   these are distinct test-specific modes, not normal operation.

6. Why do the RTL coding rules against uncontrolled combinational
   feedback and unmanaged gated clocks (introduced in Track 04) matter
   specifically for this track?
   ✅ These patterns break scan chain shift/capture operation or
   prevent all flip-flops from being properly scan-reachable, directly
   blocking successful DFT insertion.

7. What does ATPG actually generate, and what does "fault coverage"
   measure?
   ✅ ATPG generates test patterns designed to detect specific stuck-at
   faults via scan chains; fault coverage measures the percentage of
   modeled faults those patterns actually detect.

8. True or False: 100% fault coverage is typically required and
   achievable for every real design.
   ✅ False — some faults (e.g. in genuinely redundant logic) may be
   undetectable regardless of patterns; near-100% with documented
   exceptions is the realistic industry norm.

9. Why can't scan chains alone adequately test embedded memories?
   ✅ Memory-specific fault types (e.g. cell coupling, pattern-
   sensitive faults) require dedicated test algorithms (march
   algorithms via MBIST), not just stuck-at pattern testing via scan.

10. What distinct problem does boundary scan (JTAG) solve compared to
    internal scan chains?
    ✅ Boundary scan tests board-level connectivity (chip-to-chip,
    chip-to-board connections after assembly), while internal scan
    chains test the chip's own internal logic.

11. True or False: JTAG is used only for manufacturing test, never for
    debug.
    ✅ False — the same physical JTAG infrastructure commonly serves
    both debug access (as your team has used in Track 03/10) and
    manufacturing/board-level test.

12. Why does DFT insertion have a real, non-negligible PPA cost?
    ✅ Scan flip-flops and additional test logic (muxes, MBIST
    controllers) add real area, and can affect timing paths — this
    must be budgeted for, not assumed free.

13. True or False: if scan chain RTL itself has a bug, it only affects
    test coverage percentage, not whether the chip can be manufactured
    successfully.
    ✅ False — a broken scan chain or MBIST controller can mean chips
    can't be tested at all post-manufacturing, which is a
    manufacturing-blocking failure, not a minor coverage reduction.

---

## Part 2 — Applied Round

1. Your team's core has a combinational feedback loop in one module
   (discovered during a Track 04 lint pass). Explain specifically why
   this would cause a real problem during scan insertion, beyond the
   general synthesis concerns already flagged in Track 04.
   ✅ Combinational feedback loops can prevent scan chain shift/capture
   from behaving predictably (the loop's state isn't cleanly
   controllable/observable the way scan requires), potentially making
   affected flip-flops untestable or breaking ATPG's ability to
   generate valid patterns for that region.

2. An ATPG report shows 94% fault coverage with a documented list of
   undetectable faults in redundant logic. Is this necessarily a
   problem, and what would you actually check before deciding?
   ✅ Not necessarily — check whether the undetected faults are
   genuinely in redundant/unreachable logic (a legitimate, expected
   gap) versus faults in functionally important logic that simply
   weren't covered by current patterns, which would need investigation
   or additional patterns.

3. Your team's core has embedded register files (memories). Why would
   relying only on scan-chain-based ATPG be insufficient to properly
   test them, and what should be added instead?
   ✅ Scan/ATPG targets stuck-at faults via general logic testing, but
   memories have distinct fault types (e.g. pattern sensitivity,
   coupling between cells) that require dedicated march-algorithm-based
   testing; an MBIST controller should be added for these memories.

4. A teammate says "we already have JTAG for GDB debugging, so we
   don't need to think about DFT/boundary scan separately." Explain
   why this reasoning is incomplete.
   ✅ The same JTAG physical infrastructure can serve both purposes,
   but debug access (halting/inspecting the core, Track 03/10) is a
   functionally different use case from boundary scan's board-level
   connectivity testing — having one doesn't automatically mean the
   other's specific test infrastructure (boundary scan cells at I/O,
   the 1149.1 TAP protocol support for board test) is implemented or
   verified.

5. Your team decides not to perform actual DFT insertion (since
   commercial tools aren't accessible) but still wants to demonstrate
   understanding. What's the appropriate, honest deliverable for this
   track, following the project's established scope-documentation
   precedent?
   ✅ A documented scan-readiness review of the actual core RTL
   (identifying any real coding issues that would block scan insertion)
   plus a conceptual test plan describing what DFT insertion would
   involve and what would change — an honest, real analysis rather
   than either skipping the topic or fabricating a full DFT flow
   without the tools to back it.

---

## Part 3 — Practical Proof

**Task 1 — Fault model exercise**
Given a provided small combinational circuit, identify at least three
possible stuck-at faults and describe what input pattern would be
needed to detect each (controllability) and how the effect would be
observed (observability).

**Task 2 — Scan-readiness review**
Review your team's actual Track 06 core RTL against the DFT-friendly
coding checklist (no uncontrolled combinational feedback, properly
managed gated clocks, consistent reset structure), and produce a
written scan-readiness report listing any real issues found.

**Task 3 — Scan chain simulation**
Given a provided small design with scan chains already inserted,
simulate a shift-in/capture/shift-out sequence and verify the captured
result matches the expected functional behavior for a given input pattern.

**Task 4 — ATPG report interpretation**
Given a provided sample ATPG report, report the fault coverage
percentage, categorize the undetected faults, and state which
categories (if any) warrant further investigation versus which are
expected/acceptable.

**Task 5 — MBIST conceptual design**
For one of your team's actual register files/memories, write a
conceptual MBIST test plan describing which march algorithm elements
would be applied and why.

**Task 6 — Boundary scan reasoning**
Given a provided board-level schematic showing two chips connected via
several signals, describe how boundary scan would be used to verify
those specific connections after board assembly, without either chip
running functional logic.

---

## Completion state
Shown as: "Forge Check — Track 12: Cleared ✓" once all three parts
are attempted.
