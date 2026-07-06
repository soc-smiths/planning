# Track 02 — Forge Check

## Status: DRAFT v2 — expanded to match syllabus v2

---

## Part 1 — Recall Round

1. Why is register x0 hardwired to zero in RISC-V?
   ✅ Simplifies instruction encoding and hardware (e.g. NOP, MOV via
   ADD with x0, comparisons) without needing special-case instructions.

2. True or False: RV32I and RV32E use the same number of general-
   purpose registers.
   ✅ False — RV32E has 16 registers (x0–x15), RV32I has 32.

3. In the R-type instruction format, which fields together determine
   the exact operation performed?
   A) opcode only  B) opcode + funct3  C) opcode + funct3 + funct7
   D) rd + rs1
   ✅ C

4. True or False: B-type (branch) immediates are encoded as a simple
   contiguous bit field, same as I-type.
   ✅ False — B-type immediate bits are scrambled/non-contiguous in
   the encoding for hardware decode efficiency.

5. What does JALR do, and which register typically holds the address
   used for a function return?
   ✅ Jumps to a register+offset address and saves the return address;
   `ra` (x1) typically holds the return address.

6. True or False: RISC-V traps on integer division by zero.
   ✅ False — DIV/DIVU by zero returns a defined result (all 1s) rather
   than trapping.

7. What problem does the C (compressed) extension primarily solve?
   ✅ Code density — reduces instruction-fetch bandwidth and program
   size using 16-bit encodings for common instructions.

8. True or False: `li a0, 100` is a real RISC-V hardware instruction.
   ✅ False — it's a pseudo-instruction; the assembler expands it to
   a real instruction such as ADDI (or LUI+ADDI for larger constants).

9. What is a CSR, and how does CSRRW differ from a normal register
   write?
   ✅ Control and Status Register — special-purpose register accessed
   via dedicated CSR instructions; CSRRW atomically swaps a value in
   while returning the old value, unlike an ordinary register write.

10. Which CSR holds the cause of the most recent trap?
    ✅ mcause

11. True or False: exceptions and interrupts are handled identically
    because both use the same trap mechanism.
    ✅ False — mechanism is similar, but exceptions are synchronous
    (caused by the current instruction) while interrupts are
    asynchronous (external events); mcause distinguishes them.

12. What is PMP, and what real problem does it solve on a core with
    no full MMU?
    ✅ Physical Memory Protection — restricts which memory regions
    code can access, enabling isolation (e.g. bootloader vs.
    application) without full virtual memory hardware.

13. What is the difference in role between CLINT and PLIC?
    ✅ CLINT handles software and timer interrupts local to a hart;
    PLIC routes and prioritizes multiple external peripheral
    interrupts to one or more harts.

14. True or False: RV32IMC is a common choice for microcontroller-class
    cores.
    ✅ True — integer base + multiply/divide + compressed instructions,
    a practical balance for small embedded cores.

15. What is Spike, and why is it used before RTL/silicon exists?
    ✅ The RISC-V reference ISA simulator — runs binaries against the
    spec as a "golden reference" so software/verification work can
    proceed before real hardware is available.

16. True or False: claiming a core implements "RV32IMC" is automatically
    true as long as the RTL was written with that intent.
    ✅ False — conformance must actually be verified, typically using
    compliance/architecture test suites (e.g. riscv-arch-test).

17. What's the difference in what ends up in the destination register
    between LW and LB?
    ✅ LW loads a full 32-bit word; LB loads a single byte and sign-
    extends it to fill the register.

18. True or False: `objdump` can be used to disassemble a compiled
    binary back into assembly instructions.
    ✅ True.

19. What does the Vector (V) extension conceptually add to the ISA,
    at a high level?
    ✅ Vector registers and instructions that operate on multiple data
    elements per instruction (data-parallel processing).

20. Why does RISC-V formally reserve opcode space for custom
    instructions rather than leaving it undefined?
    ✅ Lets vendors add proprietary/custom instructions in a
    standardized, non-conflicting way while remaining compliant with
    the base ISA elsewhere.

---

## Part 2 — Applied Round

