# Track 02 — RISC-V ISA Deep Dive

## Status: DRAFT v1 — for team review
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
1.3 The modular extension model: base ISA + optional extensions (I, M,
    A, F, D, C, and others) — why this design choice matters for both
    tiny microcontrollers and large application processors sharing one ISA
1.4 Reading the official RISC-V specification — structure, versioning,
    how to find authoritative answers instead of relying on tutorials

## Module 2 — Base Integer ISA (RV32I)
2.1 The 32 integer registers: x0–x31, and why x0 is hardwired to zero
2.2 The standard ABI register names (ra, sp, gp, tp, a0–a7, s0–s11,
    t0–t6) and the calling convention each implies
2.3 Program counter (PC) behavior — instruction fetch and update
2.4 RV32E and reduced register-set variants — why they exist
    (embedded/area-constrained cores)

## Module 3 — Instruction Formats & Encoding
3.1 The six base formats: R, I, S, B, U, J — what each is used for
3.2 Bit-level encoding of each format: opcode, funct3, funct7, rd, rs1,
    rs2, immediate fields — decoded by hand, not just described
3.3 Immediate encoding oddities explained properly: why B-type and
    J-type immediates are bit-scrambled in the encoding (efficiency of
    hardware decode, not arbitrary)
3.4 Sign extension of immediates — where it happens and why it matters
3.5 Practical exercise mindset: given a raw 32-bit hex instruction,
    manually decode it to its assembly mnemonic and operands

## Module 4 — Instruction Categories, Fully Enumerated
4.1 Arithmetic/logic: ADD, SUB, AND, OR, XOR, SLT, SLTU, shifts (SLL,
    SRL, SRA) and their immediate variants
4.2 Load/store: LB, LH, LW, LBU, LHU, SB, SH, SW — signed vs. unsigned
    loads explained with a real example of when it matters
4.3 Control flow: BEQ, BNE, BLT, BGE, BLTU, BGEU, JAL, JALR — how
    function calls and returns are actually implemented with these
4.4 LUI and AUIPC — building 32-bit constants and PC-relative addresses
    from two instructions, explained with the actual encoding reason
4.5 FENCE and memory ordering basics (introductory; deepened in
    multi-core/verification context later if relevant)
4.6 ECALL/EBREAK — the software interrupt/breakpoint mechanism

## Module 5 — The M Extension (Integer Multiply/Divide)
5.1 MUL, MULH, MULHU, MULHSU — why there are multiple multiply variants
    (full 64-bit results from 32-bit operands, signed/unsigned combos)
5.2 DIV, DIVU, REM, REMU, and RISC-V's defined behavior on divide-by-zero
    (deliberately different from a trap — explained with the reasoning)

## Module 6 — The C Extension (Compressed Instructions)
6.1 Why compressed instructions exist — code density, especially for
    embedded/instruction-fetch-bandwidth-constrained systems
6.2 16-bit instruction encoding basics, and how a decoder distinguishes
    compressed vs. standard instructions
6.3 Which common instructions have compressed forms and why those specifically

## Module 7 — The A, F, and D Extensions (Overview + When Relevant)
7.1 A (atomics): load-reserved/store-conditional, atomic memory
    operations — why they're needed for multi-core synchronization
7.2 F and D (single/double-precision floating point): register file,
    instruction overview, and the connection back to Track 01's
    software-vs-hardware-FPU discussion
7.3 When a real embedded core would vs. wouldn't include these
    (area/power tradeoffs — ties to Track 06 core design decisions)

## Module 8 — Privilege Levels & Modes
8.1 Machine (M), Supervisor (S), and User (U) mode — what each is for
    and why the separation exists
8.2 Why most small embedded RISC-V cores implement M-mode only, and
    what functionality is lost by skipping S/U
8.3 Privilege transitions: how and why mode changes happen

## Module 9 — Control and Status Registers (CSRs)
9.1 What a CSR actually is and how it differs from a general-purpose register
9.2 CSR instructions: CSRRW, CSRRS, CSRRC and their immediate variants
9.3 Key CSRs in depth: mstatus, mtvec, mepc, mcause, mie, mip — what
    each field actually controls, not just the name
9.4 Reading/writing CSRs safely — atomicity guarantees and why they matter

## Module 10 — Exceptions, Traps & Interrupts
10.1 The distinction between exceptions (synchronous, caused by the
     instruction itself) and interrupts (asynchronous, external)
10.2 The trap-handling sequence step by step: what hardware does
     automatically vs. what software must do
10.3 mcause encoding — decoding exactly which exception/interrupt occurred
10.4 Writing a minimal trap handler and tracing execution through it
10.5 Nested traps and why they're handled carefully (ties back to
     Track 01's interrupt-priority discussion, now at the ISA level)

## Module 11 — Memory Model & Ordering
11.1 RISC-V's memory consistency model basics — what "weak ordering"
     actually permits and why it matters for multi-core/DMA scenarios
11.2 FENCE instruction variants and what ordering guarantee each provides
11.3 Why this matters even on a single-core embedded system (DMA,
     memory-mapped peripherals reordering)

## Module 12 — Comparing RISC-V Variants (Practical Decision-Making)
12.1 RV32I vs. RV32E vs. RV64I — a full practical comparison table:
     register count, address space, typical use case for each
12.2 Common extension combinations seen in real chips (RV32IMC for
     microcontrollers, RV64GC for application processors) and why
     those specific combinations are chosen
12.3 Reading a real RISC-V vendor datasheet and identifying exactly
     which ISA variant/extensions it implements

## Module 13 — Assembly Programming, Properly
13.1 RISC-V assembler directives and syntax (GNU assembler conventions)
13.2 Writing, assembling, and running real assembly programs — not just
     reading disassembly
13.3 Function calls in assembly: prologue/epilogue, stack frame setup,
     following the ABI manually (direct callback to Track 01 Module 4.4)
13.4 Reading compiler-generated assembly output (`-S` flag) and
     explaining exactly what each line does and why the compiler chose it

## Module 14 — Reading Real Disassembly & Debugging at the ISA Level
14.1 Using objdump to disassemble a compiled binary
14.2 Correlating C source lines to generated instructions
14.3 Debugging at the instruction level in GDB: stepping single
     instructions, inspecting registers directly, setting hardware
     breakpoints
14.4 Diagnosing a real bug (e.g. stack corruption, wrong ABI usage)
     purely from register state and disassembly — no source available

## Capstone Checkpoint for Track 02
A learner should be able to, unaided:
- Manually decode a raw 32-bit RISC-V instruction (hex) into its full
  assembly mnemonic and operands
- Explain the calling convention well enough to hand-trace a function
  call/return sequence in assembly
- Identify which CSRs are involved in a trap and explain the full
  hardware-then-software sequence when one occurs
- Justify why a specific extension combination (e.g. RV32IMC) would be
  chosen for a given embedded use case
- Read raw compiler-generated assembly and explain it line by line
- Debug a provided binary at the instruction level using GDB and
  objdump with no source code available
