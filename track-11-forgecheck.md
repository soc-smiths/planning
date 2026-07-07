# Track 11 — Forge Check

## Status: DRAFT v1

---

## Part 1 — Recall Round

1. Why can't firmware for a custom SoC simply reuse an existing vendor
   HAL?
   ✅ There is no vendor for a custom-designed SoC — the team's own
   register spec, memory map, and peripherals are unique and must be
   targeted directly.

2. What is a bootloader's fundamental job, distinct from an application
   or an RTOS?
   ✅ It's the earliest code that runs after reset, before any driver
   or RTOS exists — initializing minimal hardware and deciding what
   runs next (e.g. validating and jumping to an application image).

3. True or False: a BSP's purpose is to let application code remain
   unaware of SoC-specific memory map/clock details.
   ✅ True — the BSP isolates that hardware-specific knowledge so
   application code above it stays portable.

4. Why is interrupt-driven GPIO not automatically "better" than polled
   GPIO in every situation?
   ✅ Interrupt-driven reduces latency/CPU polling overhead but adds
   ISR complexity and context-switch overhead; polled can be simpler
   and sufficient for non-time-critical, infrequent checks — a real
   tradeoff, not a strict improvement.

5. What real problem does a ring buffer solve for an interrupt-driven
   UART RX driver?
   ✅ Decouples the ISR (producer, filling the buffer as bytes arrive)
   from the main-context consumer reading them, without needing the
   consumer to keep up instantly with every single byte.

6. True or False: retargeting printf to a UART on a custom SoC uses
   the exact same `_write` syscall stub mechanism taught generically
   in Track 03.
   ✅ True — same mechanism, now implemented against the team's actual
   UART driver and register interface.

7. Why does a consistent driver API shape (init/read/write/close)
   across UART, SPI, and I2C matter practically?
   ✅ Makes application code more protocol-agnostic and easier to
   reason about/reuse, and reduces onboarding friction for anyone
   working across multiple drivers.

8. What is the "hard part" of porting an RTOS to a new core,
   specifically?
   ✅ The port layer — particularly the context-switch routine, tick
   integration, and critical section implementation — since this is
   architecture-specific code, unlike most of the RTOS's portable core logic.

9. True or False: a context-switch routine can be written entirely in
   C without needing assembly.
   ✅ False — saving/restoring the full register set and switching
   stack pointers typically requires hand-written assembly, since C
   can't directly manipulate the processor's raw register state at
   that level.

10. Why would a firmware team specifically want to read their own
    core's actual mcause encoding when debugging a fault, rather than
    relying on generic RISC-V trap theory alone?
    ✅ The specific encoding values and any implementation-specific
    behavior are defined by their own core's actual hardware
    implementation (Track 06), which is the ground truth for
    interpreting a real fault on their SoC.

11. True or False: firmware should silently reset on a hard fault with
    no logging, since the watchdog will handle recovery anyway.
    ✅ False — a fault handler should ideally log/report state before
    any recovery action, since silent resets make real bugs
    undiagnosable; relying solely on the watchdog discards valuable
    debugging information.

12. Why does a change to the Track 08 register spec after firmware
    already depends on it require careful co-versioning?
    ✅ Firmware drivers are written against specific register
    offsets/behavior; an uncoordinated hardware change can silently
    break firmware that assumes the old behavior — versioning and
    communication prevent this.

---

## Part 2 — Applied Round

1. Your bootloader validates an application image via checksum before
   jumping to it. What real failure mode does this protect against,
   and what should the bootloader do if validation fails?
   ✅ Protects against jumping into corrupted or partially-written
   firmware (e.g. an interrupted update), which could execute garbage
   as instructions; on failure, the bootloader should avoid jumping and
   instead enter a safe recovery/reflash mode rather than proceeding blindly.

2. A GPIO interrupt handler in your firmware performs a lengthy
   computation directly inside the ISR, and the team notices the
   system becomes unresponsive to other interrupts. What earlier-track
   principle explains this, and how should the firmware be restructured?
   ✅ Track 01 Module 10's ISR-safety principle — ISRs should be short;
   the handler should instead set a flag/queue data and defer the
   heavy computation to a main-loop or task context.

3. After porting FreeRTOS, a task appears to never run despite being
   created successfully. List two realistic root causes specific to
   an RTOS port (not generic app bugs) you'd investigate first.
   ✅ e.g. Incorrect task priority configuration, or a bug in the
   context-switch/tick-integration port layer causing the scheduler to
   never actually switch to that task despite it being ready.

4. Your firmware experiences an illegal instruction trap. Walk through
   how you'd use your own core's mcause value and GDB to find the root
   cause, referencing your actual hardware rather than generic theory.
   ✅ Read the mcause value and cross-reference it against your Track
   06 core's actual encoding to confirm "illegal instruction," then use
   GDB (via your Track 10 on-chip debug connection) to inspect the
   faulting PC and disassemble the instruction there, checking whether
   it's corrupted memory, a bad jump target, or an unimplemented
   extension being used.

5. Your team's Track 08 register spec changes a status register's bit
   layout in a later hardware revision, but existing firmware drivers
   still assume the old layout. What's the safe process to handle this
   across the team, rather than one person silently updating hardware?
   ✅ Version the register spec change explicitly, communicate it to
   whoever owns firmware, update the generated C header from the new
   spec (Track 08 Module 2.4), and update/re-verify the affected
   drivers together — coordinated co-versioning, not a silent,
   uncommunicated hardware change.

---

## Part 3 — Practical Proof

**Task 1 — Bootloader**
Write a minimal bootloader for your team's SoC that initializes
memory, validates a checksum on a provided application image, and
jumps to it — demonstrating rejection of a deliberately corrupted image.

**Task 2 — BSP & GPIO driver**
Write a BSP for your SoC's actual memory map and a GPIO driver
supporting both polled and interrupt-driven usage, demonstrating a
working interrupt-driven LED toggle on real or simulated hardware.

**Task 3 — UART driver with printf retargeting**
Write an interrupt-driven UART driver with a ring buffer for RX, and
retarget printf to it, demonstrating real console output from your SoC.

**Task 4 — SPI/I2C driver**
Write a driver for one of your team's SPI or I2C peripherals following
the consistent init/read/write/close API shape, demonstrating a real
transaction against a provided peripheral model.

**Task 5 — FreeRTOS port**
Port FreeRTOS to your team's core, including a hand-written context-
switch routine in RISC-V assembly, and demonstrate two tasks running
preemptively with correct scheduling on real or simulated hardware.

**Task 6 — Fault debugging**
Given a provided firmware binary with a deliberately injected bug
causing a hard fault (in `/quizzes/track-11-fault`), use GDB and your
core's mcause encoding to identify the root cause.

**Task 7 — Integration demo**
Build a complete demo application under FreeRTOS exercising GPIO,
UART, and timer together, with a written integration test plan and
demonstrated passing results.

**Task 8 — Driver documentation**
Write API documentation for one driver built in this track, usable by
a teammate who has never seen its implementation.

---

## Completion state
Shown as: "Forge Check — Track 11: Cleared ✓" once all three parts
are attempted.
