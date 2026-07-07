# Track 11 — Applied Embedded Firmware & Drivers on Your Own SoC

## Status: DRAFT v1 — for team review
## Standard: Builds on Tracks 00–10. Industry depth throughout.

## Why this track exists
Track 01 taught embedded C in the abstract; this track applies it to
the exact chip your team built. This is where "I understand embedded
firmware concepts" becomes "I wrote the boot code, drivers, and RTOS
port for a real SoC that didn't exist before we designed it" — the
single most convincing proof of the project's whole premise.

---

## Module 1 — From Generic Embedded C to Your Own SoC
1.1 Why firmware for a custom SoC can't just reuse a vendor HAL —
    there is no vendor, your team is the vendor now
1.2 Reconciling every earlier assumption: Track 01's generic register
    structs, Track 02's ISA, Track 08's actual register maps — writing
    firmware against your own team's real, versioned register spec
1.3 Establishing the firmware repo structure: boot/, drivers/, hal/,
    rtos-port/, apps/ — clean separation matching Track 04 Module 19's
    project organization principles, now for firmware

## Module 2 — Bootloader Design & Implementation
2.1 What a bootloader's actual job is on your custom SoC: earliest
    code after reset, before any driver or RTOS exists
2.2 Reset vector and startup code specific to your Track 06 core —
    direct, concrete application of Track 01 Module 9's generic startup
    theory to your team's real memory map from Track 08
2.3 Memory initialization: copying .data, zeroing .bss, stack pointer
    setup — verified against your actual linker script (Track 03
    Module 4) for this SoC
2.4 Boot mode selection: how the bootloader decides what to run next
    (e.g. flash-resident app vs. a mode for loading new firmware over
    UART) — a real, deliberate design decision, not assumed
2.5 A minimal but real bootloader: initializes hardware, validates
    (checksums) an application image, jumps to it

## Module 3 — Board Support Package (BSP) Design
3.1 What a BSP actually is: the layer that knows about this specific
    SoC's memory map, clock configuration, and peripheral instances —
    isolating that knowledge from application code above it
3.2 Writing the BSP's hardware initialization sequence: clock/PLL
    setup if applicable, peripheral base address definitions generated
    from the Track 08 register spec (not hand-copied — direct
    continuation of Track 08 Module 2.4's single-source-of-truth principle)
3.3 Why a clean BSP boundary is what makes the same application code
    portable to a future revision of your team's SoC without rewriting
    everything

## Module 4 — GPIO Driver
4.1 Writing a real driver against your Track 08 GPIO register spec:
    direction configuration, read, write, interrupt-enable API
4.2 Interrupt-driven vs. polled GPIO usage — a genuine design choice
    with real tradeoffs (latency vs. CPU usage), not just "use interrupts"
4.3 Writing and registering the actual ISR (direct application of
    Track 01 Module 10's ISR-safety rules, now for a real interrupt
    routed through your Track 08 PLIC wiring)

## Module 5 — UART Driver
5.1 Polled vs. interrupt-driven vs. DMA-style buffered UART drivers —
    three real implementation tiers with genuine tradeoffs
5.2 Retargeting printf to your own UART driver (direct, concrete
    application of Track 03 Module 8.4's syscall-stub theory to your
    team's actual hardware, not a simulated example)
5.3 Ring buffer design for interrupt-driven RX: a real data structure
    problem (producer in ISR context, consumer in main context) tying
    directly back to Track 01 Module 10.4's race-condition warnings

## Module 6 — SPI & I2C Drivers
6.1 Writing a blocking SPI driver against your Track 08 SPI peripheral
    register interface, including correct mode/chip-select handling
6.2 Writing a blocking I2C driver against your Track 08 I2C peripheral,
    correctly handling the clock-stretching-aware status flags your
    team specifically designed in Track 08 Module 6.2
6.3 Designing a simple, consistent driver API shape across UART/SPI/
    I2C (init/read/write/close pattern) — an industry norm that makes
    application code protocol-agnostic where possible

## Module 7 — Timer Driver & System Tick
7.1 Configuring your Track 08 timer peripheral for a periodic system
    tick interrupt — the heartbeat that will drive Module 9's RTOS scheduler
7.2 Implementing a millisecond delay function and a simple uptime
    counter using the system tick — real, commonly-needed firmware primitives
7.3 PWM driver: exposing your Track 08 timer's PWM mode through a
    clean duty-cycle-setting API

## Module 8 — Interrupt Vector Table & Handler Registration
8.1 Building the real interrupt vector table for your SoC, wiring each
    PLIC-routed peripheral interrupt (Track 08 Module 7) to its actual
    handler function
8.2 A dynamic handler registration mechanism (function pointer table)
    vs. a hardcoded vector table — a genuine architectural choice with
    flexibility/simplicity tradeoffs
8.3 Nested interrupt handling policy for this specific core, revisiting
    Track 02 Module 13.5/Track 06 Module 8 now at the firmware level

## Module 9 — Porting an RTOS (FreeRTOS)
9.1 Why a project reaches for an RTOS instead of a bare superloop: real
    motivating scenarios (multiple independent timing-sensitive tasks)
9.2 What porting an RTOS to a new architecture actually involves: the
    port layer's job (context switch code, tick integration, critical
    section implementation) — the genuine hard part of an RTOS port
9.3 Writing the context-switch routine in RISC-V assembly for your
    specific core — direct, concrete application of Track 02 Module 17's
    assembly skills and Track 06's actual register/ABI implementation
9.4 Integrating the Module 7 system tick into FreeRTOS's scheduler tick
9.5 Writing and running your first two real FreeRTOS tasks on your own
    SoC, demonstrating actual preemptive multitasking on hardware you built

## Module 10 — Firmware Debugging on Your Own SoC
10.1 Using your Track 03 GDB/OpenOCD flow (now via Track 10's on-chip
     debug hardware) to debug firmware specifically — setting a
     breakpoint inside a driver, inspecting peripheral registers live
10.2 Diagnosing a hard fault / illegal instruction trap in your own
     firmware, using your own core's actual mcause encoding (Track 06
     Module 8) rather than generic trap theory
