# Track 01 — Forge Check

## Status: DRAFT v2 — expanded to match syllabus v2

---

## Part 1 — Recall Round

1. What does `volatile` actually prevent the compiler from doing?
   ✅ Caching a variable's value in a register/optimizing away repeated
   reads/writes — forces the compiler to access memory every time.

2. True or False: On a microcontroller with no OS, something still
   calls main() automatically.
   ✅ True — startup/crt0 code calls it after initializing memory.

3. Which memory section holds initialized global variables?
   A) .bss  B) .data  C) .text  D) .rodata
   ✅ B

4. What is the two's complement representation of -1 in an 8-bit
   signed integer?
   ✅ 11111111

5. True or False: `int *p` and `int p[]` behave identically in every
   context in C.
   ✅ False — they decay similarly in many contexts but aren't identical
   (e.g. sizeof behaves differently).

6. Why do embedded bare-metal programs typically avoid malloc/free?
   ✅ Fragmentation risk, non-determinism, no OS to reclaim memory on
   failure.

7. What does startup code do to .bss before main() runs?
   ✅ Zeroes it out.

8. True or False: reading an uninitialized local variable always
   gives 0.
   ✅ False — undefined behavior, value is indeterminate.

9. What does treating a hardware register as a memory-mapped struct
   member actually mean?
   ✅ Reading/writing it directly reads/writes the physical register at
   that memory address, not ordinary RAM.

10. Which bitwise expression clears bit 3 of `reg`?
    ✅ `reg &= ~(1 << 3);`

11. True or False: an ISR should generally do as much work as possible
    before returning.
    ✅ False — ISRs should be short; defer heavy work to main context.

12. What is a race condition between main code and an ISR, in one
    sentence?
    ✅ Shared data accessed by both without protection, causing
    inconsistent results depending on timing.

13. Why can debug and release builds of the same code behave
    differently?
    ✅ Optimizations in release builds can expose bugs (e.g. missing
    volatile) that debug builds hide by chance.

14. True or False: struct members always lay out in memory with no
    gaps.
    ✅ False — padding/alignment can insert gaps.

15. What tool would you use to step through code and inspect memory/
    registers after a crash?
    ✅ GDB

16. What is endianness, and why does it matter when reading a
    multi-byte register or network packet?
    ✅ The byte order used to store multi-byte values (big vs. little);
    misreading it produces completely wrong values even though each
    byte is correct.

17. True or False: relying on the exact bit order of a C bit-field is
    guaranteed to be portable across all compilers.
    ✅ False — bit-field layout is implementation-defined; relying on
    exact order is a portability risk.

18. What is MISRA-C, in one sentence, and why does embedded industry
    reference it so often?
    ✅ A set of coding guidelines for safety-critical C, widely used/
    required in automotive, aerospace, and medical firmware.

19. Why might a project choose fixed-point arithmetic instead of
    floating point on a small microcontroller?
    ✅ No hardware FPU means software floating point is slow; fixed-
    point gives predictable, faster math using integer operations.

20. What does a watchdog timer do, and what happens if firmware fails
    to "pet" (reset) it in time?
    ✅ It resets the system if not periodically acknowledged — protects
    against firmware hangs; failing to pet it triggers a system reset.

21. True or False: holding interrupts disabled for a long critical
    section has no real cost.
    ✅ False — it delays response to other (possibly urgent) interrupts,
    risking missed events or timing violations.

22. Why is embedded code typically structured with a hardware
    abstraction layer (HAL) specifically to support testing?
    ✅ It lets hardware register access be mocked/substituted in
    software, so logic can be unit-tested off-target without real hardware.

---

## Part 2 — Applied Round

1. This code toggles an LED but doesn't work as expected on real
   hardware, only in simulation:
   ```c
   while (*status_reg == 0) { }
   What's likely missing, and why does it matter here specifically?
✅ status_reg should be volatile — without it, the compiler may
cache the read and optimize the loop into an infinite loop that
never re-reads the actual hardware value.
Given a register at 0x40000000 with bit 5 meaning "enable," write
the C statement to set that bit without disturbing others, assuming
reg points to that address.
✅ *reg |= (1 << 5);
A learner writes heavy data-processing logic directly inside an ISR
and complains the system "randomly freezes." What's the design
mistake, and how should it be restructured?
✅ ISR doing too much work, blocking other interrupts/timing; should
set a flag or queue data and let main-loop code process it.
Explain, in your own words, the full sequence from power-on to the
first line of main() executing.
✅ Reset vector triggers startup code → .data copied flash→RAM →
.bss zeroed → stack pointer initialized → main() called.
Why does sizeof(struct) sometimes exceed the sum of its members'
individual sizes?
✅ Padding inserted by the compiler for alignment requirements.
A function reads a 4-byte sensor value over SPI and gets a value
1000x too large. The datasheet says the sensor sends big-endian
data. What's the likely bug?
✅ The code is interpreting the bytes as little-endian (the host's
native order) without converting — byte order mismatch.
A static analyzer flags a line as "possible use of uninitialized
variable." The code compiles and "usually works." Should this be
ignored? Why or why not?
✅ No — undefined behavior can appear to work by chance depending on
stack garbage/optimization; it must be fixed, not dismissed because
it currently runs.
Firmware occasionally resets itself in the field with no crash log.
What two hardware-level mechanisms would you check first, and why?
✅ Watchdog timeout (firmware hung and wasn't petted) and brown-out
detection (unstable power) — both cause silent resets without a
software-visible crash.
Part 3 — Practical Proof
Task 1 — Bit manipulation
Write C functions to set, clear, toggle, and check any given bit of a
32-bit unsigned integer, without library functions.
Task 2 — Register struct
Given a provided hypothetical peripheral with CTRL, STATUS, and DATA
registers at known offsets, write a correct C struct for this memory
map, and one function that enables the peripheral using it.
Task 3 — Trace a crash
Given a provided minimal bare-metal binary and GDB session output
(in /quizzes/track-01-crash), identify the faulting line and explain
why, using the backtrace.
Task 4 — Static analysis
Run cppcheck (or clang-tidy) on a provided deliberately-flawed C file
(in /quizzes/track-01-lint), list every real finding, and fix them.
Task 5 — Endianness
Given a 4-byte big-endian value read from a provided byte array, write
a C function that correctly reconstructs it as a native integer on a
little-endian host.
Completion state
Shown as: "Forge Check — Track 01: Cleared ✓" once all three parts
are attempted.
