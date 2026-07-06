# Track 01 — Embedded C & Bare-Metal Programming

## Status: DRAFT v2 — for team review
## Standard: Zero prior coding knowledge assumed. Industry depth throughout.

## Why this track exists
This is where "I've never written code" becomes "I understand exactly
what happens between typing C and a CPU executing instructions, with
no operating system to hide any of it" — and can write firmware the
way it's actually written in shipped products, not just toy examples.

---

## Module 1 — Programming Fundamentals in C (from zero)
1.1 What a program actually is — source code, compilation, execution
1.2 Variables, types (int, char, float, double), and how each is
    actually stored in memory (sizes, not just syntax)
1.3 Fixed-width types (`uint8_t`, `int32_t`, etc.) and why embedded code
    uses these instead of plain `int`/`char` almost everywhere
1.4 Operators: arithmetic, relational, logical, bitwise
1.5 Control flow: if/else, switch, for, while, do-while
1.6 Functions: declaration vs. definition, parameters, return values
1.7 Scope and lifetime: local, global, static — and why static behaves
    differently in embedded contexts

## Module 2 — Pointers & Memory, Properly
2.1 What a pointer actually is — an address, not a "special variable"
2.2 Pointer arithmetic, pointer-to-pointer, function pointers
2.3 Arrays vs. pointers — where they actually differ
2.4 const correctness, including const with pointers
2.5 Dynamic memory (malloc/free) and why bare-metal code usually avoids
    it (fragmentation, determinism, no OS to reclaim memory)

## Module 3 — Arrays, Strings, Structs, Unions
3.1 Arrays: declaration, indexing, multi-dimensional layout in memory
3.2 C strings: null termination, common functions, buffer overflow risk
3.3 Structs: memory layout, padding and alignment (critical groundwork
    for register-mapping later)
3.4 Unions: shared memory interpretation, real embedded use cases
3.5 typedef in hardware-facing code
3.6 Bit-fields in structs: how they're used for register modeling, and
    why relying on exact bit-field layout across compilers is risky
    (portability warning, explained with a real example)

## Module 4 — The Call Stack & Function Mechanics
4.1 How function calls actually work: stack frames, return addresses
4.2 Recursion and its real stack cost on memory-constrained targets
4.3 Pass by value vs. pointer/reference, and efficiency tradeoffs
4.4 Calling conventions & ABI basics: how arguments are actually passed
    in registers/stack (conceptual now, tied to real RISC-V ABI in
    Track 02)

## Module 5 — Numbers the Way Hardware Sees Them
5.1 Binary, hexadecimal, octal — fluent conversion
5.2 Signed vs. unsigned representation, two's complement explained
5.3 Integer overflow behavior and real consequences
5.4 Bit manipulation: masks, shifts, set/clear/toggle/check individual
    bits — used constantly in register access
5.5 Endianness: big vs. little, why it matters the moment you touch a
    peripheral register, a network packet, or share data across
    processors — with a concrete byte-layout example, not just a
    definition

## Module 6 — What "Bare-Metal" Actually Means
6.1 OS-hosted vs. bare-metal: what an OS normally provides and what's
    missing without one
6.2 What main() really is on a microcontroller — what calls it
6.3 The boot process from power-on to main()

## Module 7 — Memory Layout of an Embedded Program
7.1 Memory sections: .text, .data, .bss, .rodata, stack, heap
7.2 How the linker decides section placement (intro; full depth Track 03)
7.3 Stack/heap collision — a real embedded bug class, with an example
    of how to detect it (stack canaries, static analysis of stack depth)

## Module 8 — Volatile, Registers & Memory-Mapped I/O
8.1 Memory-mapped I/O: registers are just addresses
8.2 volatile: what it prevents, and real bugs from omitting it
8.3 Representing registers as structs in C
8.4 Safe bit-level register read/modify/write patterns

## Module 9 — Startup Code & the Vector Table
9.1 What startup/crt0 code does before main() runs
9.2 The vector table: reset handler, exception/interrupt vectors
9.3 Writing and tracing a minimal startup routine

## Module 10 — Interrupts & ISR-Safe Code
10.1 What an interrupt is at the hardware level (conceptual; full
     instruction-level detail in Track 02)