10.3 Debugging RTOS-specific failure classes: stack overflow in a task,
     priority inversion, a task never running (misconfigured priority
     or a bug in your Module 9 port layer)

## Module 11 — Firmware Reliability: Watchdog Integration & Fault Recovery
11.1 Wiring your Track 01 Module 16 watchdog theory to a real watchdog
     peripheral (if implemented in Track 08) or documenting it as a
     scoped-out feature — an honest decision either way, following
     Track 06's precedent for documented scope choices
11.2 Fault handler design: what firmware should actually do when a hard
     fault occurs (log state, safe reset) rather than silently hanging
11.3 Brown-out/reset-cause reporting: reading and reporting why the
     chip actually reset, using real reset-cause information if your
     SoC exposes it

## Module 12 — Application-Level Firmware & Integration Demo
12.1 Writing a real, complete demo application exercising GPIO, UART,
     timer, and (if implemented) SPI/I2C together under FreeRTOS —
     the practical, working proof that every track converged correctly
12.2 Structuring application code cleanly on top of the BSP/driver
     layers (Module 2–8) rather than touching registers directly —
     the payoff of the layered architecture built through this track
12.3 Writing an integration test plan for this demo firmware, applying
     Track 05's verification-planning discipline to firmware validation
     specifically (not just "it seems to work")

## Module 13 — Firmware Documentation & Versioning
13.1 Writing driver-level documentation: API reference, usage examples
     for each driver built in this track — the software-side
     counterpart to Track 08 Module 11's IP datasheets
13.2 Firmware/hardware co-versioning: what happens when the Track 08
     register spec changes after firmware already depends on it —
     applying Track 08 Module 11.3's IP versioning discipline from the
     firmware consumer's side this time

## Capstone Checkpoint for Track 11
A learner/team should be able to, unaided:
- Write a real bootloader and BSP for their team's own SoC from its
  actual register spec, not a generic example
- Write working GPIO, UART, SPI, and I2C drivers against their own
  hardware, using correct interrupt-safe patterns where applicable
- Port FreeRTOS to their own core, including a correct hand-written
  context-switch routine
- Run at least two real preemptively-scheduled tasks on real hardware
- Debug a real fault (hard fault, RTOS-specific bug) on their own SoC
  using GDB/OpenOCD and their core's actual trap/cause information
- Produce a complete demo application and integration test plan
  proving the whole system works together
- Produce firmware-side documentation usable by a teammate who didn't
  write the drivers
