# Track 08 — Forge Check

## Status: DRAFT v1

---

## Part 1 — Recall Round

1. What distinguishes "IP" from "code that works in my testbench"?
   ✅ IP has a documented interface contract, register spec, dedicated
   verification suite, and a clean boundary allowing integration
   without needing to read the source — reusable and handoff-ready.

2. True or False: W1C (write-1-to-clear) means writing a 1 to a bit
   sets it.
   ✅ False — writing a 1 to a W1C bit clears it (typically used for
   interrupt status flags); the bit is otherwise set by hardware, not
   by software writes.

3. Why does every GPIO input need synchronization before use inside
   the peripheral's logic?
   ✅ External GPIO signals are asynchronous to the internal clock
   domain, creating the same metastability risk as any other CDC —
   requires the two-flop synchronizer technique.

4. True or False: clearing a W1C interrupt status bit can safely ignore
   the possibility of a new edge arriving at the same time.
   ✅ False — a race between clearing an old status and a new edge
   arriving must be handled correctly (e.g. the new edge should still
   result in a set status), or interrupts can be silently lost.

5. What does a timer's prescaler do?
   ✅ Divides a fast system clock down to a slower, usable timer tick
   rate.

6. True or False: a UART peripheral's TX and RX FIFOs are only useful
   for convenience, with no real functional necessity.
   ✅ False — FIFOs decouple firmware timing from real-time byte
   arrival/departure, preventing data loss when firmware can't service
   every single byte instantly.

7. Why would a design choose to reuse the Track 07 protocol monitor
   when verifying the Track 08 peripheral wrapper, rather than writing
   a new checker?
   ✅ The underlying bus/protocol behavior is unchanged; reusing the
   existing verified monitor avoids duplicated effort and leverages
   already-proven checking logic (VIP reuse principle).

8. What is the "claim/complete" sequence in the context of PLIC-based
   interrupt handling?
   ✅ The handler "claims" the highest-priority pending interrupt from
   the PLIC (identifying its source), handles it, then signals
   "complete" back to the PLIC to indicate that source can interrupt again.

9. True or False: interrupt priority assignment across peripherals is
   arbitrary and doesn't need justification.
   ✅ False — priority should reflect real system requirements (e.g.
   which events are more time-critical), and the choice should be
   documented and justified.

10. Why is SoC-level verification a genuinely different problem than
    verifying each peripheral individually?
    ✅ It exercises interactions between blocks (e.g. simultaneous
    events, shared bus contention, interrupt timing across multiple
    sources) that per-peripheral unit tests cannot expose.

11. True or False: running real compiled firmware against a full SoC
    in simulation is unnecessary if every peripheral individually
    passed its own testbench.
    ✅ False — this is exactly the kind of system-level, multi-block
    interaction testing that per-peripheral testbenches cannot cover.

12. Why does reset tree design matter at the SoC level beyond what was
    already handled per-module in Track 04?
    ✅ Multiple blocks (core, peripherals) must come out of reset in a
    correct, defined relative order at the system level; a
    module-level reset synchronizer alone doesn't guarantee correct
    system-wide reset sequencing.

13. What is the purpose of an IP datasheet, and who is its primary
    audience?
    ✅ Documents a peripheral's register map, timing, and integration
    behavior; primary audience is someone integrating or using the IP
    (e.g. a firmware engineer) without needing to read the RTL source.

14. True or False: register map changes to an existing peripheral IP
    can be made freely once firmware already depends on it.
    ✅ False — changes need careful, backward-compatible management
    since existing firmware depends on the current register behavior;
    breaking changes require coordinated updates.

---

## Part 2 — Applied Round

1. A GPIO peripheral's interrupt-status register uses W1C semantics.
   Firmware reads the status, sees bit 2 set, and writes back the same
   value to clear it — but a new edge on bit 2 arrives in between the
   read and the write. What could go wrong, and how should the
   hardware handle this correctly?
   ✅ If handled naively, the write could clear the status bit even
   though a new, distinct interrupt event just occurred, silently
   losing it; correct hardware design ensures a new edge occurring
   during/after the clear still results in the bit being set
   afterward (e.g. by having the set condition take priority over a
   simultaneous clear, or via proper edge-latch-then-clear ordering).

2. Your team wires the UART, timer, and GPIO interrupts all to the
   same PLIC priority level. What real problem could this cause during
   a scenario where multiple interrupts arrive simultaneously?
   ✅ With equal priority, the PLIC's tie-breaking (often lowest-ID-
   wins or similar) determines order arbitrarily from a design intent
   perspective — a genuinely time-critical event (e.g. a UART RX
   about to overflow) might not be serviced first if it happens to
   have a lower priority encoding, causing avoidable data loss.

3. Firmware writes real compiled code that toggles a GPIO output, and
   in SoC-level simulation, the output value changes one clock cycle
   later than the write instruction's completion. Is this necessarily
   a bug? What would you check first?
   ✅ Not necessarily a bug — check the register interface's actual
   design (e.g. an AHB-Lite pipelined write with a registered output),
   the register spec's documented timing, and the bus protocol's
   defined latency behavior before assuming this is wrong; it may be
   the correct/expected behavior of a pipelined bus write.

4. Two peripherals share a single clock domain, but a GPIO input pin
   is nominally "in the same domain" per the block diagram, yet still
   causes intermittent failures without synchronization. Why is
   synchronization still needed here?
   ✅ Even if internal logic shares a clock domain, an external GPIO
   pin's transitions are physically asynchronous to that clock (driven
   by an external signal with no relationship to the internal clock
   edges) — CDC synchronization is about the signal's origin, not
   just which internal domain it's used in.

5. A new firmware version needs an additional status bit in an
   existing, already-integrated UART peripheral's register map. What's
   the safe way to add this without breaking existing firmware/
   integration guide assumptions?
   ✅ Add the new bit in a previously reserved/unused bit position
   (not repurposing an existing bit), document it clearly with a
   version note, and update the IP datasheet — preserving backward
   compatibility for firmware that doesn't yet use the new bit.

---

## Part 3 — Practical Proof

**Task 1 — Register specification**
Write a complete register map specification (offsets, fields, access
types, reset values) for a GPIO peripheral, before writing any RTL.

**Task 2 — GPIO IP**
Implement the Task 1 GPIO peripheral in RTL, including correct input
synchronization and race-free W1C interrupt status handling, with a
directed and constrained-random verification suite.

**Task 3 — Timer/PWM IP**
Implement a timer peripheral with free-running, one-shot, and PWM
modes, including a prescaler, with verification covering rollover and
compare-equals-max edge cases.

**Task 4 — UART peripheral wrapper**
Wrap your Track 07 UART controller with a register interface including
TX/RX FIFOs and interrupt generation, and verify FIFO overflow/
underflow behavior explicitly.

**Task 5 — Interrupt aggregation**
Wire GPIO, timer, and UART interrupts to a PLIC model, assign and
justify a priority scheme, and write a system-level test verifying the
full claim/complete path for each source.

**Task 6 — Full SoC integration**
Integrate your Track 06 core, interconnect, and all Task 2–5
peripherals into one top-level design with a complete, documented,
non-overlapping address map.

**Task 7 — Real firmware on the full SoC**
Cross-compile a real C program (using your Track 03 toolchain) that
exercises GPIO, timer, and UART together, and run it against your
full SoC in simulation, demonstrating correct multi-peripheral behavior.

**Task 8 — IP datasheet**
Write a complete IP datasheet for one peripheral of your choice,
usable by a teammate who has never seen its RTL.

---

## Completion state
Shown as: "Forge Check — Track 08: Cleared ✓" once all three parts
are attempted.
