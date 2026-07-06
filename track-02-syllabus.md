# Track 02 — RISC-V ISA Deep Dive

## Status: DRAFT v2 — for team review
## Standard: Builds on Track 01. Industry depth throughout.

## Why this track exists
This is where "I can write C" becomes "I know exactly what instructions
my C compiles to, why the ISA is shaped the way it is, and what every
bit in an instruction word means." Track 04 (RTL design) and Track 06
(building your own core) are only meaningful once this is second nature.

---

## Module 1 — Why RISC-V, and ISA Design Philosophy
1.1 CISC vs. RISC — the actual tradeoffs, not just "RISC is simpler"
1.2 Why RISC-V specifically: open ISA, modular extensions, no licensing
    fees, industry/academic adoption reasons
1.3 The modular extension model: base ISA + optional extensions — why
    this lets tiny microcontrollers and large application processors
    share one ISA
1.4 Reading the official RISC-V specification — structure, versioning,
    unprivileged vs. privileged spec documents, how to find authoritative
    answers instead of relying on tutorials
1.5 The RISC-V ecosystem landscape: who builds real silicon on it
    (SiFive, and others), how vendors differentiate while staying
    ISA-compliant

## Module 2 — Base Integer ISA (RV32I)
2.1 The 32 integer registers: x0–x31, and why x0 is hardwired to zero
2.2 The standard ABI register names (ra, sp, gp, tp, a0–a7, s0–s11,
    t0–t6) and the calling convention each implies
2.3 Program counter (PC) behavior — instruction fetch and update
2.4 RV32E and reduced register-set variants — why they exist

## Module 3 — Instruction Formats & Encoding
3.1 The six base formats: R, I, S, B, U, J
3.2 Bit-level encoding of each format, decoded by hand
3.3 Immediate encoding oddities: why B-type and J-type immediates are
    bit-scrambled (hardware decode efficiency, not arbitrary)
3.4 Sign extension of immediates — where and why
3.5 Manually decoding a raw 32-bit hex instruction to mnemonic + operands

## Module 4 — Instruction Categories, Fully Enumerated
4.1 Arithmetic/logic: ADD, SUB, AND, OR, XOR, SLT, SLTU, shifts and
    their immediate variants
4.2 Load/store: LB, LH, LW, LBU, LHU, SB, SH, SW — signed vs. unsigned
    loads with a real example of when it matters
4.3 Control flow: BEQ, BNE, BLT, BGE, BLTU, BGEU, JAL, JALR
4.4 LUI and AUIPC — building 32-bit constants and PC-relative addresses
4.5 FENCE and memory ordering basics
4.6 ECALL/EBREAK — the software interrupt/breakpoint mechanism

## Module 5 — Pseudo-Instructions & What the Assembler Actually Does
5.1 What a pseudo-instruction is: `li`, `mv`, `nop`, `ret`, `call`,
    `j`, `not`, `neg` — and the real instruction(s) each expands to
5.2 Why pseudo-instructions exist: readability without adding real
    opcodes to the hardware
5.3 Reading assembler-expanded output to see the real instructions
    behind common pseudo-ops — removes the "magic" from assembly code

## Module 6 — The M Extension (Integer Multiply/Divide)
6.1 MUL, MULH, MULHU, MULHSU — why multiple multiply variants exist
6.2 DIV, DIVU, REM, REMU, and RISC-V's defined divide-by-zero behavior

## Module 7 — The C Extension (Compressed Instructions)
7.1 Why compressed instructions exist — code density
7.2 16-bit instruction encoding basics, compressed vs. standard decode
7.3 Which common instructions have compressed forms, and why those

## Module 8 — The A, F, and D Extensions
8.1 A (atomics): load-reserved/store-conditional, atomic memory ops,
    why needed for multi-core synchronization
8.2 F and D (floating point): register file, instruction overview,
    connecting back to Track 01's software-vs-hardware-FPU discussion
8.3 When a real embedded core would/wouldn't include these (area/power
    tradeoffs feeding into Track 06 design decisions)

## Module 9 — Newer & Specialized Extensions (Awareness Level)
9.1 Bitmanip (B) extension: what it adds and why (bit manipulation
    without long instruction sequences)
9.2 Vector (V) extension: the core idea of vector registers and
    data-parallel operations, at a conceptual level — full depth not
    required for this project's core, but industry-relevant awareness
9.3 Custom/vendor-specific extensions: how RISC-V formally reserves
    opcode space for custom instructions, and why this matters for
    real commercial chips (relevant context for Track 06 if the team
    ever adds custom instructions to their own core)

## Module 10 — Privilege Levels & Modes
10.1 Machine (M), Supervisor (S), and User (U) mode — purpose of each
10.2 Why most small embedded RISC-V cores implement M-mode only
10.3 Privilege transitions: how and why mode changes happen