10.2 Writing an ISR safely — what can/cannot be done inside one
10.3 volatile and interrupts: shared-state bugs
10.4 Race conditions between main code and ISRs, with a concrete example
10.5 Interrupt priorities and nested interrupts — why priority
     inversion happens and how it's avoided
10.6 Critical sections: disabling/re-enabling interrupts safely, and
     the cost of holding them disabled too long

## Module 11 — Debugging Bare-Metal Code
11.1 Why print-statement debugging doesn't work the same with no OS —
     semihosting and UART-based debug output as alternatives
11.2 GDB fundamentals: breakpoints, stepping, inspecting memory/registers
11.3 Reading a stack trace/backtrace to find a crash location
11.4 Common failure symptoms and their usual causes (hard fault, stuck
     loop, silent hang) — a diagnostic mental model
11.5 Using a logic analyzer/oscilloscope alongside code-level debugging
     when the bug is timing-related (bridges to Track 10 lab skills)

## Module 12 — Common Embedded C Pitfalls (industry reality check)
12.1 Undefined behavior — what it is and why the compiler can do
     "anything," with real examples
12.2 Type punning and strict aliasing rules
12.3 Compiler optimization surprises (debug vs. release divergence)
12.4 Portability traps: assumed sizes/endianness that aren't guaranteed

## Module 13 — Coding Standards & Code Quality (industry-mandatory)
13.1 Why coding standards exist in embedded specifically — safety and
     certification context (aerospace, automotive, medical)
13.2 MISRA-C: what it is, why it's referenced constantly in industry
     job postings, key rule categories (not full compliance, but real
     understanding of the intent)
13.3 Static analysis tools (e.g. cppcheck, clang-tidy) — running one
     on your own code and interpreting its output
13.4 Code review practices specific to embedded: what reviewers look
     for (volatile misuse, magic numbers, unchecked bounds)
13.5 Documentation discipline: Doxygen-style comments, why undocumented
     register-level code becomes unmaintainable fast

## Module 14 — Floating Point & Math on Constrained Hardware
14.1 Software floating point vs. hardware FPU — what changes and why
     it matters for performance on smaller cores
14.2 Fixed-point arithmetic as an alternative — when and why it's used
     instead of floats on constrained/no-FPU targets
14.3 Common precision/rounding pitfalls in embedded math

## Module 15 — Inline Assembly & Compiler Intrinsics
15.1 When and why you'd drop into assembly from C (accessing
     instructions C can't express directly)
15.2 GCC inline assembly syntax, constraints, clobber lists — read and
     write basic examples
15.3 Compiler intrinsics as the safer alternative to hand-written asm

## Module 16 — Power, Watchdogs & System Reliability
16.1 Low-power modes: sleep/idle states and wake-up sources, conceptually
16.2 Watchdog timers: why they exist, how to "pet" one correctly, and
     what happens when firmware fails to
16.3 Brown-out detection and reset causes — reading why a chip actually
     reset (a real debugging skill, not trivia)

## Module 17 — Testing Embedded Code
17.1 Why traditional unit testing is harder without hardware — mocking
     hardware registers/peripherals in software
17.2 Structuring code (hardware abstraction layers) specifically to
     make it testable off-target
17.3 Introduction to a lightweight embedded testing approach (e.g.
     Unity/Ceedling conceptually) — enough to recognize and extend
     existing test setups later

## Capstone Checkpoint for Track 01
A learner should be able to, unaided:
- Write a C program correctly using pointers, structs, bit-fields, and
  bit manipulation
- Explain precisely what happens from power-on to main()
- Write a correct struct-based register map and safely read/modify/
  write individual bits using it
- Identify why code needs `volatile` just by reading it, and identify
  a race condition between an ISR and main code
- Explain the difference software vs. hardware floating point makes,
  and when fixed-point would be chosen instead
- Read and explain a short piece of inline assembly
- Run a static analyzer on their own code and correctly interpret at
  least one real finding
- Diagnose a simple crash using GDB and a backtrace
- Explain what a watchdog timer does and why it matters in shipped
  firmware
