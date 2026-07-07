# Track 08 — Peripheral IP Design & SoC Integration

## Status: DRAFT v1 — for team review
## Standard: Builds on Tracks 00–07. Industry depth throughout.

## Why this track exists
This is where the core (Track 06) and the protocols (Track 07) stop
being separate exercises and become one working SoC. Every peripheral
built here is a real, reusable IP block — designed, documented, and
verified the way an IP team would hand it off to another team that's
never seen the source.

---

## Module 1 — What "IP Design" Actually Means
1.1 The difference between "code that works in my testbench" and "IP":
    a documented interface contract, a register spec, a verification
    suite, and a clean boundary someone else can integrate blind
1.2 Hard IP vs. soft IP vs. firm IP — where RTL-level peripheral
    designs like this project's fit, and why that matters for reuse
1.3 The IP handoff package: what a real IP deliverable includes
    (RTL, testbench, documentation, integration guide) — this becomes
    the template every peripheral in this track follows

## Module 2 — Register Specification & Design (the IP Contract)
2.1 Writing a register map specification before writing RTL — address
    offsets, field definitions, reset values, access types
2.2 Access type semantics precisely: RW, RO, WO, W1C (write-1-to-clear),
    RW1S — why each exists and the real hardware behavior each implies
2.3 IP-XACT and machine-readable register description formats — why
    industry tooling auto-generates RTL/docs/headers from one source
    of truth rather than hand-writing all three separately
2.4 Generating a C header (register struct, directly reusable from
    Track 01 Module 8) from the same register spec — one source, no
    drift between hardware and firmware definitions

## Module 3 — GPIO Controller, Fully Specified
3.1 Register map: data, direction, interrupt-enable, interrupt-status
    (W1C), pull-up/down configuration
3.2 Input synchronization: why every GPIO input needs the CDC
    synchronizer from Track 04 Module 11 before use internally
3.3 Edge/level interrupt detection logic, and correctly clearing
    W1C status bits without racing a new edge
3.4 Full design + directed and constrained-random verification against
    the register spec (applying Track 05 methodology to this exact IP)

## Module 4 — Timer/Counter IP, Fully Specified
4.1 Register map: count, compare/reload, control, status, interrupt
4.2 Free-running vs. one-shot vs. periodic modes — FSM design
    (Track 04 Module 4 applied directly)
4.3 Prescaler design: dividing a fast system clock to a usable timer
    tick rate, with the actual divider math
4.4 PWM generation as an extension of the same timer datapath — duty
    cycle register, compare logic, output toggle
4.5 Full verification: coverage of every mode transition and boundary
    (rollover, compare-equals-max edge case)

## Module 5 — UART Peripheral IP (Wrapping Track 07's Controller)
5.1 Wrapping the Track 07 UART TX/RX controller with a register
    interface: TX FIFO, RX FIFO, status flags (TX-empty, RX-full,
    framing/parity error), interrupt-enable
5.2 FIFO design: depth choice tradeoffs, full/empty/almost-full flags,
    and why almost-full matters for interrupt-driven firmware
5.3 Interrupt generation policy: on RX-not-empty, on TX-empty, on
    error — and how firmware (Track 11) will actually use each
5.4 Full verification including FIFO overflow/underflow edge cases

## Module 6 — SPI & I2C Peripheral IP (Wrapping Track 07's Controllers)
6.1 Register interface wrapping the Track 07 SPI controller: clock
    divider configuration, mode (CPOL/CPHA) configuration register,
    chip-select control, TX/RX data registers
6.2 Register interface wrapping the Track 07 I2C controller: target
    address register, R/W command register, clock-stretch-aware
    status flags
6.3 Full verification for both, reusing the Track 07 protocol monitors
    as the checking layer (direct reuse of VIP, not rewritten)

## Module 7 — Interrupt Aggregation: Wiring Peripherals to PLIC/CLINT
7.1 Connecting every peripheral's interrupt output (GPIO, timer, UART,
    SPI, I2C) to PLIC interrupt source lines — the real wiring exercise
    that makes Track 02 Module 14's PLIC/CLINT theory concrete
7.2 Assigning interrupt priorities across peripherals and justifying
    the chosen priority scheme (e.g. why a timer tick might outrank a
    UART byte-received interrupt, or vice versa, for this project's use case)
7.3 Verifying the full interrupt path end to end: peripheral event →
    PLIC → core trap → correct claim/complete sequence — a genuine
    system-level test, not a per-module unit test

## Module 8 — Bus Integration: Wiring It All Together
8.1 Connecting every peripheral IP (via its Track 07 bus interface) to
    the interconnect and address map designed in Track 07 Module 7
8.2 Final, complete SoC-level address map document — the single source
    of truth firmware (Track 11) will be written against
8.3 Top-level integration: core + interconnect + all peripherals +
    interrupt controller as one connected design, elaborated and
    checked for connectivity (no floating ports, no unconnected
    interrupt lines)

## Module 9 — SoC-Level (System) Verification
9.1 Why SoC-level verification is a genuinely different problem than
    per-peripheral verification: interactions between blocks, not just
    each block in isolation
9.2 Writing system-level test scenarios: e.g. "core writes to GPIO,
    triggers a UART transfer, timer interrupt fires mid-transfer" —
    real multi-peripheral interaction tests
9.3 Running actual compiled firmware (a real Track 01/11-style C
    program, cross-compiled with the Track 03 toolchain) against the
    full SoC in simulation — the point where every earlier track's
    output is exercised together for the first time
9.4 Reusing Spike/ISS-based reference checking (Track 02/05/06) at the
    system level where applicable, and where it stops applying once
    real peripheral timing is involved

## Module 10 — Low-Power & Clock/Reset Tree Integration at SoC Level
10.1 Clock distribution: single clock domain vs. multiple domains for
     different peripherals, and where the CDC synchronizers from
     Track 04/07 are actually needed at the system boundary
10.2 Reset tree: ensuring every peripheral and the core itself come out
     of reset in a correct, defined order — connecting to Track 04
     Module 3.6's reset domain crossing content at full system scale
10.3 Peripheral clock gating at the SoC level (building on Track 04
     Module 12) — which peripherals are realistic candidates and why

## Module 11 — IP Documentation & Handoff
11.1 Writing a complete IP datasheet for each peripheral: overview,
     register map, timing diagrams, interrupt behavior, integration
     notes — the actual deliverable a firmware engineer (Track 11)
     will read instead of the RTL source
11.2 Writing a top-level SoC integration guide: memory map, interrupt
     map, clock/reset requirements — onboarding documentation for
     anyone (including future SoC Smiths contributors) joining the project
11.3 Versioning IP blocks: why register maps need careful, backward-
     compatible change management once firmware depends on them
     (directly foreshadowing real firmware/hardware co-development pain)

## Capstone Checkpoint for Track 08
A learner/team should be able to, unaided:
- Write a complete register specification for a peripheral before
  writing any RTL, with correct access-type semantics
- Design, integrate, and fully verify a GPIO, timer/PWM, UART, SPI,
  and I2C peripheral IP, each wrapped in a clean register interface
- Wire every peripheral's interrupt correctly through a PLIC to the
  core, with justified priority assignment
- Produce a complete, non-overlapping SoC-level address map
- Run real compiled firmware against the full integrated SoC in
  simulation and observe correct multi-peripheral behavior
- Correctly reason about clock/reset tree integration at the SoC level
- Produce a complete IP datasheet and SoC integration guide usable by
  someone who has never seen the RTL