## Module 11 — Physical Memory Protection (PMP)
11.1 What PMP is and the real-world problem it solves — restricting
     which memory regions code can access, even without a full MMU
11.2 PMP configuration registers and address-matching modes
     (TOR/NA4/NAPOT) explained with worked examples
11.3 Why PMP matters specifically for embedded security (isolating a
     bootloader from application code, sandboxing untrusted code)

## Module 12 — Control and Status Registers (CSRs)
12.1 What a CSR actually is and how it differs from a GPR
12.2 CSR instructions: CSRRW, CSRRS, CSRRC and immediate variants
12.3 Key CSRs in depth: mstatus, mtvec, mepc, mcause, mie, mip, mtval
     — what each field actually controls
12.4 Reading/writing CSRs safely — atomicity guarantees

## Module 13 — Exceptions, Traps & Interrupts
13.1 Exceptions (synchronous) vs. interrupts (asynchronous)
13.2 The trap-handling sequence: hardware-automatic steps vs.
     software-required steps
13.3 mcause encoding — decoding exactly which exception/interrupt
13.4 Writing and tracing a minimal trap handler
13.5 Nested traps and careful handling (ties to Track 01 interrupt
     priority discussion, now at ISA level)

## Module 14 — Interrupt Controllers: CLINT & PLIC
14.1 Why the base ISA alone doesn't handle multiple external interrupt
     sources — the need for a standard interrupt controller
14.2 CLINT (Core-Local Interruptor): software interrupts, timer
     interrupts, mtime/mtimecmp explained concretely
14.3 PLIC (Platform-Level Interrupt Controller): priority, claim/
     complete protocol, how multiple peripheral interrupts get routed
     to a hart — directly relevant once Track 08 peripherals need
     real interrupt wiring

## Module 15 — Memory Model & Ordering
15.1 RISC-V's memory consistency model basics — what "weak ordering"
     permits and why it matters for multi-core/DMA
15.2 FENCE instruction variants and their ordering guarantees
15.3 Why this matters even on single-core systems (DMA, memory-mapped
     peripherals reordering)

## Module 16 — Comparing RISC-V Variants (Practical Decision-Making)
16.1 RV32I vs. RV32E vs. RV64I — full practical comparison table
16.2 Common real combinations (RV32IMC for microcontrollers, RV64GC
     for application processors) and why those specific combinations
16.3 Reading a real RISC-V vendor datasheet and identifying exactly
     which ISA variant/extensions it implements

## Module 17 — Assembly Programming, Properly
17.1 RISC-V assembler directives and GNU assembler syntax
17.2 Writing, assembling, and running real assembly programs
17.3 Function calls in assembly: prologue/epilogue, stack frame setup,
     manually following the ABI (ties to Track 01 Module 4.4)
17.4 Reading compiler-generated assembly (`-S` flag) and explaining
     exactly what each line does and why the compiler chose it

## Module 18 — ISA-Level Simulation Before Silicon Exists
18.1 Why software teams need to run code before RTL/hardware exists —
     the role of an ISA simulator
18.2 Spike (the RISC-V reference simulator): running a binary, what
     "golden reference" means and why it matters for later verification
     (directly feeds Track 05)
18.3 QEMU for RISC-V: emulation vs. simulation distinction, when each
     tool is the right choice

## Module 19 — ISA Compliance & Conformance
19.1 Why "RV32IMC" being claimed isn't automatically true — the need
     for conformance testing
19.2 Introduction to riscv-arch-test / compliance test suites, and how
     a real core (including the team's own, later in Track 06) gets
     validated against the spec

## Module 20 — Reading Real Disassembly & Debugging at the ISA Level
20.1 Using objdump to disassemble a compiled binary
20.2 Correlating C source lines to generated instructions
20.3 Debugging at the instruction level in GDB: single-stepping
     instructions, inspecting registers, hardware breakpoints
20.4 Diagnosing a real bug (stack corruption, wrong ABI usage) purely
     from register state and disassembly — no source available

## Capstone Checkpoint for Track 02
A learner should be able to, unaided:
- Manually decode a raw 32-bit RISC-V instruction into its full
  assembly mnemonic and operands
- Explain the calling convention well enough to hand-trace a function
  call/return sequence in assembly
- Explain what a given pseudo-instruction actually expands to
- Configure a PMP region conceptually and explain what it protects
- Identify which CSRs are involved in a trap and explain the full
  hardware-then-software sequence
- Explain how PLIC/CLINT route an external interrupt to a trap handler
- Justify why a specific extension combination would be chosen for a
  given embedded use case
- Run a binary on Spike and explain why this matters before RTL exists
- Read raw compiler-generated assembly and explain it line by line
- Debug a provided binary at the instruction level using GDB and
  objdump with no source code available
