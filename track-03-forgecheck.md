# Track 03 — Forge Check

## Status: DRAFT v1

---

## Part 1 — Recall Round

1. True or False: "the toolchain" refers to a single program.
   ✅ False — it's a bundle of separate projects (binutils, GCC, a C
   library, GDB) built to work together.

2. In `riscv32-unknown-elf-gcc`, what does the `elf` component
   indicate?
   ✅ The target ABI/output format is bare-metal ELF (no OS), as
   opposed to e.g. `linux-gnu`.

3. What are the four main stages a C file passes through to become an
   executable?
   ✅ Preprocessing → compiling → assembling → linking.

4. True or False: `-E` produces assembly output.
   ✅ False — `-E` stops after preprocessing and shows the expanded
   source; `-S` produces assembly.

5. What does the MEMORY command in a linker script define?
   ✅ The available memory regions (e.g. flash, RAM) with their origin
   address and length.

6. True or False: VMA and LMA are always the same address for every
   section.
   ✅ False — for sections like .data, LMA (load address, e.g. in
   flash) can differ from VMA (execution address, e.g. in RAM), since
   it's copied from one to the other at startup.

7. What is a relocation in an object file, in one sentence?
   ✅ A record telling the linker to patch in a real address at a
   specific location once final addresses are known.

8. True or False: sections and segments in ELF mean the same thing.
   ✅ False — sections are the linking-time view (used by the linker/
   compiler tools); segments are the loading-time view (used by a
   loader to map memory).

9. Why can't a `.elf` file usually be flashed directly to a
   microcontroller?
   ✅ ELF contains extra metadata (symbol tables, debug info, section
   headers) not needed by the hardware; typically converted to raw
   binary or HEX first.

10. What does the `_sbrk` syscall stub in newlib need to provide?
    ✅ Heap memory management support (used by malloc) — since there's
    no OS to provide it, the platform must implement it.

11. True or False: printf on a bare-metal target works automatically
    with no extra setup.
    ✅ False — it requires retargeting via syscall stubs (e.g. `_write`)
    to actually send output somewhere (like a UART).

12. What is OpenOCD's role in a debugging setup?
    ✅ Bridges GDB to the physical JTAG/SWD debug interface on real
    hardware, since GDB itself can't speak those hardware protocols.

13. True or False: hardware and software breakpoints are functionally
    interchangeable with no practical limits.
    ✅ False — hardware breakpoints are limited in number (implemented
    in silicon) but work on any memory including flash; software
    breakpoints (code patching) are more plentiful but need writable
    memory.

14. What does `-Os` optimize for, specifically?
    ✅ Code size, rather than pure execution speed — important for
    flash-constrained embedded targets.

15. True or False: object files compiled with different `-march`/`-mabi`
    settings can always be linked together safely.
    ✅ False — mismatched ABI/arch object files can fail to link or
    produce incorrect behavior; multilib exists specifically to avoid
    this by providing matching library variants.

16. What does a linker "region overflowed" error actually mean?
    ✅ The combined size of sections assigned to a memory region
    exceeds that region's defined length in the linker script.

17. True or False: `-g` (debug info) changes the actual machine
    instructions generated.
    ✅ False — it adds debug metadata (symbol/line info) without
    changing the generated code's behavior (though it may affect
    binary size).

18. What does Link-Time Optimization (LTO) allow the compiler to do
    that it normally can't?
    ✅ Optimize across translation unit (file) boundaries, since
    normally each file is optimized independently before linking.

---

## Part 2 — Applied Round

1. A linker error says `undefined reference to '_write'`. Explain what
   this means and how you'd fix it.
   ✅ Some code (likely printf via newlib) calls `_write` but no
   implementation exists in the linked object files; fix by
   implementing `_write` (typically to send bytes over UART).

2. A build fails with `region 'RAM' overflowed by 512 bytes`. Walk
   through exactly what you'd check first, and in what order.
   ✅ Check the linker script's MEMORY definition for RAM's declared
   size, check the map file to see what's actually consuming RAM
   (large buffers/arrays, stack/heap reservation), then either reduce
   usage or reconsider memory layout — not just increase the declared
   size blindly if it doesn't match real hardware.

3. Given a `.data` section with differing LMA and VMA, explain in your
   own words why this is necessary and what code must run to make it
   correct at runtime.
   ✅ Initialized data must be stored in non-volatile flash (LMA) but
   execute from RAM (VMA) since flash is often slower/not always
   writable; startup code must copy .data from LMA to VMA before main()
   runs.

4. You compile with `-O0` and your bug disappears; compiling with
   `-O2` it reappears. What does this strongly suggest, and what
   earlier-track concept does it likely connect to?
   ✅ Strongly suggests a missing `volatile` or other undefined-
   behavior-dependent code the optimizer is now legally allowed to
   change — directly connects to Track 01's volatile/UB discussion.

5. Explain, step by step, what happens when you run a `printf("hi\n")`
   call all the way down to bytes appearing on a UART, on a newlib-based
   bare-metal system.
   ✅ printf formats the string → internally calls a lower-level write
   routine → which calls the newlib `_write` syscall stub → the
   platform's `_write` implementation writes each byte to the UART's
   transmit data register (with volatile access), typically polling a
   status register bit until ready.

---

## Part 3 — Practical Proof

**Task 1 — Manual pipeline**
Given a provided simple C file, manually run each of the four
compilation stages separately using the correct individual gcc/
binutils invocations, and briefly describe what changed in the output
at each stage.

**Task 2 — Linker script from scratch**
Given a provided memory map (flash origin/size, RAM origin/size),
write a complete, correct linker script placing .text/.rodata in
flash and .data/.bss in RAM with correct LMA/VMA handling.

**Task 3 — ELF inspection**
Given a provided compiled ELF binary, use readelf and objdump to
report: entry point address, number of sections, and the address of a
named symbol provided in the task.

**Task 4 — Syscall retargeting**
Given a provided minimal newlib-based project with UART driver code
already written, implement `_write` to correctly retarget printf to
the UART.

**Task 5 — Debug session**
Given a provided binary and a simulated or real target setup, launch
OpenOCD + GDB, set a breakpoint at a specified function, run to it,
and report the value of a specified memory-mapped register at that point.

**Task 6 — Diagnose from a map file**
Given a provided linker map file, identify which single symbol/object
is consuming the most space in a specified section.

---

## Completion state
Shown as: "Forge Check — Track 03: Cleared ✓" once all three parts
are attempted.