1. Given the raw instruction hex `0x00A50533`, walk through identifying
   the opcode field and explain the general process you'd use to fully
   decode it (exact mnemonic not required — process must be correct).
   ✅ Extract bits [6:0] as opcode, use it to determine format (R-type
   here based on opcode value), then extract funct3/funct7/rd/rs1/rs2
   fields per the R-type layout and cross-reference the ISA manual's
   opcode table.

2. A function's disassembly shows `sw ra, 0(sp)` near the start and
   `lw ra, 0(sp)` near the end, with `addi sp, sp, -16` / `addi sp,
   sp, 16` bracketing them. Explain what's happening and why.
   ✅ Standard prologue/epilogue: saving the return address to the
   stack (since the function calls another function and would
   otherwise clobber ra) and allocating/deallocating stack space,
   restoring ra before returning.

3. A trap handler reads mcause and gets a value indicating an
   "illegal instruction" exception. What are two realistic causes of
   this in a real system?
   ✅ e.g. Program counter jumped to invalid/corrupted memory (executing
   data as instructions), or code compiled for an extension not
   implemented by the actual core (e.g. using M-extension instructions
   on a core without it).

4. Why would a chip designer choose RV32E over RV32I for a specific
   product, and what's the real cost of that choice?
   ✅ Chosen for area/register-file size savings in extremely
   constrained embedded targets; cost is fewer registers available,
   more spilling to memory, potentially more instructions/less performance.

5. Explain why FENCE instructions matter even on a single-core system
   with no other CPU, using a concrete example involving a peripheral.
   ✅ E.g. writing a DMA descriptor then triggering DMA start — without
   ordering guarantees, the write to memory might not be visible to
   the DMA engine yet when the trigger write occurs; FENCE ensures
   ordering.

6. Two peripherals (a UART and a timer) both need to interrupt the CPU.
   Explain, at a high level, the path an interrupt takes from either
   peripheral asserting its interrupt line to the CPU's trap handler
   running.
   ✅ Peripheral asserts interrupt → PLIC receives and prioritizes it
   among pending peripheral interrupts → PLIC signals the hart's
   external interrupt line → CPU traps, mcause indicates external
   interrupt → handler queries PLIC (claim) to find which source fired
   → handles it → signals PLIC complete.

7. Why would a team run their own RISC-V core's test programs on Spike
   before running them on their actual RTL simulation?
   ✅ Spike gives a fast, trusted "golden" reference of correct
   behavior; comparing the core's RTL execution trace against Spike's
   output helps isolate whether a bug is in the software/test or in
   the core's hardware implementation.

8. A team claims their custom core implements RV32IMC. What would you
   actually do to verify that claim rather than just trusting the RTL
   author's intent?
   ✅ Run the core against a compliance/architecture test suite (e.g.
   riscv-arch-test) covering the I, M, and C extensions and confirm
   all tests pass.

---

## Part 3 — Practical Proof

**Task 1 — Manual instruction decode**
Given three provided raw 32-bit hex instructions, manually decode each
to its full assembly mnemonic and operands, showing the field
extraction work (not just the final answer).

**Task 2 — Assembly from scratch**
Write a RISC-V assembly function (no C, no compiler) that computes the
sum of an array of integers, correctly following the standard calling
convention (proper prologue/epilogue, correct argument/return registers).

**Task 3 — Trap handler trace**
Given a provided minimal trap handler and a triggering scenario (in
`/quizzes/track-02-trap`), trace exactly which CSRs are read/written
and in what order, and identify the trap cause from the provided
mcause value.

**Task 4 — Disassembly detective work**
Given a provided stripped binary and GDB register dump at a crash
point (no source code), identify the likely bug class (e.g. stack
corruption, bad jump target, ABI violation) using only objdump output
and register state.

**Task 5 — Run it on Spike**
Given a provided small RISC-V binary, run it under Spike, capture the
execution trace/output, and explain what "matching the golden
reference" would mean if this were later compared against RTL simulation.

**Task 6 — PMP reasoning**
Given a provided memory map (bootloader region + application region),
describe the PMP configuration (address range + permissions) that
would prevent application code from writing into the bootloader region.

---

## Completion state
Shown as: "Forge Check — Track 02: Cleared ✓" once all three parts
are attempted.
